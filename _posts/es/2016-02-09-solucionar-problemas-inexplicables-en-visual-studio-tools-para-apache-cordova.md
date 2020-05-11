---
title: "Solucionar problemas inexplicables en Visual Studio Tools para Apache Cordova"
header:
  image: /assets/posts/es/solucionar-problemas-inexplicables-en-visual-studio-tools-para-apache-cordova/header.jpg
toc: false
classes: wide
tags: [ Español, Cordova, Visual Studio ]
categories: [ Cordova, Visual Studio ]
lang: es
ref: 2
permalink: /es/solucionar-problemas-inexplicables-en-visual-studio-tools-para-apache-cordova/
---

A veces, cuando trabajas con **Visual Studio Tools para Apache Cordova** puedes encontrar algunos problemas inexplicables. Estos problemas pueden surgir en el momento de la compilación, al abrir un proyecto, y suelen ser por extraños motivos que no logramos determinar.

Una posible solución a estos problemas, y que no acarrea problemas secundarios en caso de no funcionar, es el siguiente:

1. Asegura tener instalado y actualizado *Google Chrome*.
1. Borra la carpeta: `c:\Users\<username>\appdata\local\microsoft\visualstudio\14.0\componentmodelcache`
1. Abre *Visual Studio* y crea un nuevo proyecto *Cordova* en blanco. Esto regenerará la carpeta que acabamos de borrar.
1. Depura el proyecto creado en *Ripple*. Es posible que cause algún problema en un primer intento. Cierra el navegador e intenta compilar de nuevo, debería conectar sin mayor inconveniente.

![Resultado](/assets/posts/es/solucionar-problemas-inexplicables-en-visual-studio-tools-para-apache-cordova/resultado.png)

Con estos sencillos pasos solucionaremos muchos problemas y limpiaremos los recursos que se encuentran en memoria caché.
