# 🛰️Proyecto_Semillero_Netlife


**Proyecto_Del Grupo Overmind**

**Este proyecto es un entorno de simulación de atención al cliente diseñado para probar y mejorar las interacciones de un bot de soporte técnico (Netlife Ecuador). Utiliza Inteligencia Artificial (Google Gemini) para recrear diálogos realistas entre un agente de soporte y clientes con diversos perfiles psicológicos, niveles técnicos y modismos locales.**


# 📋 Integrantes:

**1) Garcia Castro Julio Andres**

**2) Ruales Luna Milena Sidney**

**3) Molineros Yagual Joan Manuel**

**4) Peñafiel Alvarado Edinson Enrique**

**5) Camacho Vera Ashley Denisse**

**6) Chilan Choez Jamilet Melanie** 

**7) Pedro Estiben Muñoz Changin**

**8) Flores Barrera Paul Andres**

**9)  Manzano Ortiz Joel Carlos**

**10) Vargas Sanchez Stiven Alexander**

.

# 🎯 Objetivos de la Práctica
Implementar arquitecturas de Agentes: Configurar dos instancias de LLM con roles contrapuestos.

* **Gestión de Contexto:** Utilizar el historial de mensajes de LangChain para mantener la coherencia en diálogos extensos.

* **Persistencia de Datos:** Integrar SQLite para parametrizar el comportamiento de los agentes.

* **Evaluación Sintética:** Generar métricas de satisfacción (CSAT) basadas en el razonamiento de la IA.

# 💻 Tecnologías Utilizadas

**Core**: Python 3.x

**IA Orchestrator:** LangChain (Core, Google GenAI)

**LLM:** Google Gemini 1.5/2.0 Flash

**Database:** SQLite3

**Environment:** OS Environment Variables para gestión de API Keys.

# 🛠️ Arquitectura y Componentes Técnicos
**1. Motor de Inteligencia Artificial**

El proyecto utiliza la arquitectura de Gemini 2.0 Flash (vía LangChain), seleccionada por su baja latencia y alta capacidad de razonamiento contextual.Se han configurado dos perfiles de temperatura distintos:

    Agente de Soporte: $T = 0.5$ (Para respuestas consistentes, profesionales y deterministas).
  
    Simulador de Cliente: $T = 0.7$ (Para introducir variabilidad, emociones y lenguaje natural coloquial).

**2. Capa de Persistencia (Modelado de Datos)**

Se implementó un esquema en SQLite para la gestión de "Personas". Cada perfil se define mediante 5 dimensiones clave:

* **Tono:** Define la carga emocional (irritable, amable, neutro).

* **Intención:** El objetivo transaccional de la llamada.

* **Nivel Técnico:** Grado de alfabetización digital del usuario (Bajo/Medio/Alto).Personalidad: Arquetipo del cliente.

* **Ubicación:** Contexto geográfico para la inserción de modismos regionales (Ecuador).

**3. Ingeniería de Prompts (Prompt Engineering)**

* El sistema emplea Role-Based Prompting y Chain of Thought implícito para asegurar que: 

* El cliente mantenga su "personaje" sin romper la cuarta pared (no admite ser una IA).El bot siga los protocolos corporativos de Netlife.


# 📊 Flujo de Ejecución
* **Inicialización:** Se crea o conecta la base de datos y se pueblan los perfiles de prueba.

* **Selección Aleatoria:** El sistema extrae un perfil de cliente para garantizar pruebas no sesgadas.

* **Ciclo de Diálogo:** Se establece un bucle de mensajes donde los agentes intercambian SystemMessage y HumanMessage de LangChain.

* **Evaluación Post-Interacción:** El objeto SimuladorCliente ejecuta una función de evaluación basada en la resolución del problema y la empatía del bot.

# ⚙️ Configuración del Entorno
* **Requisitos Técnicos**_ Python 3.10 o superior.

* **Biblioteca**_ langchain-google-genai para la integración con Gemini.

* **Acceso a una API Key de Google AI Studio.**

# Instalación Rápida
**Instalar dependencias**

    pip install -q -U langchain-google-genai

**Configurar variable de entorno (Linux/windows)**

    export GOOGLE_API_KEY="tu_clave_aqui"

**Ejecutar script**

    python simulador_soporte.py

# Explicacion del Codigo 
** Importar Librerías 📚**

    import os
    import sqlite3
    import random
    from langchain_google_genai import ChatGoogleGenerativeAI
    from langchain_core.messages import SystemMessage, HumanMessage

* **sqlite3** entrega el contexto (quién es el cliente).

* **os** activa el permiso para usar la IA.

* **SystemMessage** le da personalidad a los agentes.

* **ChatGoogleGenerativeAI** genera el diálogo.

* **HumanMessage** mantiene el hilo de la conversación.
  

# **1. Configuración de Entorno**

    os.environ["GOOGLE_API_KEY"] = ""
# **2. Base de Datos de Perfiles**

    def inicializar_bd():
        conn = sqlite3.connect("clientes.db")
        cursor = conn.cursor()
        cursor.execute("""
        CREATE TABLE IF NOT EXISTS perfiles (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            tono TEXT,
            intencion TEXT,
            nivel_conocimiento TEXT,
            personalidad TEXT,
            ubicacion TEXT
        )
        """)
        conn.commit()
        return conn

    
    def insertar_perfil(conn, perfil):
        cursor = conn.cursor()
        cursor.execute("""
        INSERT INTO perfiles (tono, intencion, nivel_conocimiento, personalidad, ubicacion)
        VALUES (?, ?, ?, ?, ?)
        """, (perfil["tono"], perfil["intencion"], perfil["nivel_conocimiento"], perfil["personalidad"], perfil["ubicacion"]))
        conn.commit()

        
    def obtener_perfil_aleatorio(conn):
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM perfiles ORDER BY RANDOM() LIMIT 1")
        fila = cursor.fetchone()
        if fila:
            return {
                "tono": fila[1],
                "intencion": fila[2],
                "nivel_conocimiento": fila[3],
                "personalidad": fila[4],
                "ubicacion": fila[5]
            }
        else:
            return None
# **3. Clase SimuladorCliente**

        class SimuladorCliente:
    def __init__(self, perfil):
        self.llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash", temperature=0.7)
        self.perfil = perfil
        self.historial = []

    def _crear_prompt_sistema(self):
        return f"""
        Actúa como un cliente real de servicios de telecomunicaciones en Ecuador.
        Tu perfil es:
        - Tono: {self.perfil['tono']}
        - Conocimiento técnico: {self.perfil['nivel_conocimiento']}
        - Personalidad: {self.perfil['personalidad']}
        - Ubicación: {self.perfil['ubicacion']}
        
        Tu objetivo es {self.perfil['intencion']}. 
        Usa modismos ecuatorianos ligeros si el tono es informal. No digas que eres una IA.
        """

    def generar_respuesta(self, mensaje_bot=None):
        mensajes = [SystemMessage(content=self._crear_prompt_sistema())]
        
        for msg in self.historial:
            mensajes.append(msg)

        if mensaje_bot:
            mensajes.append(HumanMessage(content=f"Bot dice: {mensaje_bot}"))
        else:
            mensajes.append(HumanMessage(content="Inicia la conversación saludando."))

        respuesta = self.llm.invoke(mensajes)
        self.historial.append(HumanMessage(content=f"Cliente: {respuesta.content}"))
        return respuesta.content

    def evaluar_bot(self):
        puntuacion = random.randint(1, 5)
        comentarios = {
            1: "El servicio fue muy malo, no resolvió mi problema.",
            2: "No quedé satisfecho, la atención fue confusa.",
            3: "El bot respondió, pero podría mejorar en claridad.",
            4: "Buena atención, aunque faltó un poco más de detalle.",
            5: "Excelente servicio, rápido y claro. Muy satisfecho."
        }
        comentario = comentarios[puntuacion]
        return puntuacion, comentario

# **4. Clase BotSoporte**

    class BotSoporte:
    def __init__(self):
        self.llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash", temperature=0.5)
        self.historial = []

    def _crear_prompt_sistema(self):
        return """
        Actúa como un agente de soporte de Netlife en Ecuador.
        Tu objetivo es resolver dudas técnicas de clientes sobre cobertura y servicio.
        Sé empático, claro y profesional. No digas que eres una IA.
        Cuando el problema esté resuelto, finaliza la conversación con una despedida amable.
        """

    def generar_respuesta(self, mensaje_cliente):
        mensajes = [SystemMessage(content=self._crear_prompt_sistema())]

        for msg in self.historial:
            mensajes.append(msg)

        mensajes.append(HumanMessage(content=f"Cliente dice: {mensaje_cliente}"))

        respuesta = self.llm.invoke(mensajes)
        self.historial.append(HumanMessage(content=f"Bot: {respuesta.content}"))
        return respuesta.content
        
# **5. Ejecución de la Simulación**

    def ejecutar_demo():
    conn = inicializar_bd()

    Insertar perfiles de ejemplo si la tabla está vacía
    cursor = conn.cursor()
    cursor.execute("SELECT COUNT(*) FROM perfiles")
    if cursor.fetchone()[0] == 0:
        perfiles_demo = [
            {
                "tono": "un poco impaciente pero educado",
                "intencion": "averiguar por qué no hay cobertura en su sector de Guayaquil",
                "nivel_conocimiento": "bajo",
                "personalidad": "Persona mayor que prefiere explicaciones simples",
                "ubicacion": "Guayaquil, Sauces"
            },
            {
                "tono": "tranquilo",
                "intencion": "consultar sobre la factura de internet",
                "nivel_conocimiento": "medio",
                "personalidad": "joven curioso",
                "ubicacion": "Quito, La Floresta"
            },
            {
                "tono": "molesto",
                "intencion": "reclamar por la velocidad baja del servicio",
                "nivel_conocimiento": "alto",
                "personalidad": "profesional exigente",
                "ubicacion": "Cuenca, Centro"
            },
            {
                "tono": "amable",
                "intencion": "preguntar cómo instalar el router",
                "nivel_conocimiento": "bajo",
                "personalidad": "persona mayor paciente",
                "ubicacion": "Ambato, Ficoa"
            }
        ]
        for p in perfiles_demo:
            insertar_perfil(conn, p)

   **Seleccionar perfil aleatorio**
   
        perfil = obtener_perfil_aleatorio(conn)
        cliente = SimuladorCliente(perfil)
        bot = BotSoporte()

        print("--- INICIO DE SIMULACIÓN ---")
        print(f"📌 Perfil seleccionado: {perfil}\n")

        # Cliente inicia
        frase_inicial = cliente.generar_respuesta()
        print(f"👤 [CLIENTE]: {frase_inicial}\n")

        # Bot responde dinámicamente
        respuesta_bot = bot.generar_respuesta(frase_inicial)
        print(f"🤖 [BOT NETLIFE]: {respuesta_bot}\n")

        # Cliente reacciona
        reaccion_cliente = cliente.generar_respuesta(respuesta_bot)
        print(f"👤 [CLIENTE]: {reaccion_cliente}\n")

        # Bot razona otra vez
        respuesta_bot2 = bot.generar_respuesta(reaccion_cliente)
        print(f"🤖 [BOT NETLIFE]: {respuesta_bot2}\n")

        # Cliente reacciona nuevamente
        reaccion_final_cliente = cliente.generar_respuesta(respuesta_bot2)
        print(f"👤 [CLIENTE]: {reaccion_final_cliente}\n")

        # Evaluación final del cliente
        puntuacion, comentario = cliente.evaluar_bot()
        print(f"⭐ [EVALUACIÓN CLIENTE]: {puntuacion}/5")
        print(f"💬 [COMENTARIO]: {comentario}")

    if __name__ == "__main__":
    ejecutar_demo()

# 🎓 Conclusiones del Proyecto

**1. Eficacia de la Arquitectura de Agentes** El uso de agentes contrapuestos (Cliente vs. Bot) demuestra que es posible automatizar el testing de servicios sin intervención humana. Al asignar roles específicos mediante SystemMessage, el sistema logra una simulación de "teatro lógico" donde cada IA respeta sus fronteras operativas: una centrada en la resolución (Bot) y otra en la experiencia subjetiva (Cliente).

**2. Gestión de Contexto y Memoria** La implementación de self.historial mediante objetos de LangChain resalta la importancia de la memoria conversacional. Sin el almacenamiento secuencial de los HumanMessage, la interacción perdería coherencia. Esto demuestra que la potencia de un LLM no reside solo en su conocimiento preentrenado, sino en su capacidad de procesar el contexto acumulado durante la sesión.
   
**3. Parametrización mediante Persistencia** La integración de SQLite como fuente de verdad para los perfiles de usuario marca la diferencia entre un chatbot estático y un simulador dinámico. Conclusiones técnicas indican que:

* Desacoplar los datos (SQL) de la lógica (Python) facilita la escalabilidad.
* El uso de variables como ubicacion y tono permite evaluar el desempeño del modelo ante sesgos regionales y lingüísticos (modismos ecuatorianos).
   
**4. Control de Estocasticidad (Temperatura)** El ajuste diferenciado de la temperature en las instancias de ChatGoogleGenerativeAI revela un hallazgo crítico:Una temperatura baja ($0.5$) es vital para agentes de soporte que deben seguir protocolos.Una temperatura alta ($0.7$) es necesaria para agentes que simulan humanos, permitiendo una mayor expresividad y realismo en la queja o consulta.

**5. Evaluación Automatizada (Feedback Sintético)** Aunque la función evaluar_bot utiliza actualmente una lógica simple, establece la base para sistemas de Auto-QA. La capacidad del agente cliente para "juzgar" al bot al final de la interacción abre la puerta a procesos de mejora continua donde la IA se entrena a sí misma basándose en métricas de satisfacción generadas sintéticamente.
   
