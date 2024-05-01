## Objetivo
En este ejercicio práctico, nuestro objetivo es familiarizarnos con la prevención de ataques de inyección de scripts en GitHub Actions.

## Tareas

1. Crear un archivo llamado 21 - workflow-security.yaml bajo la carpeta .github/workflows en la raíz de su repositorio. El archivo debe tener las siguientes características:
   - Nombre: 21 - Workflow Security.
   - desencadenantes:
     - pull_request
   - Añadir un único trabajo llamado unsafe-pr al flujo de trabajo.
     - Debería ejecutarse en ubuntu-latest.
     - Debería contener dos steps:
       - El primer step debería hacer checkout del código usando la acción de terceros apropiada.
       - El segundo step, llamado Check PR title, debería ejecutar el siguiente script de shell (comprueba si el título de la PR comienza con el substring feat e imprime un mensaje en consecuencia):
         ```shell
         title=${{ github.event.pull_request.title }}
         if [[ $title =~ ^feat ]]; then
           echo "PR is a feature"
           exit 0
         else
           echo "PR is not a feature"
           exit 1
         fi
         ```
2. Confirmar los cambios y hacer push del código. Crear una PR cambiando cualquier archivo en el repositorio, comprometiendo los cambios en una nueva rama y proponiendo los cambios a través de una PR. Proporcionar el siguiente título a la "abc"; ls -R; . Tómese unos momentos para inspeccionar el resultado de la ejecución del flujo de trabajo desencadenado. ¿Qué comandos ejecutó?
3. Añadir un segundo trabajo llamado safer-pr al flujo de trabajo.
   - Debería ejecutarse en ubuntu-latest.
   - Debería contener dos steps:
     - El primer step debería hacer checkout del código usando la acción de terceros apropiada.
     - El segundo step, llamado Check PR title, debería ejecutar el mismo script que arriba. Sin embargo, en lugar de establecer la variable title en el comando de shell, debería establecer una variable de entorno llamada TITLE y con el valor establecido en el título de la PR, y luego hacer referencia a esta variable desde el script de shell.
4. Confirmar los cambios y hacer push del código. Crear una PR cambiando cualquier archivo en el repositorio, comprometiendo los cambios en una nueva rama y proponiendo los cambios a través de una PR. Proporcionar el titulo " 'abc'; ls -R;" . Tómese unos momentos para inspeccionar el resultado de la ejecución del flujo de trabajo desencadenado. ¿Se ejecutaron los comandos maliciosos?
