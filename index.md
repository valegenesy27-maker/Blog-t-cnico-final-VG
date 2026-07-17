# Post-Mortem Técnico: Resolviendo Vulnerabilidades en el Registro de Usuarios
Fecha del incidente: 14 de Julio de 2026
Autor: Equipo de Desarrollo Backend & UX
Estado: Resuelto / Lecciones Aprendidas
## Contexto: 
Durante el desarrollo de nuestra plataforma de cursos online, nos enfocamos en diseñar un flujo de registro extremadamente rápido para maximizar la conversión de nuevos estudiantes. El sistema permitía la creación de cuentas de forma ágil, priorizando la velocidad de navegación en el frontend. El entorno de producción corría de manera estable, pero pronto comenzamos a detectar inconsistencias críticas en la base de datos de usuarios.
## Problema 
El enfoque en la "rapidez" nos llevó a omitir validaciones estrictas en el lado del servidor y del cliente. Esto desencadenó los siguientes problemas técnicos en producción:
- Inyección de datos corruptos: Los usuarios podían registrarse con correos electrónicos sintácticamente incorrectos (por ejemplo, sin "@", sin dominio o con espacios en blanco).
- Falta de integridad: Se procesaban formularios con campos obligatorios vacíos.
- Fallas en la comunicación: Al no registrarse correos válidos, el sistema de colas fallaba al intentar enviar los emails de confirmación y recuperación de contraseñas, saturando los logs de error del servidor.
- Pésima experiencia de usuario (UX): Cuando el backend rechazaba silenciosamente un registro mal formateado, el sistema no mostraba ningún mensaje de error en la interfaz, dejando al usuario congelado en la pantalla de carga.
## Acciones
(Post-Mortem Constructivo)
Para solucionar la incidencia y evitar que vuelva a ocurrir, el equipo se reunió en una sesión de Post-Mortem Constructivo bajo la premisa de no buscar culpables (blameless post-mortem), enfocándonos puramente en la mejora de nuestros procesos de ingeniería:
- Revisión del Flujo y Diagnóstico: Mapeamos el ciclo de vida del registro e identificamos que la validación se delegaba únicamente a un regex básico en el frontend que fue desactivado accidentalmente en una actualización previa.
- Robustecimiento del Código (Hotfix):
Implementamos validaciones robustas de formato utilizando librerías estándar en el backend.
Diseñamos mensajes de error descriptivos y amigables en la interfaz de usuario para indicar exactamente qué campo falló (ej. "Por favor, ingresa un correo válido con formato nombre@ejemplo.com").
- Automatización de Pruebas: Establecimos que ninguna funcionalidad de entrada de datos se desplegará en el futuro sin pruebas unitarias e de integración correspondientes.
## Aprendizajes
Este incidente nos dejó valiosas lecciones técnicas y metodológicas:
- Validar siempre en ambos lados: La validación en el cliente (frontend) es para la UX; la validación en el servidor (backend) es por seguridad e integridad de datos. Ambas son obligatorias.
- Monitoreo preventivo: Debimos haber capturado el incremento de errores en el servicio de emails mucho antes. Implementaremos alertas automáticas.
- Cultura Constructiva: Abordar el error buscando fallas en el pipeline de trabajo (y no apuntando con el dedo a quien commiteó el bug) nos permitió resolver el problema en tiempo récord y sin fricciones en el equipo.
## Evidencia de Control de Versiones
El proceso de resolución se documentó de manera transparente utilizando Git. A continuación se detallan los commits clave del flujo de trabajo (Pull Request #42): 
- Commit 1: feat: agregar validaciones estrictas de formato email en backend
- Commit 2: fix: mejorar feedback visual y mensajes de error en formulario
- Commit 3: test: añadir pruebas unitarias para casos límite de registro
## Reflexión sobre Feedback Radicalmente Sincero
Durante la sesión de Post-Mortem y la revisión de código (Code Review), aplicamos el concepto de Feedback Radicalmente Sincero (Radical Candor). En lugar de caer en la "empatía ruinosa" (callar el error para no incomodar) o en la "agresión ofensiva" (criticar el trabajo del desarrollador frontend), nos comunicamos bajo el principio de Cuidar Directamente y Desafiar de Frente. Señalamos de forma muy clara y directa que saltarse las pruebas de integración en el sprint anterior fue un error crítico de metodología. Esta honestidad brutal, combinada con un ambiente de apoyo mutuo, nos permitió aceptar la crítica sin tomárnosla como algo personal, corregir el flujo de trabajo de inmediato y fortalecer la confianza técnica del equipo.
