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

# **📋 Tabla de Contenidos:**

**Descripción General**

**Características Principales**

**Arquitectura del Sistema**

**Instalación y Configuración**

**Uso**

**Tecnologías Utilizadas** 

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
