# Entregable-2-CI

## 1. Integrantes

Ioav Mizrachi - [@Imizrachim](https://github.com/Imizrachim) 

Mateo Muñoz - [@mateomunozb](https://github.com/usuario2_github)(propietario del repositorio)

## 2. Evidencias

- **URL del Pull Request de la sección 9:** https://github.com/mateomunozb/cicd-pipeline-python/pull/1

- **URL de la ejecución fallida del pipeline causada por el error intencional:** https://github.com/mateomunozb/cicd-pipeline-python/actions/runs/43612804177/job/132501314761

## 3. Preguntas

### 1. Ventajas de un pipeline de CI

El uso de un pipeline de Integración Continua proporciona diferentes beneficios durante el desarrollo del proyecto.

- **Detección temprana de errores.** Permite identificar errores automáticamente cada vez que se realizan nuevos cambios, evitando que lleguen a los usuarios finales o afecten otras ramas.
- **Garantía de calidad continua.** Permite establecer estándares de calidad mediante validaciones de linters y porcentajes mínimos de cobertura de pruebas, ayudando a mantener un código organizado y consistente.
- **Automatización de tareas repetitivas.** Evita tener que ejecutar manualmente las pruebas, los análisis de seguridad y la construcción de imágenes Docker. Esto facilita el proceso de desarrollo y reduce posibles errores.

### 2. Diferencia entre tipos de pruebas

- **Prueba unitaria.** Se utiliza para validar una parte específica del código de manera aislada, verificando que su lógica funcione correctamente sin depender de otros componentes del sistema.
- **Prueba de componente.** Se utiliza para verificar el funcionamiento de un componente y su interacción con otras partes de la aplicación, como sus rutas y plantillas. Para esto se puede utilizar un cliente de pruebas que simula las peticiones sin necesidad de levantar un servidor real.
- **Prueba de aceptación.** Se utiliza para validar el comportamiento de la aplicación desde la perspectiva del usuario final. En este caso, se automatiza un navegador web para interactuar con la aplicación y verificar que las funcionalidades respondan como se espera.

### 3. Pasos del workflow de GitHub Actions

- **Checkout.** Permite obtener el código fuente del repositorio dentro del entorno virtual de GitHub Actions. En este caso, también permite que SonarCloud pueda identificar los cambios realizados.
- **Set up Python e Install dependencies.** Se encargan de configurar la versión de Python requerida e instalar las dependencias del proyecto para poder ejecutar la aplicación y las herramientas de validación.
- **Run Black, Pylint y Flake8.** Ejecutan herramientas de análisis y formato de código para verificar que se cumplan los estándares definidos. En este caso, permiten identificar problemas de estilo y calidad sin detener el pipeline por los hallazgos.
- **Run Unit and Component Tests.** Ejecuta las pruebas mediante pytest y verifica que se cumpla una cobertura mínima del 80%, permitiendo validar que la lógica principal funcione correctamente antes de continuar.
- **Build Docker image y Start container.** Se encargan de construir la imagen Docker con la aplicación y levantar un contenedor en segundo plano para disponer de un entorno en ejecución donde realizar las siguientes pruebas.
- **Run Acceptance Tests.** Ejecuta las pruebas de Selenium sobre el contenedor activo, verificando que la aplicación funcione correctamente desde la perspectiva del usuario.
- **SonarCloud Scan.** Envía el código y los reportes generados a SonarCloud para realizar el análisis de calidad y seguridad. En este caso, también evalúa el quality gate y detiene el pipeline si no se cumplen los requisitos establecidos.
- **Login y Push Docker image.** Permiten autenticar la sesión y publicar la imagen validada en Docker Hub para que quede disponible para futuros despliegues.

### 4. Dificultades y aprendizajes

- **Mateo Muñoz:** Una de las dificultades fue entender cómo GitHub Actions maneja los contenedores y sus puertos dentro del entorno virtual, ya que es diferente a lo que normalmente utilizo en Jenkins. Para solucionarlo, fue necesario revisar cómo se ejecutan los servicios en segundo plano y ajustar la configuración para poder realizar las pruebas de aceptación.

- **Ioav Mizrachi:** En mi caso, esta actividad fue un primer acercamiento al tema de pruebas y a herramientas de DevOps, ya que anteriormente no había trabajado con este tipo de procesos. Como normalmente desarrollo proyectos propios y no trabajo dentro de equipos de desarrollo, nunca había sentido la necesidad de implementar estas prácticas. Sin embargo, durante la actividad entendí que también pueden ser útiles en proyectos individuales, especialmente para validar los cambios, detectar errores a tiempo y mantener una mejor organización del código. Creo que gran parte de ese desconocimiento se debía a que no había tenido la oportunidad de trabajar con estas herramientas.

### 5. Construcción y publicación de la imagen

Construir la imagen de Docker, ejecutar las pruebas de aceptación sobre ella y publicar esa misma imagen permite garantizar que el artefacto que se despliega sea el mismo que se validó durante el pipeline. Esto evita problemas relacionados con diferencias entre entornos, ya que las pruebas se realizan sobre la aplicación empaquetada con sus dependencias y configuración. De esta manera, se reducen los riesgos de encontrar errores durante el despliegue que no fueron detectados previamente.

### 6. Validación del Pull Request y protección de rama

Para las pruebas adicionales se agregaron **test_sumar_decimales**, con el objetivo de detectar problemas de precisión en operaciones con números decimales, y **test_operaciones_con_dos_negativos**, para verificar el comportamiento de las operaciones cuando se utilizan dos valores negativos.

Al introducir un error intencional en la función de resta, el pipeline falló en el paso **Run Unit and Component Tests**, debido a que no se superaron las pruebas relacionadas con esta operación.

Como resultado, se detuvo la ejecución y no se realizaron los pasos posteriores de construcción de la imagen, pruebas de aceptación, análisis de SonarCloud y publicación en Docker Hub. Esto permitió evitar que el error llegara a las siguientes etapas del proceso, ya que GitHub bloqueó la posibilidad de hacer merge en el PR al no cumplirse las comprobaciones obligatorias y, adicionalmente, se evitó publicar una imagen que contenía el error.
