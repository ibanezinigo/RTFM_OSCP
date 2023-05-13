```
wfuzz -c --hc=404 -z file,/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://192.168.1.X/FUZZ
```
Para tener un caso práctico, supongamos que tenemos un directorio **/design** que nos devuelve un Forbidden. Algo que podemos hacer es configurar una enumeración de doble Payload desde wfuzz a fin de descubrir recursos existentes bajo dicho directorio.

Para ello, nos creamos un fichero _extensions.txt_ con el siguiente contenido:
php
txt
html
xml
cgi

Posteriormente, hacemos uso de Wfuzz siguiendo la siguiente sintaxis:

```
wfuzz -c --hc=404 -z file,/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -z file,extensions http://192.168.1.X/design/FUZZ.FUZ2Z
```


https://wfuzz.readthedocs.io/en/latest/user/basicusage.html