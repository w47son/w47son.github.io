---
title: 'Análisis de documentos maliciosos - MALDOCS'
date: 2023-04-17 11:00:00 +0200
categories: [Tutorial,Malware]
tags: [Tutorial,MalDocs,BlueTeam, Malware]
image:
  path: /assets/img/commons/maldocs-analysis/maliciousWord.jpg
  width: 400
  height: 300
---

Los **documentos maliciosos** tipo **Word**, **Excel** o incluso **PDF** se propagan la mayoría de ellos por **email**. Por lo que tener una buena medida de *email security* es esencial pero hay algunos archivos que se saltarán esas restricciones.
<br/>
<br/>
En algún momento alguien te habrá enviado un documento muy sospechoso el cual te gustaría analizar. Para esos casos viene bien hacerlo no solo con el *antivirus* sino con *otras herramientas*.
<br/>
En este caso, vamos a **analizar** los documentos **DOCM** o **DOTM** que contienen **Macros** (La M de DOCM es de macros)

Para poner un ejemplo practico, vamos a tomar este challenge de *CyberDefenders*

<a href="https://cyberdefenders.org/blueteam-ctf-challenges/51" target="_blank">Descargar DOCM Malicioso</a>


<br/>
Los primeros pasos a tener en cuenta son:

## 1 - Poseer un laboratorio de Análisis de Malware
Para empezar siempre un Análisis de Malware, debes disponer de una máquina para ello. Puedes conseguir hacerte con la tuya aquí: <a href="https://w47son.github.io/posts/malware-analysis-lab/" target="_blank">Configurar laboratorios para Análisis de Malware</a>

**Con REMNUX podrás tener todas las herramientas necesarias.**


## 2 - Instalación de programas necesarios



**LibreOffice** (Word, Excel, etc.)
<a href="https://www.libreoffice.org/download/download-libreoffice/" target="_blank">https://www.libreoffice.org/</a>
<br/>
<br/>

*En el caso de que no instales REMNUX debes descargar estas herramientas*

**Oletools** 
```bash
git clone https://github.com/decalage2/oletools.git
```

**Oledump**
```bash
wget https://raw.githubusercontent.com/DidierStevens/DidierStevensSuite/master/oledump.py
```


## 3 - Herramientas online a tener en cuenta

<a href="https://gchq.github.io/CyberChef/" target="_blank">CyberChef</a> para desofuscar

<a href="https://app.any.run/" target="_blank">Any.Run</a> realiza un análisis dinámico

<a href="https://www.virustotal.com/gui/home/search" target="_blank">VirusTotal</a> revisión en mas de 60 antivirus

--------

Después de descargar el documento, procedemos a hacerle un análisis de metadatos con **olemeta** (también podríamos hacerlo con *exiftool*) y ver su hash para luego poder mandarlo any.run o virustotal con el comando <br/>```md5sum sample.bin```

![Resultado Olemeta]({{ 'assets/img/commons/maldocs-analysis/olemeta.png' | relative_url }}){: width="450" height="650" .center-image }
<br/>
Podemos observar que su nombre original es 'Normal.dotm', se ha editado 1 vez, la edición no ha durado ni 1 minuto, tiene 1 página y 3 palabras. Con esto podemos sospechar bastante.
<br/>
<br/>

Ejecutamos **oleid** en el archivo para obtener más información y obtendremos que tiene Macros este DOTM

![Resultado Oleid]({{ 'assets/img/commons/maldocs-analysis/oleid.png' | relative_url }}){: width="550" height="650" .center-image }

<br/>

*Podríamos saltarnos estos pasos anteriores y analizar el documento en busca de todos los macros posibles pero no entenderíamos a qué nos enfrentamos*

Una vez sabemos que tiene macros, lanzamos **oledump**

![Resultado Oledump]({{ 'assets/img/commons/maldocs-analysis/oledump.png' | relative_url }}){: width="450" height="450" .center-image }

Aquí podemos ver que en el stream *13* es un macro 'vba' que tiene un tamaño de *1367* y se encuentra en *'Macros/VBA/diakzouxchouz'*

Con el parametro '-s' elegimos el stream que queremos seleccionar y '-v' para hacer un vba decompression. En el stream 13 vemos que se utiliza la función **document_open()** que se ejecuta al abrir el archivo.

<br/>

Para ver todos los Macros de una vez podemos utilizar **olevba**

![Resultado Olevba1]({{ 'assets/img/commons/maldocs-analysis/olevba1.png' | relative_url }}){: width="450" height="450" .center-image }

Aquí nos muestra todos los Macros y su contenido. Como podemos ver, nos señala **document_open()** como algo importante a tener en cuenta.
<br/>
<br/>

![Resultado Olevba2]({{ 'assets/img/commons/maldocs-analysis/olevba2.png' | relative_url }}){: width="450" height="450" .center-image }

En la parte final nos da una descripción de todo lo que ha podido encontrar, como un **base64**.

<br/>

Ese base64 pertenece al *'Macros/roubhaol/i09/o'* en el que vemos que se repite mucho una serie de caracteres.

![Resultado base64]({{ 'assets/img/commons/maldocs-analysis/base64.png' | relative_url }}){: width="1050" height="1050" .center-image }

<br/>

Después de encontrar el patrón lo copiamos todo y lo llevamos a **CyberChef**

![Resultado cyberchef]({{ 'assets/img/commons/maldocs-analysis/cyberchef.png' | relative_url }}){: width="1050" height="1050" .center-image }

Reemplazamos **2342772g3&\*gs7712ffvs626fq** (*si analizamos las macros podríamos saber que reemplaza este string*), quitamos los null bytes, reemplazamos el string 'powershell -e' y obtenemos el base64. Con un poco de esfuerzo y borrando más, podemos ver como se comporta este malware. 
<br/>
```El malware descarga un archivo de estas páginas que renombra al directorio C:\Users\usuario\337.exe```
<br/>

----
<br/>

Para analizarlo en **LibreOffice** (deberías deshabilitar la ejecución de macros antes), vamos a *'Tools'* y a *'Edit Macros'* 

![Resultado libreoffice1]({{ 'assets/img/commons/maldocs-analysis/libreoffice1.png' | relative_url }}){: width="1050" height="1050" .center-image }

<br/>
Viendo las otras herramientas sabemos que son mejores pero aquí podríamos **debuggearlo** y **ver formularios**

![Resultado libreoffice2]({{ 'assets/img/commons/maldocs-analysis/libreoffice2.png' | relative_url }}){: width="1050" height="1050" .center-image }


----

<br/>
Si hemos sacado el md5 del archivo y lo hemos buscado en **any.run** veremos que ese mismo script de powershell se habría ejecutado.
<br/>

![Resultado anyrun]({{ 'assets/img/commons/maldocs-analysis/anyrun.png' | relative_url }}){: width="1050" height="1050" .center-image }
_<a href="https://app.any.run/tasks/ea5283ab-70c2-453e-a7fc-9a99fa085e84/" target="_blank">Análisis en any.run</a>_

