## Objetivo
Explorar cómo usar y cómo extender nuestra composite custom action.
Para lograrlo, aprovecharemos una aplicación React que crearemos (si no la tenemos ya) con la ayuda de la utilidad create-react-app. 

## Tareas

1. Generar una aplicación React:

   - Crear una nueva carpeta llamada 18-custom-actions en la raíz del repositorio.
   - Usando una terminal, cd en este directorio y crear una aplicación React dentro de un directorio react-app. 
   - Una vez que la configuración de React esté lista, deberías ver un mensaje de éxito.

2. Crear un archivo llamado 19-1-custom-actions-composite.yaml en la carpeta .github/workflows en la raíz de tu repositorio.
   - Nombrar el flujo de trabajo 19 - 1 - Custom Actions - Composite.
   - desencadenantes:
     - workflow_dispatch: el workflow_dispatch también debería recibir un input llamado target-env, de tipo choice y con las siguientes opciones: dev, prod.
   - Establecer la opción run-name del flujo de trabajo en 19 - 1 - Custom Actions - Composite | env - 'recuperar el input target-env aquí'.
   - Definir una variable env de nivel de workflow llamada working-directory y con el valor 19-custom-actions/react-app (o con el directorio que hayas usado para la aplicación React).
   - Trabajos:
     - **build**:
       - Debería ejecutarse en ubuntu-latest.
       - Debería establecer la opción default working-directory para los comandos run en el valor de la variable env working-directory.
       - Debería contener 4 steps:
         - El primer step debería hacer checkout del código.
         - El segundo step, llamado Setup Node and NPM Dependencies, debería usar la acción personalizada recientemente creada (19.1). Para hacer referencia a una acción personalizada creada en el mismo repositorio que el flujo de trabajo, simplemente puedes proporcionar la ruta del directorio donde se encuentra el archivo action.yaml. En este caso, esto sería ./.github/actions/composite-cache-deps.
         - No olvides proporcionar los inputs necesarios a través de la clave 'with'!
         - El tercer step, llamado Test, debería ejecutar el comando npm run test.
         - El cuarto step, llamado Build, debería ejecutar el comando npm run build.

3. Confirmar los cambios y hacer push del código. Desencadenar el flujo de trabajo manualmente desde la interfaz de usuario y tómate unos momentos para inspeccionar el resultado de la ejecución del flujo de trabajo.
4. Ahora vamos a extender nuestra acción personalizada (19.1) para permitirnos omitir las dependencias de desarrollo durante el proceso de instalación. Esto puede ser útil al construir versiones de producción de nuestra aplicación, ya que el tamaño del paquete es mucho más pequeño.
   - Modificar archivo action.yaml para la composite action:
     - Añadir un tercer input, que debería llamarse target-env, no debería ser requerido, tener una descripción de '"dev" or "prod". Controls whether dev dependencies are installed', y por defecto a dev.
     - Cambiar el paso Install dependencies para determinar condicionalmente si debería ejecutar el script npm ci (por defecto) o la variación "npm ci --omit=dev" del script basado en el valor target-env recibido.
     - Actualizar la clave de caché para incluir la información target-env para que coincida correctamente con las cachés 'dev' y 'prod'.
5. Modificar el flujo 19-1-custom-actions.yaml para pasar el input target-env como parámetro a la acción personalizada.
6. Confirmar los cambios y hacer push del código. Desencadenar el flujo de trabajo manualmente desde la interfaz de usuario con diferentes parámetros y tómate unos momentos para inspeccionar el resultado de la ejecución del flujo de trabajo.
