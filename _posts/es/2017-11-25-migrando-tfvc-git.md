---
title: "Migrando nuestros proyectos de TFVC a Git"
header:
  image: /assets/posts/es/migrando-tfvc-git/header.jpg
tags: [ Español, Git, TFVC ]
categories: [ Git, DevOps ]
lang: es
ref: 7
permalink: /es/migrando-tfvc-git/
---

Con el declive de sistemas de control de versiones de código como [**Team Foundation Version Control (TFVC)**](https://www.visualstudio.com/docs/tfvc/overview) y el auge de otras como [**Git**](https://git-scm.com/), nos encontramos ante un problema común y recurrente en repositorios *TFVC*:

¿Cómo cambiamos de un repositorio *TFVC* ya existente a un repositorio *Git*, sin afectar a todo el trabajo ya realizado, a todo el seguimiento de código, de manera sencilla y rápida, y lo más importante, sin que afecte al equipo?

Ante este problema, nos encontramos con dos soluciones:

- Crear manualmente un repositorio *Git* y pasar poco a poco cada uno de estos cambios e información que nos gustaría conservar.

- Usar herramientas creadas por la comunidad para, en pocos pasos, copiar todo el contenido de un repositorio a otro y además obtener las ventajas que *Git* aporta a nuestro proyecto.

La respuesta obvia es la segunda opción, y el motivo de este documento, sirviéndonos de la herramienta [**git-tfs**](http://git-tfs.com/) para lograrlo. Comenzamos.

## Requerimientos previos

Necesitas tener instalado [*.NET 4*](https://msdn.microsoft.com/en-us/library/w0x726c2(v=vs.110).aspx) o superior, y las versiones 2010, 2012, 2013 o 2015 de [*Team Explorer*](https://msdn.microsoft.com/es-es/library/hh500420.aspx) (o [*Visual Studio*](https://www.visualstudio.com/es-es/visual-studio-homepage-vs.aspx)).

Para poder usar la herramienta *git-tfs* recomiendo el uso de [*Chocolatey*](https://chocolatey.org/).

**Chocolatey NuGet** es un **Machine Package Manager**, quiere decir, un gestor de paquetes para nuestro ordenador, capaz de instalar herramientas de manera sencilla y bien gestionada usando *Windows*. Para instalar *Chocolatey*, abre una consola de comandos **como administrador** e ingresa el siguiente comando:

```shell
C:\> @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

Reiniciamos la consola de comandos.

## Obteniendo **git-tfs**

Abrimos una consola de comandos y ejecutamos:

```shell
choco install gittfs
```

Una vez instalado, tienes que asegurarte de que la carpeta de **git-tfs** está incluida en la variable de entorno `PATH`. Puedes incluir la carpeta de manera temporal usando:

```shell
set PATH=%PATH%;%cd%\GitTfs\bin\Debug
```

## Usando *git-tfs*

### Ayuda

Como de costumbre, existe una sección de ayuda en la propia herramienta, basta con usar los siguientes comandos:

```shell
# Todos los comandos disponibles
git tfs help

# Mostrar un resumen de uso de uno de los comandos
git tfs help <comando>
```

### Listando nuestros proyectos y ramas

Dado que en nuestro servidor *TFS* pueden existir más de un proyecto, podemos usar el comando `list-remote-branches` para listar todos los proyectos disponibles y sus ramas.

```shell
# Si usamos Visual Studio Team Services (Visual Studio Online)
git tfs list-remote-braches http://[ACCOUNT].visualstudio.com/tfs/DefaultCollection

# Si usamos Visual Studio On-Premise
git tfs list-remote-braches http://tfs:8080/tfs/DefaultCollection
```

> A partir de este punto asumimos que se está usando *VSTS* por tanto usaremos siempre la dirección `http://[ACCOUNT].visualstudio.com/tfs/`, para usar *VS On-Premise* basta con cambiar las direcciones vistas a continuación por `http://tfs:8080/tfs/`

Lo que dará una salida por consola de comandos como la siguiente:

```shell
$/project/trunk [*]
|
+- $/project/branch1
|
+- $/project/branch2
|
+- $/project/branch3
|  |
|  +- $/project/branche3-1
|
+- $/project/git_central_repo


$/other_project/trunk [*]
|
+- $/other_project/b1
|
+- $/other_project/b2

Cloning root branches (marked by [*]) is recommended!

PS:if your branch is not listed here, perhaps you should convert the containing folder to a branch in TFS
```

Es posible que no encontremos el proyecto, o rama en el listado, esto es debido a que seguramente nuestra rama sea una carpeta y no una rama propiamente dicha.

### Convertir una carpeta a rama (opcional)

En el caso de no disponer de la rama que necesitamos, es muy sencillo [convertir una carpeta a una rama](https://www.visualstudio.com/es-es/docs/tfvc/branch-folders-files).

Desde *Visual Studio*, accedemos a *Source Control Explorer* de nuestro repositorio *TFVC*, en él veremos las diferentes carpetas que forman el proyecto. Solo tenemos que seleccionar la carpeta que queremos convertir en rama, hacer click derecho del ratón y seleccionar: `Branching and Merging > Convert to Branch`.

![Convertir a Rama](/assets/posts/es/migrando-tfvc-git/convertir-a-rama.png)

### Clonar solo la raíz

Es posible que solo necesitemos clonar la raíz por diferentes razones: que no nos interese el resto de ramas, que queramos migrar nuestro proyecto a un repositorio de *Git* libre de ramas muertas... Para ello, y siguiendo con el listado de proyectos y ramas antes mencionado, podemos usar el siguiente comando para generar un repositorio *Git* a partir del repositorio *TFS*:

```shell
git tfs clone http://[ACCOUNT].VisualStudio.com/tfs/DefaultCollection $/project/trunk . --branches=none
```

Este método no es oportuno si hemos realizado merges en nuestro repositorio *TFS*. Y deberemos usar el siguiente método.

### Clonar todo el historial

Si por otro lado, queremos clonar todo el repositorio *TFS* y generar un nuevo repositorio *Git* con todas las ramas y cambios disponibles, solo tenemos que usar el siguiente comando:

```shell
git tfs clone http://[ACCOUNT].VisualStudio.com/tfs/DefaultCollection $/project/trunk . --branches=all
```

Ambas opciones son lentas pero seguras, así que aprovecha para tomar un café.

Una vez creado nuestro repositorio *Git*, nos situamos en la carpeta creada para continuar con las operaciones.

### Limpiando los commits (opcional)

Podemos limpiar nuestro nuevo repositorio *Git* de cualquier impureza creada en la conversión y arrastrada del repositorio *TFS*.

Para limpiar todos los metadatos creados por *git-tfs* en los mensajes de los commit:

```shell
git filter-branch -f --msg-filter "sed 's/^git-tfs-id:.*$//g'" -- --all
```

Una vez resuelto, verificamos que todo está correcto en nuestro repositorio *Git* y borramos la carpeta `.git/refs/original`, lo que borrará todas las ramas antiguas o muertas.

Si queremos disponer de unos ids de cambios de una forma más “humana” y no la que generó *TFS* en su día, usaremos el siguiente comando:

```shell
git filter-branch -f --msg-filter "sed 's/^git-tfs-id:.*;C\([0-9]*\)$/Changeset:\1/g'" -- --all
```

### Subiendo los cambios a un repositorio remoto

Añade un repositorio remoto a tu repositorio local usando el repositorio *Git* central de tu elección:

```shell
git remote add origin https://github.com/user/project.git
```

A continuación, sube todos los cambios de todas las ramas creadas en el proceso:

```shell
git push --all origin
```

Hemos terminado.

## Conclusión

Con estos sencillos pasos habremos convertido nuestro repositorio *TFS* a un repositorio *Git* conservando todo el historial de cambios, todas las ramas, y toda la información de nuestro repositorio antiguo.

> NOTA: Los *WorkItems*/Tareas asociados a cada *checkin/commit* **no pueden ser convertidos** de *TFS* a *Git*, para ello, habría que manualmente modificar cada commit y añadir la información.

> NOTA: En este momento, *Visual Studio Team Services* trabaja de manera separada con los repositorios de tipo *Git* y *TFS*, y **requiere como mínimo uno de ellos una vez creados**. Es decir, si en el momento de convertir nuestro proyecto de *TFS* a *Git*, trabajamos en el mismo proyecto y ambos repositorios son subidos al mismo proyecto, dispondríamos de dos repositorios en el mismo proyecto, uno de *Git* y otro de *TFS*, al solo existir uno de cada tipo, sería imposible borrar el repositorio de *TFS* y trabajar solo con el de *Git*, teniendo que de algún modo limpiar o marcar que el repositorio *TFS* ha quedado obsoleto.

## Más información de git-tfs

- [Repositorio oficial](https://github.com/git-tfs/git-tfs).
- [Web oficial](http://git-tfs.com/).
- [Documentación oficial](https://github.com/git-tfs/git-tfs/tree/master/doc/).
