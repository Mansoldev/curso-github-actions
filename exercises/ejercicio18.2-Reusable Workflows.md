## Objetivo
Familiarizarse con la creación y uso de flujos de trabajo reutilizables entre diferentes repositorios.


## Tareas

### Configurar un proyecto de pruebas E2E de Cypress en un nuevo repositorio:

1. Crear un nuevo repositorio en GitHub llamado gha-course-example-e2e.
2. Marcar el repositorio como privado y proporcionar una descripción razonable.
3. Marcar la casilla para agregar un archivo README al repositorio.
4. Desplazarse hacia abajo y hacer clic en Crear repositorio.
5. Clonar este repositorio en tu configuración local utilizando tu método preferido. No clonar esto dentro de un directorio existente. Abrir el nuevo directorio del repo en Visual Studio Code y abrir una terminal en el nuevo directorio.
6. Inicializar un proyecto npm con npm init -y.
7. Instalar Cypress con npm install cypress@13.6.1 --save-dev --save-exact.
8. Una vez finalizada la instalación, ejecutar npx cypress open para configurar Cypress.
9. Debería aparecer una nueva ventana. Hacer clic en Continuar y en E2E Testing.
10. Hacer clic en Continuar una vez más, y luego en Iniciar pruebas E2E en Chrome. Debería aparecer una nueva ventana.
11. En esta nueva ventana, hacer clic en Scaffold example specs. Esto generará varios archivos de prueba en el directorio gha-course-example-e2e. Tomarse unos minutos para inspeccionar los archivos creados en la carpeta cypress/e2e.
12. Volver a la ventana que ejecuta las pruebas E2E, y hacer clic en cualquiera de los nombres de archivo listados en la pantalla principal. Tomarse unos minutos para inspeccionar cómo Cypress ejecuta las pruebas, así como los resultados de las pruebas.
13. Cerrar todas las ventanas de Chrome, la configuración está lista y podemos usar Cypress en la CLI.
14. Añadir un archivo .gitignore y ignorar la carpeta node_modules.
15. Añadir una nueva entrada al apartado scripts del archivo package.json:
    ```json
    "test:e2e": "cypress run"
    ```
16. Ejecutar las pruebas de Cypress con npm run test:e2e.
17. Para que las pruebas se ejecuten más rápido, eliminar la carpeta 2-advanced-examples y todo su contenido. Esto dejará un solo archivo en ejecución, que es mucho más rápido que ejecutar todos los archivos.
18. Confirmar y hacer push de todos los cambios. 

### Crear el flujo de trabajo E2E dentro del nuevo repositorio:

1. Crear un archivo llamado e2e.yaml en la carpeta .github/workflows del repositorio gha-course-example-e2e.
2. Nombrar el flujo de trabajo E2E Tests.
3. Añadir los siguientes desencadenantes al flujo de trabajo:
   - workflow_dispatch
4. Añadir un único job llamado e2e-tests al flujo de trabajo, con las siguientes especificaciones:
   - Debería ejecutarse en ubuntu-latest.
   - Debería contener cuatro steps:
     - El primer step, llamado Checkout code, debería hacer checkout del código usando la acción de terceros apropiada.
     - El segundo step, llamado Setup node, debería usar la acción de terceros apropiada para configurar Node con una versión de '20.x'.
     - El tercer step, llamado Install dependencies, debería instalar las dependencias de npm con npm ci.
     - El cuarto step, llamado Run E2E tests, debería ejecutar el comando npm run test:e2e.
5. Confirmar los cambios y hacer push del código. Desencadenar el flujo de trabajo manualmente desde la interfaz de usuario y tómate unos momentos para inspeccionar el resultado de la ejecución del flujo de trabajo. 


### Ejecutar un flujo de trabajo reutilizable desde otro repositorio:

1. Desde el repositorio original en el que se están haciendo todos los ejercicios:
   - 1.1 Asegurarse de que se pueden ejecutar flujos de trabajo reutilizables desde otro repositorio yendo a la configuración del repositorio, luego a Actions, luego a General, y marcando la opción "Allow all actions and reusable workflow" en la parte superior de la página.
   - 1.2 Crear un archivo llamado 18-3-reusable-workflows.yaml en la carpeta .github/workflows.
   - 1.3 Las especificaciones del flujo de trabajo reutilizable son las siguientes:
      - Nombre: 18 - 3 - Reusable Workflows.
      - Desencadenantes:
        - workflow_dispatch
   - 1.4 Añadir un primer job llamado *deploy* al flujo de trabajo, con las siguientes especificaciones:
      - Debería usar el flujo de trabajo reutilizable definido anteriormente. Para ello, simplemente usa la key *uses* a nivel de job y pasa la ruta del flujo de trabajo: ./.github/workflows/18-1-reusable-workflows.yaml
      - Pasar cualquier valor como argumento de target-directory al flujo de trabajo reutilizable.
   - 1.5 Añadir un segundo job llamado *e2e-tests* al flujo de trabajo, con las siguientes especificaciones:
      - Debería ejecutarse en ubuntu-latest.
      - Debería tener una dependencia del job *deploy*.
      - Debería usar el flujo de trabajo reutilizable E2E definido anteriormente. Para ello, simplemente usa la key *uses* a nivel de job y pasa el identificador completo del flujo de trabajo: <owner>/<repository>/.github/workflows/e2e.yaml@<branch, tag, or sha commit>.
   - 1.6 Confirmar los cambios y hacer push del código. 

Este paso no funcionará todavía. Ahora vamos a seguir los pasos necesarios para hacer que todo funcione.

2. En el nuevo repositorio (gha-course-example-e2e):
   - 2.1 Añadir un desencadenador de evento *workflow_call* al archivo de definición del flujo de trabajo e2e.yaml.
   - 2.2 Ir a la configuración del repositorio -> Actions -> General, y desplazarse hacia abajo. Bajo el encabezado de Acceso, seleccionar la opción "Accessible from repositories owned by the user..." (o en la organización xyz dependiendo de si se están utilizando organizaciones). Guardar los cambios.
   - 2.3 Si el repositorio original (el utilizado en el punto 1 anterior) es público, no podrá ejecutar un flujo de trabajo reutilizable privado. Por lo tanto, marcar dicho repositorio como privado:
     - Ir a la página de configuración del repositorio y desplazarse hacia abajo.
     - En la sección Zona de peligro, hacer clic en el botón Cambiar visibilidad y luego en Cambiar a Privado.
   - 2.4 Una vez realizados estos pasos, intentar volver a desencadenar el flujo de trabajo. 

Todavía fallará, pero por un motivo diferente. Vamos a trabajar en eso a continuación.

3. En el nuevo repositorio (gha-course-example-e2e):
   - 3.1 Cambiar el archivo de definición del flujo de trabajo e2e.yaml:
     - Pasar los siguientes parámetros a la acción de checkout:
       - repository: <tu nombre de usuario u organización>/<tu repositorio original github-actions-course>
       - ref: main
   - 3.2 Confirmar los cambios y hacer push del código. Si se vuelve a desencadenar el flujo de trabajo, seguirá viendo un problema de acceso al comprobar el código del repositorio e2e. Vamos a solucionar esto ahora.
   - 3.3 Bajo el desencadenador *workflow_call*, añadir una clave de secrets y luego un único secreto llamado access-token, que debería estar marcado como requerido.
   - 3.4 Pasar un parámetro adicional a la acción de checkout:
     - token: <recuperar el valor del input access-token. Si no está disponible, usar el valor predeterminado secrets.GITHUB_TOKEN
   
4. En el repositorio original (github-actions-course):
   - 4.1 Crear un nuevo token de acceso personalizado (PAT). Si se están utilizando organizaciones, tendrás que autorizar a los usuarios a acceder al contenido del repositorio a través de PATs. Para ello:
     - Hacer clic en tu imagen, desplazarse hacia abajo y hacer clic en Configuración.
     - En la nueva página, desplazarse hacia abajo hasta Configuración de desarrollador.
     - Bajo Tokens de acceso personal, hacer clic en granular y luego en Generar un nuevo token.
     - Seleccionar solo el repositorio gha-course-example-e2e y dar al token acceso de lectura al contenido del repositorio.
     - Guardar los cambios y almacenar el token de forma segura.
     - Crear un secreto de repositorio (se puede crear de forma similar a como se crearon las variables de repositorio) llamado GH_TOKEN y guardar el valor del token como un secreto. 
   - 4.2 Ahora podemos acceder al secreto desde nuestro archivo 18-3-reusable-workflows.yaml a través del contexto de secrets:
     - Añadir una clave de secrets junto a la clave uses que hace referencia al flujo de trabajo e2e y pasar un único secreto llamado access-token con un valor de ${{ secrets.GH_TOKEN }}.

5. Confirmar los cambios y hacer push del código. Si todos los pasos se ejecutaron correctamente, el flujo de trabajo reutilizable debería ejecutarse correctamente ahora. 
