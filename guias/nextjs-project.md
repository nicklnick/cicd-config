# Creación de proyecto de Next.js

En esta sección, crearemos un proyecto de Next.js para luego dockerizarlo y poder realizar los tests unitarios y e2e (UX/UI) en él.

1. En primer lugar es recomendable eliminar el archivo `README.md` autogenerado por GitLab, ya que este entrará en conflicto con la herramienta de generación de proyectos de Next.js. Para esto nos pararemos sobre el directorio raíz del proyecto y ejecutaremos:

   ```bash
   rm README.md
   ```

2. Inicializaremos el proyecto con el comando a continuación y eligiremos las distintas opciones por las que nos va llevando la CLI a elección.

   ```bash
   # Con "./" le indicamos generar el proyecto dentro de la misma carpeta en la cual
   # nos encontramos
   npx create-next-app@latest ./
   ```

3. A continuación podremos probar la aplicación generada con el comando

   ```bash
   npm run dev
   ```

   Esto nos mostrará en la terminal un mensaje del estilo

   ```bash
   > my-project@0.1.0 dev
   > next dev

       ▲ Next.js 14.2.3
       - Local:        http://localhost:3000

       ✓ Starting...
       ✓ Ready in 1670ms
   ```

   Si accedemos al navegador en la URL `http://localhost:3000`, podremos observar la aplicación ejecutandose localmente

   <img src="../img/guias/nextjs-project-paso3.png" width="70%"/>