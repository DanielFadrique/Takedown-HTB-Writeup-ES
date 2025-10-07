# Takedown HTB Writeup ES
Writeup del sherlock retirado Takedown de la plataforma HackThebox, el cual se centra en el dominio del análisis de paquetes de red ante un posible equipo comprometido. Este writeup es una traducción directa en español del material oficial, el cual se encuentra en inglés

-----------------

![/img/Pasted image 20251005151439.png | 1080](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251005151439.png)

El laboratorio Sherlock **Takedown** de la plataforma **HackTheBox**, es un laboratorio que se encarga de poner a prueba las hablidades del jugador en tareas sobre el analisis de redes y malware, una practica muy buena y versatil para poder ir viendo todo el espectro de un *Cyber Kill Chain*.

Pero sin mas dilacion, empecemos con la resolucion del Sherlock:

-------------------

# Descripción y primer analisis

---------------------
## Descripción

-----------------

Vamos a leer el extracto que nos deja la pagina para entender a que nos enfrentamos:

```
We've identified an unusual pattern in our network activity, indicating a possible security breach. Our team suspects an unauthorized
intrusion into our systems, potentially compromising sensitive data. Your task is to investigate this incident.
```

Vamos a traducirlo al español para ver que nos quieren decir:

```
Hemos identificado un patron inusual en nuestra actividad de red, indicando una posible brecha de seguridad. Nuestro equipo sospecha 
de una intrusion no autorizada en nuestros sistemas, que potencialmente compromete datos muy sensibles. Tu tarea es investigar este 
incidente.
```

-------------

## Primer analisis

-------------

Nos dan un archivo _.zip_ para poder comenzar la investigacion asi que vamos a darle un repaso rapido.

Primero nos descargaremos el _.zip_ y listaremos los contenidos:

![/img/Foto_1.jpg](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Foto_1.jpg)
Como podemos observar dentro del *.zip* vemos un archivo *.pcap*, un tipo de archivo que recoge la actividad de red durante un tiempo determinado, estos archivos se pueden abrir con el software que vamos a utilizar, **Wireshark**.

------------------

# Preguntas y resolución del laboratorio

Las preguntas que nos hacen para completar en este laboratorio son las siguientes:

## 1. ¿Desde que dominio se ha descargado el script VBS?
-----------------

Abramos inicialmente el *.pcap* y empecemos a revisar de primera instancia que nos encontramos:

Vamos a buscar por los paquetes **SMB2** para poder ver si podemos encontrar algo interesante:

![/img/Pasted image 20251006184835.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006184835.png)
Como podemos ver de toda esa traza de paquetes podemos observar que un equipo esta intentando acceder a un servidor via **SMB2**, mandando un *NEGOTIATION Request* para poder acceder a un recurso compartido en **IPC$**, llamado **AZURE_DOC_OPEN.vbs**, creando el archivo correctamente.

El nombre del dominio lo podemos ver gracias a las dirección de destino de Wireshark

#### **Respuesta:**  *escuelademarina.com*

------------------
## 2. ¿Cual es la direccion IP asociado con la pregunta #1 usado para el ataque?

Aunque en lo personal tengo habilitado la opcion de resolucion de IPs en Wireshark, podemos encontrar esta respuesta por otra parte, si entramos a *Estadisticas* -> *Direcciones resueltas* nos aparecera la siguiente ventana:

![/img/Pasted image 20251006190002.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006190002.png)
Desde aqui podemos filtrar por la respuesta anterior: *escuelademarina.com* y ver que IP contiene:

![/img/Pasted image 20251006190047.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006190047.png)
Y ahi mismo encontramos nuestra respuesta de la pregunta:

#### **Respuesta:**  *165.22.16.55*

-------------------
## 3. ¿Cual es el nombre del archivo del VBS usado para el acceso inicial?

Gracias a la pequeña investigación obtenida en la pregutna #1 podemos ver el nombre del archivo VBS el cual es:

![/img/Pasted image 20251006190232.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006190232.png)
#### **Respuesta:**  *AZURE_DOC_OPEN.vbs*

--------------
## 4. ¿Cual ha sido la URL usada para conseguir el script de PowerShell?

Para completar esta pregunta, vamos a guardarnos el archivo VBS:

![/img/Pasted image 20251006191808.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006191808.png)
Y una vez guardado, vamos a ver que contiene con el comando *strings*, viendo al final del mismo, un objeto **PowerShell** y una **URL**:

![/img/Foto_2.jpg](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Foto_2.jpg)
#### **Respuesta:**  *badbutperfect[.]com/nrwncpwo* 

--------------
## 5. ¿Cual es el binario legitimo que se ha descargado en la maquina victima?

Para estas siguientes preguntas podemos seguir la secuencia de **HTTPs** en el *.pcap*.

![/img/Pasted image 20251006192748.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006192748.png)
Nos encontramos con un **GET** del archivo que se nos menciona en la *URL* de la pregunta anterior, *nrwncpwo*

![/img/Pasted image 20251006192845.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006192845.png)
Vamos a seguir la secuencia a ver que información nos da sobre lo que se ejecuta:

![/img/Foto_3.jpg| 2080](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Foto_3.jpg| 2080)
Desde ahi, podemos ver que se esta intentando nombrar un *.exe* a AutoHotKey, vamos a investigar en internet sobre dicho nombre del .*exe*:

![/img/Pasted image 20251006193442.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006193442.png)
Y si, parece un binario legitimo segun lo que pone en la descripción de la pagina: *AutoHotKey es un lenguaje gratis de open-source para Windows que permite a los usuarios crear scripts simples a scripts complejos para todo tipo de tareas como:...*

#### **Respuesta:**  *AutoHotKey*

------------------
## 6. ¿Desde que URL se ha usado el malware con el binario de la pregunta #5?

Viendo un poco mas en la respuesta del servidor, encontraremos una URL:

![/img/Pasted image 20251007113218.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251007113218.png)
#### **Respuesta:**  *http[:]//badbutperfect[.]com/jvtobaqj* 

--------------
## 7. ¿Que nombre de archivo era el malware de la pregunta #6 en el disco?

Justo podemos verlo después de la **URL** de la pregunta #6 podemos ver el nombre del malware:

![/img/Pasted image 20251006194211.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006194211.png)
#### **Respuesta:**  *script.ahk*

## 8. ¿Cual es el TLSH del malware?

Vamos a guardar el archivo de la pregunta #6, *jvtobaqj* y lo pasaremos por **Virustotal**, ya que es el malware que nos mencionan:

![/img/Pasted image 20251006194635.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006194635.png)
#### **Respuesta:**  *T15E430A36DBC5202AD8E3074270096562FE7DC0215B4B32659C9EF16835CF6FF9B6A1B8*

---------------------
## 9. ¿Cual es el nombre del malware? Usa el nombre otorgado por McAfee Ikarus, y alejandro.sanchez.

Dentro de Virustotal, en la pestaña de *Detection*, buscaremos como lo cataloga uno de los antivirus mencionados en la pregunta:

![/img/Pasted image 20251006194850.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006194850.png)
#### **Respuesta:**  *DarkGate*

------------------
## 10. ¿Cual es el string de usuario-agente de la maquina infectada?

Durante todo este tiempo, la IP: *10.3.19.101* ha sido la maquina victima, para poder intentar ver el usuario-agente de dicha maquina tendremos que hacer un pequeño filtro de la IP con comunicaciones HTTP: 

![/img/Pasted image 20251006202940.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006202940.png)
Como podemos ver, la IP victima hace muchas peticiones **POST** a un servidor, vamos a investigar una de ellas:

![/img/Pasted image 20251006203133.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006203133.png)
Ahí nos podemos encontrar al **User-agent**(*usuario-agente*) de la maquina victima:

#### **Respuesta:**  *Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36*

-------------
## 11. ¿Hacia que IP se conecta de la pregunta anterior a traves del RAT(Remote Access Trojan)?

Como hemos visto en la pregunta #10 tenemos una IP que su resolucion de nombre nos da: *badbutperfect.com*, vamos a buscar su IP en *Direcciones resueltas* como hicimos en la pregunta #2:

![/img/Pasted image 20251006203454.png](https://github.com/DanielFadrique/Takedown-HTB-Writeup-ES/blob/main/img/Pasted%20image%2020251006203454.png)
#### **Respuesta:**  *103.124.105.78*

------------------

*Daniel Fadrique #2025*

