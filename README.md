# Automatización de Registro de Notas en HubSpot con IA y n8n

Este proyecto es una demostración de un flujo de trabajo de automatización avanzado, construido en n8n, que procesa transcripciones de voz, extrae información clave mediante IA y registra los datos como notas en el CRM de HubSpot. La solución está diseñada para ser robusta, manejando diferentes escenarios de negocio y utilizando llamadas directas a la API para una integración completa.

Este flujo de trabajo representa un **caso de uso real y de alto valor** para equipos de ventas y soporte, permitiendo la automatización del registro de interacciones de voz y asegurando que ninguna información valiosa se pierda.

## Diagrama del Flujo de Trabajo

## Diagrama del Flujo de Trabajo

El flujo sigue una arquitectura lógica para procesar los datos, tomar decisiones y ejecutar las acciones correspondientes en el CRM.

```mermaid
graph TD
    subgraph "1. Entrada y Análisis"
        A[▶️ Webhook: Recibe Transcripción] --> B(🧠 AI Agent: Extrae Entidades);
        B --> C{📊 Parsear Datos: Limpia JSON};
    end

    subgraph "2. Lógica de Negocio en HubSpot"
        C --> D[🔍 HubSpot: Buscar Contacto por Email];
        D --> E{❓ IF: ¿Contacto Existe?};
    end

    subgraph "3. Ramal A: Contacto NO Existe"
        E --o|False| F[➕ HubSpot: Crear Contacto];
        F --> G[📝 HTTP Request: Crear y Asociar Nota];
    end

    subgraph "4. Ramal B: Contacto SÍ Existe"
        E --✔|True| H[📝 HTTP Request: Crear y Asociar Nota a Contacto Existente];
    end

    subgraph "5. Finalización"
        G --> Z[✅ Fin del Flujo];
        H --> Z;
    end

    style A fill:#D6EAF8,stroke:#3498DB,stroke-width:2px
    style B fill:#D1F2EB,stroke:#1ABC9C,stroke-width:2px
    style C fill:#D1F2EB,stroke:#1ABC9C,stroke-width:2px
    style D fill:#FAD7A0,stroke:#F39C12,stroke-width:2px
    style E fill:#FCF3CF,stroke:#F39C12,stroke-width:2px
    style F fill:#FADBD8,stroke:#E74C3C,stroke-width:2px
    style G fill:#FADBD8,stroke:#E74C3C,stroke-width:2px
    style H fill:#E8DAEF,stroke:#8E44AD,stroke-width:2px
    style Z fill:#E5E7E9,stroke:#979A9A,stroke-width:2px
```


## Requerimiento del Proyecto (Caso de Negocio)

El objetivo principal es automatizar el proceso de registrar interacciones de voz con clientes en HubSpot. El flujo de trabajo debe ser capaz de:

1.  **Recibir Datos:** Aceptar una transcripción de texto a través de un endpoint seguro (Webhook).
2.  **Análisis con IA:** Utilizar un modelo de lenguaje para analizar el texto y extraer entidades estructuradas como nombre, email, nombre de la empresa y la intención de la llamada.
3.  **Integración Inteligente con HubSpot:**
    *   Buscar si un contacto ya existe en HubSpot usando el email extraído.
    *   Si el contacto **existe**, añadir la transcripción completa como una nueva nota a su registro.
    *   Si el contacto **no existe**, crear un nuevo registro de contacto y, posteriormente, añadir la nota.
4.  **Entrega:** El flujo debe ser exportable como un archivo JSON para su fácil despliegue en otras instancias de n8n.

## Tecnologías y Componentes Clave

Este proyecto demuestra competencia en la integración de varias tecnologías modernas de automatización y API:

*   **n8n.io:** La plataforma de automatización low-code/pro-code que orquesta todo el flujo de trabajo.
*   **Inteligencia Artificial (AI Agent):** Integración con un modelo de lenguaje (como los accesibles a través de OpenRouter) para realizar Procesamiento de Lenguaje Natural (NLP) y extracción de entidades.
*   **HubSpot CRM:** El sistema de gestión de relaciones con clientes donde se almacenan y gestionan los datos.
*   **API de HubSpot (vía `HTTP Request`):** Se realiza una llamada directa a la API REST de HubSpot para crear y asociar notas. Esto demuestra la capacidad de superar las limitaciones de los nodos nativos y realizar integraciones a un nivel más profundo y profesional.
*   **Webhooks:** Utilizados como el punto de entrada para la recepción de datos en tiempo real.
*   **JSON:** El formato de datos estándar utilizado para la comunicación entre todos los servicios.
*   **JavaScript (en Nodo `Code`):** Utilizado para transformar y preparar datos de manera confiable antes de ser enviados a la API.

## Consideraciones para Pruebas

Para probar este flujo de trabajo, se necesita la siguiente configuración:

1.  **Instancia de n8n:** Un entorno de n8n donde se pueda importar el archivo `.json` del flujo.
2.  **Cuenta de HubSpot:** Se requiere acceso a una cuenta de HubSpot.
3.  **Credenciales:**
    *   **AI Agent:** Una clave de API para el servicio de IA (ej. OpenRouter).
    *   **HubSpot Private App:** Se debe crear una Aplicación Privada en HubSpot con los siguientes `scopes` (permisos) para generar un Access Token:
        *   `crm.objects.contacts.read`
        *   `crm.objects.contacts.write`
        *   `crm.objects.notes.write`
4.  **Herramienta de Solicitud HTTP (ej. Postman, Insomnia, ReqBin):** Para enviar datos de prueba al endpoint del Webhook de n8n.

### **Escenarios de Prueba:**

*   **Caso 1 (Contacto Nuevo):** Enviar un JSON con una transcripción que contenga un email que **no** exista en HubSpot.
    *   **Resultado Esperado:** Un nuevo contacto se crea en HubSpot, y una nota con la transcripción se asocia a él.
*   **Caso 2 (Contacto Existente):** Enviar un JSON con una transcripción que contenga un email que **sí** exista en HubSpot.
    *   **Resultado Esperado:** Al contacto existente se le añade una nueva nota con la transcripción.

**Ejemplo de `Body` para la solicitud POST al Webhook:**

```json
{
  "transcripcion": "Hola, mi nombre es Ana García de la empresa Soluciones Tech. Mi correo es ana.garcia.demo@email.com. Te llamo para hacer un seguimiento sobre la propuesta que enviamos la semana pasada."
}
