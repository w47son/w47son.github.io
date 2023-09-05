---
title: 'Detección Avanzada: EDR Aurora + Sigma Rules'
date: 2023-09-05 17:00:00 +0200
categories: [Tutorial,Malware]
tags: [Malware,Aurora,EDR]
image:
  path: /assets/img/commons/aurora-edr-detection/aurora_logo.jpg

---

Analizar, detectar y erradicar amenazas es clave a la hora de salvar a tu compañía o en el momento de investigar un posible malware en un entorno controlado. Por ello, vamos a ver que nos puede ofrecer el <a href="https://www.nextron-systems.com/aurora/" target="_blank">EDR AURORA</a>:

## ¿Qué es AURORA?
![DiagramaAurora]({{ 'assets/img/commons/aurora-edr-detection/diagrama.png' | relative_url }}){: width="550" height="550" .w-50 .right }
El Agente AURORA es un agente **EDR ligero** y personalizable basado en <a href="https://github.com/SigmaHQ/sigma" target="_blank">**Sigma**</a>. Utiliza **Event Tracing for Windows** (ETW) y les aplica reglas Sigma e IOC. Aurora activa "acciones de respuesta" que se mostrarán en el Visor de registros de eventos de Windows.

Es personalizable por el conjunto de reglas abiertas de Sigma, puede configurarse para usar una cantidad limitada de recursos y ahorra ancho de banda de la red y almacenamiento, ya que funciona on-premise y ningún dato sale de la red.

<br/>
<br/>
<br/>

## Diferencias con Sysmon


![SysmonDiff]({{ 'assets/img/commons/aurora-edr-detection/sysmon.png' | relative_url }}){: width="850" height="550" }

- AURORA **lee datos de muchos canales ETW diferentes** y los enriquece con información en tiempo real para recrear eventos muy similares a los generados por Sysmon.
- El **volumen de registro relativo es muy bajo**, ya que AURORA sólo envía los eventos en los que **se activó una regla Sigma**
- AURORA **admite diferentes canales de salida**: Registro de eventos de Windows, archivo de registro, objetivos UDP/TCP
- No requiere ningún controlador de kernel adicional y eso supone un **riesgo limitado** para la estabilidad del sistema


## Aurora Lite

Estaremos utilizando Aurora Lite, por lo que hay que tener en cuenta las diferencias con la versión completa

![AuroraDiff]({{ 'assets/img/commons/aurora-edr-detection/auroradiff.png' | relative_url }}){: width="550" height="550"}

Debido a que Aurora Lite es bastante limitado y tiene la **respuesta de acciones limitada a solamente 5 reglas**, realmente no nos serviría este EDR excepto, que sea pagando.

*Aurora es compatible con Windows 7/ Windows Server 2012 o versiones más recientes y debe ejecutarse con privilegios de administrador. También debe excluirse de cualquier solución antivirus o EDR en ejecución. Esto es para evitar que la aplicación sea bloqueada en la ejecución de sus servicios.*


## <a href="https://www.nextron-systems.com/aurora/#get-aurora" target="_blank">Descarga</a>
*Necesitarás poner un email donde recibir la **licencia** ya que es necesaria, además del software.*

## Configuraciones

Antes de instalar, hay que tener claro los diferentes tipos de configuraciones que tiene Aurora, *desde más demandante a menos pero con algunas funciones desactivadas.*

![Configuraciones]({{ 'assets/img/commons/aurora-edr-detection/configuraciones.png' | relative_url }}){: width="550" height="550"}

- **Mínimo:** Esta configuración examina eventos considerados como mínimo de nivel de informe alto.
- **Reducido:** Esta configuración examina eventos considerados como mínimo de nivel de informe alto.
- **Estándar:** Esta configuración abarca eventos de nivel medio de gravedad.
- **Intenso:** Esta configuración examina eventos considerados como mínimo de nivel de informe bajo.


## Instalación

Una vez tengas la licencia y el software, juntamos ambos en una nueva carpeta.


![AuroraCarpeta]({{ 'assets/img/commons/aurora-edr-detection/auroraCarpeta.png' | relative_url }}){: width="850" height="550" .w-50 .right  }

Una vez extraído el zip, podemos ver las siguientes carpetas importantes:
- **Custom-signatures**, para poder añadir nuestras propias reglas sigma.
- **Signatures**, que son reglas ya establecidas y vienen muchos tipos para identificar malware conocidos y desconocidos.
- **agent-config-xxxxx.yml**, son los diferentes tipos de configuraciones que hay para aurora.
- **aurora-agent-64.exe**, ejecutable de versiones x86 y x64.
- **aurora-lite-id-fechaInicio-fechaFinal.lic**, que contiene la licencia y muestra el inicio y final de esta.

<br/>

**En mi caso**, voy a elegir la configuración intense ya que no quiero tener ningún modulo ni fuente desactivado porque pretendo registrar todo lo posible para mi *maquina de análisis de malware*.

Abrimos una **consola en administrador** y lo instalamos con su config.

Para hacer una instalación **sin reglas o iocs custom** se puede con el siguiente comando (**recomendado**)

```powershell
C:\Aurora>aurora-agent-64.exe --install -c agent-config-intense.yml
```

## Instalación con reglas CUSTOM

Las reglas e iocs custom se puede añadir después de la instalación normal pero si quieres añadirlas antes, así es como se haría.

En la carpeta custom nos ofrecen templates para iocs que podemos modificar. (También podríamos añadir reglas sigma en la carpeta sigma-rules)

![customSignature]({{ 'assets/img/commons/aurora-edr-detection/customSignature.png' | relative_url }}){: width="550" height="550"  }

Si quieres **con reglas e iocs custom debes especificar** --rules-path o --ioc-path y con ello si deseas utilizar las **reglas integradas y los IOC de Aurora**, deberás proporcionarlo también.
```powershell
C:\Aurora>aurora-agent-64.exe --install -c agent-config-intense.yml --rules-path .\signatures\sigma-rules --ioc-path .\signatures\iocs --rules-path .\custom-signatures\sigma-rules --ioc-path .\custom-signatures\iocs
```

![customPath]({{ 'assets/img/commons/aurora-edr-detection/customPath.png' | relative_url }}){: width="550" height="550"  }


Si añades reglas después de la instalación en la carpeta ``C:\Program Files\Aurora-Agent\custom-signatures``, debemos **parar el servicio e iniciarlo** para que se efectúen.

![ServicioAurora]({{ 'assets/img/commons/aurora-edr-detection/servicioAurora.png' | relative_url }}){: width="550" height="550"  }


Después de la instalación, se puede modificar la configuración del agente por si quieres añadir más rutas de reglas, cambiar la configuración de aurora o modificar el log size.

![agentConfig]({{ 'assets/img/commons/aurora-edr-detection/agentConfig.png' | relative_url }}){: width="550" height="550"  }



## Uso del EDR

Para ver el estado actual de aurora, tenemos que ejecutar ``aurora-agent-64.exe --status`` y veremos que hay 2395 reglas activadas y bastantes IOC cargados.

![statusAurora]({{ 'assets/img/commons/aurora-edr-detection/status.png' | relative_url }}){: width="650" height="450" } 


Para ponerlo en práctica, vamos a ejecutar ``whoami /priv`` y nos iremos al visor de eventos-Aplicación y filtramos por Aurora:

![eventViewer]({{ 'assets/img/commons/aurora-edr-detection/eventviewer.png' | relative_url }}){: width="750" height="450" } 

Tenemos sus detalles del por qué se activó:

![eventViewerDetalles]({{ 'assets/img/commons/aurora-edr-detection/detalleseventviewer.png' | relative_url }}){: width="750" height="450" } 

Aquí se ve haciendo uso del customIoc que añadí en el paso de instalación con reglas custom

![cobaltStrike]({{ 'assets/img/commons/aurora-edr-detection/cobaltstrike.png' | relative_url }}){: width="750" height="450" } 

## Response actions

Para activar las <a href="https://aurora-agent-manual.nextron-systems.com/en/latest/usage/responses.html" target="_blank">responses actions</a>, podíamos hacerlo a la hora de instalar con ``aurora-agent-64.exe --install --response-set .\response-sets\response.yml --activate-responses`` o después con el agentConfig.yml

Podemos añadir una custom rule para utilizarla después como action response
![responseConfig]({{ 'assets/img/commons/aurora-edr-detection/response1.png' | relative_url }}){: width="550" height="550" } 

El único ejemplo response-sets preset que hay es esta:

![responsePreset]({{ 'assets/img/commons/aurora-edr-detection/response2.png' | relative_url }}){: width="550" height="550" } 

Vamos a modificar la regla preset y lo añadimos a ``C:\Program Files\Aurora-Agent\response-sets``:

![responseCustom]({{ 'assets/img/commons/aurora-edr-detection/response3.png' | relative_url }}){: width="550" height="550" } 

Hay que modificar el **agentConfig.yml** y en la parte de **response-sets** poner el yml que modificamos.
Si activamos el **activate-reponses** a *True*, en yml actuará y **ejecutará esa acción**(*evento 6000*), en cambio, si lo ponemos a *False* actuará como una **respuesta simulada**, quiere decir que quedará registrada pero no se ejecutará (*evento 6001*).
![agentConfig]({{ 'assets/img/commons/aurora-edr-detection/response4.png' | relative_url }}){: width="550" height="550" } 

Al ejecutar el malware de cobalt strike, lo detectó y actuó:

![responseActivated]({{ 'assets/img/commons/aurora-edr-detection/response5.png' | relative_url }}){: width="650" height="550" } 

En la siguiente imagen, utilicé el preset de ransomware que venia como ejemplo y dejó mucho que desear, ya que no actuó porque se ejecutó como administrador(aunque el mismo aurora estaba siendo ejecutado como administrador):

![wannacry]({{ 'assets/img/commons/aurora-edr-detection/wannacry.png' | relative_url }}){: width="550" height="550" } 

## Conclusión

Aurora Lite merece la pena por las sigma rules porque su gestión de response no me acabó de convencer. 

Sus más de 2000 reglas que vienen ya incluidas son realmente buenas para poder crear nuevas reglas de respuesta en un entorno de análisis de malware y para un entorno personal, con la configuración mínima es interesante observar que reglas se detectan aunque no tenga una respuesta.

Lo veo más un software de detección en vez de un EDR, ya que no hemos probado la versión premium y me gustaría que hubieran más presets de respuestas aunque aún así está limitado.

Pese a todo, estas reglas sigma dan información de mejor calidad a un SIEM que solo sysmon. Por lo que un conjunto de *sysmon y aurora*, sería interesante implementarlo.

Para mí, es un *must* a tener en un *Windows Malware Analysis*

**Aurora EDR Lite 7,5/10**