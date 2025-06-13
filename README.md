# 🏥 Bot de Telegram - Registro de Seguros Médicos

Un bot inteligente de Telegram desarrollado con **n8n** que automatiza el proceso de registro de usuarios para seguros médicos mediante conversaciones naturales.

## 📋 Descripción

Este workflow de n8n implementa un asistente conversacional que recopila de forma ordenada los datos personales necesarios para el registro en un seguro médico, validando cada campo y manteniendo el contexto de la conversación.

## ✨ Características

- 🤖 **Conversación natural** con IA (GPT-4o-mini)
- 📝 **Recopilación automática** de 7 datos personales
- ✅ **Validación en tiempo real** de cada campo
- 💾 **Persistencia** en base de datos PostgreSQL
- 🧠 **Memoria conversacional** para mantener el contexto
- 🌍 **Soporte multiidioma** (Español por defecto)

## 🏗️ Arquitectura del Workflow

```
Telegram Trigger → AI Agent → Telegram Response
                      ↓
        [Cerebro] + [Memoria] + [STORE]
         OpenAI     PostgreSQL   PostgreSQL
```

### Componentes

| Nodo | Función | Descripción |
|------|---------|-------------|
| **Telegram Trigger** | Entrada | Escucha mensajes entrantes |
| **AI Agent** | Motor | Procesa conversación y lógica |
| **Cerebro** | IA | Modelo GPT-4o-mini de OpenAI |
| **Memoria** | Contexto | Historial conversacional |
| **STORE** | Persistencia | Almacena datos validados |
| **Telegram** | Salida | Envía respuestas al usuario |

## 📊 Datos Recopilados

El bot recopila la siguiente información en orden secuencial:

1. 👤 **Nombre completo**
2. 📞 **Teléfono** *(mínimo 6 dígitos, soporte +34)*
3. 📧 **Email** *(debe contener @ y .)*
4. 🎂 **Edad** *(0-120 años, se convierte a fecha de nacimiento)*
5. 🏡 **Dirección completa**
6. 🆔 **Documento de identidad**
7. 💼 **Ocupación**

> ⚠️ **Nota**: La edad se convierte automáticamente a fecha de nacimiento en formato `AAAA-MM-DD`

## 🔧 Configuración

### Prerrequisitos

- ✅ Instancia de n8n
- ✅ Bot de Telegram configurado
- ✅ Base de datos PostgreSQL
- ✅ API Key de OpenAI

### Variables de Entorno

```env
# Telegram Bot Token
TELEGRAM_BOT_TOKEN=your_bot_token_here

# OpenAI API
OPENAI_API_KEY=your_openai_key_here

# PostgreSQL Connection
POSTGRES_HOST=your_postgres_host
POSTGRES_DB=your_database_name
POSTGRES_USER=your_username
POSTGRES_PASSWORD=your_password
```

### Estructura de Base de Datos

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nombre_completo VARCHAR(255) NOT NULL,
    telefono VARCHAR(50) NOT NULL,
    email VARCHAR(255) NOT NULL,
    fecha_nacimiento DATE NOT NULL,
    direccion TEXT NOT NULL,
    documento_identidad VARCHAR(100) NOT NULL,
    ocupacion VARCHAR(255) NOT NULL,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 🚀 Instalación

1. **Importar el workflow**
   ```bash
   # Importa el archivo pifienergy-n8n.json en tu instancia de n8n
   ```

2. **Configurar credenciales**
   - Telegram API
   - OpenAI API
   - PostgreSQL Connection

3. **Activar el workflow**
   ```
   Estado: ✅ Activo
   ```

## 💬 Ejemplo de Conversación

```
Usuario: Hola, quiero registrarme
Bot: ¡Hola! Te ayudo con el registro para tu seguro médico. 
     Empecemos con tu nombre completo, por favor.

Usuario: Juan Pérez García
Bot: ¡Perfecto, Juan Pérez García! 📌 
     Ahora dime tu número de teléfono, por favor.

Usuario: +34 612 345 678
Bot: Excelente 📞 Ahora necesito tu email...

[... continúa hasta completar los 7 campos ...]

Bot: 📋 Resumen de tu registro:
     - 👤 Nombre: Juan Pérez García
     - 📞 Teléfono: +34 612 345 678
     - 📧 Email: juan@example.com
     - 🎂 Fecha de nacimiento: 1990-05-15
     - 🏡 Dirección: Calle Mayor 123, Madrid
     - 🆔 Documento: 12345678A
     - 💼 Ocupación: Ingeniero
     
     🎉 ¡Registro completado! Te contactaremos pronto.
```

## ⚙️ Validaciones

| Campo | Regla de Validación |
|-------|-------------------|
| **Teléfono** | Mínimo 6 dígitos, acepta +34 |
| **Email** | Debe contener `@` y `.` |
| **Edad** | Entre 0 y 120 años |
| **Otros** | No pueden estar vacíos |

## 🔍 Funcionalidades Avanzadas

### Procesamiento Inteligente
- ✅ Detecta múltiples datos en un solo mensaje
- ✅ Ignora respuestas tipo "ok", "sí", "vale"
- ✅ Manejo de errores con mensajes amigables

### Memoria Conversacional
- 🧠 Recuerda el contexto completo
- 🧠 Ventana de contexto: 10 mensajes
- 🧠 Sesión por chat ID único

## ⚠️ Limitaciones y Consideraciones

> **⚠️ Advertencia**: Este bot recopila datos personales sensibles. Asegúrate de cumplir con las regulaciones de privacidad aplicables (GDPR, LOPD, etc.)

> **🔒 Seguridad**: Los datos se almacenan en PostgreSQL. Implementa medidas de seguridad adecuadas:
> - Cifrado de base de datos
> - Acceso restringido
> - Copias de seguridad regulares

> **📊 Rendimiento**: El modelo GPT-4o-mini tiene límites de tokens (500 max) y temperatura baja (0.3) para respuestas consistentes.

## 🛠️ Personalización

### Modificar Campos
Para cambiar los campos recopilados, edita el prompt del sistema en el nodo **AI Agent**:

```javascript
// Ejemplo: Agregar campo "Estado Civil"
8. Estado civil (soltero/casado/divorciado/viudo)
```

### Cambiar Idioma
Modifica el `systemMessage` en el nodo **AI Agent** para cambiar el idioma de las interacciones.

### Ajustar Validaciones
Las reglas de validación se encuentran en la sección `🔍 VALIDACIONES` del prompt del sistema.

## 📈 Métricas y Monitoreo

El workflow incluye campos para tracking:
- `fecha_registro`: Timestamp automático
- `id`: Identificador único secuencial

## 🤝 Contribución

1. Fork el repositorio
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para más detalles.

## 🆘 Soporte

Si encuentras algún problema o tienes preguntas:

1. 🐛 **Issues**: Reporta bugs en GitHub Issues
2. 💬 **Discusiones**: Usa GitHub Discussions para preguntas
3. 📧 **Email**: Contacto directo para asuntos críticos

---

**⭐ Si este proyecto te resulta útil, ¡considera darle una estrella!**
