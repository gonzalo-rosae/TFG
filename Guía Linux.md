# TFG
Este repositorio ha sido creado para ilustrar la parte experimental y práctica de mi Trabajo Fin de Grado, de título «Implementación de un modelo de extracción de relaciones semánticas basado en modelos de lenguaje para el español».

## Instrucciones generales
Lo primero será crear el entorno, con las librerías y programas oportunos para poder ejecutar el script que creará el dataset. Los pasos a ejecutar vienen detallados en orden 1 a 1 en las secciones **«Creación del entorno en Linux»** e **«Instalación de las librerías necesarias»**.

A continuación, habrá que hacer algunas modificaciones al código para su correcto funcionamiento, como se detalla en **«Modificaciones al código»**, tanto en las librerías instaladas como en el propio repositorio clonado.

Finalmente, se muestran los dos comandos necesarios para crear el dataset en la sección **«Ejecución y creación del dataset»**.

*Nota:* En la instalación de las librerías hay un par de interacciones a través de la terminal que se enseñan en la sección del final de este archivo, llamada **«Interacciones»**.


## Creación del entorno en Linux

1) Instalar Miniconda
	+ Descargar paquete para Linux de versión Conda 23.5.0: https://docs.conda.io/en/latest/miniconda.html
	+ Abrir e instala con los parámetros de configuración por defecto (*)
	+ Abrir la terminal o consola de comandos

2) Definir variables de entorno (USUARIO y ENTORNO)
   	+ Nombre del usuario donde se ha instalado Conda → USUARIO
   	+ Nombre del entorno Conda que se creará en el siguiente paso → ENTORNO
   	
   	**Sintaxis:** export NOMBRE_VARIABLE=valor_variable

   	*Nota:* al cerrar la consola las variables desaparecen y hay que definirlas de nuevo
4) Crear entorno Python 3.7 y activarlo
	+ conda create --name $ENTORNO python=3.7
	+ Proceed ([y]/n)? y
	+ conda activate $ENTORNO

## Instalación de librerías necesarias

1) Instalación librerías torch (conda):
   <pre>
	conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c conda-forge
   </pre>
   **[INTERACCIÓN 1]***

2) Instalación librerías (pip):
   <pre>
	pip install transformers sentencepiece protobuf
   </pre>

3) Actualización paquetes a la última versión:
	<pre>
	pip install -U pip setuptools wheel
	</pre>

4) Instalación Rust:
   <pre>
   curl https://sh.rustup.rs -sSf | sh
   </pre>
   **[INTERACCIÓN 2]***

5) Recargar el entorno:
	<pre>
	source "$HOME/.cargo/env"
	</pre>

6) Instalación Cython:
	<pre>
	pip install Cython
	</pre>

7) Clonación repositorio cRocoDiLe:
	<pre>
	git clone https://github.com/Babelscape/crocodile.git
	</pre>

8) Desplazamiento a directorio cRocoDiLe:
	<pre>
	cd crocodile
	</pre>

9) Clonación Wikiextractor:
	<pre>
	git clone https://github.com/LittlePea13/wikiextractor.git
	</pre>
   
10) Instalación librerías cRocoDiLe (*requirements*):
	<pre>
	pip install -r requirements.txt
	</pre>


## Modificaciones al código

### Modificaciones al código repositorio:

Añadimos línea para parar la ejecución en caso de fallo:
<pre>
sed -i '8i\set -e' extract_lan.sh
</pre>

Añadimos argumento para que no falle al intentar crear una carpeta ya existente:
<pre>
sed -i 's/mkdir data\/$1/mkdir -p data\/$1/' extract_lan.sh
</pre>

Modificación creación índice para hacerlo condicional (y que solo se ejecute si no se ha creado ya el índice):
<pre>
sed -i "s#wikimapper create \$1wiki-latest --dumpdir data/\$1/ --target data/\$1/index_\$1wiki-latest.db#if [ ! -f \"data/\$1/index_\$1wiki-latest.db\" ]; then\n  wikimapper create \$1wiki-latest --dumpdir data/\$1/ --target data/\$1/index_\$1wiki-latest.db\nelse\n  echo \"INFO - Wikidata database file already exists, skipping creation step.\"\nfi#g" extract_lan.sh
</pre>

Descomentamos sentencia para decodificar línea leída:
<pre>
sed -i "s/output.write(line)#.encode('utf-8'))/output.write(line.encode('utf-8'))/g" wikiextractor/wikiextractor/WikiExtractor.py
</pre>

Y ahora sustituimos las apariciones de UTF-8 por latin1 (ISO-8859-1):
<pre>
sed -i 's/utf-8/iso-8859-1/g' wikiextractor/wikiextractor/WikiExtractor.py
</pre>

Arreglamos una línea que tiene que corregir el formato del último fichero de resúmenes de Wikipedia
<pre>
sed -i 's|echo "</data>" >> ls -1 text/$1/**/* | tail -1|echo "</data>" >> "$(ls -d text/*/ | sort -r | head -1)"\/"$(ls -1 "$(ls -d text/*/ | sort -r | head -1)" | sort -r | head -1)"|' extract_lan.sh
</pre>

### Modificaciones al código librería:
**wikimapper\download.py:{línea 52}**\
ADD:     wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"
<pre>
sed -i '52i\    wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"' /home/$USUARIO/miniconda3/envs/$ENTORNO/lib/python3.7/site-packages/wikimapper/download.py
</pre>

**wikimapper\download.py:{línea 54}**\
FROM:    for dump in [pages_dump, page_props_dump, redirects_dump]:\
  TO:    for dump in [pages_dump, page_props_dump, redirects_dump, wikipedia_dump]:
<pre>
sed -i 's/for dump in \[pages_dump, page_props_dump, redirects_dump\]:/for dump in \[pages_dump, page_props_dump, redirects_dump, wikipedia_dump\]:/' /home/$USUARIO/miniconda3/envs/$ENTORNO/lib/python3.7/site-packages/wikimapper/download.py
</pre>

**wikimapper\processor.py:{línea 117}**\
FROM: csv.field_size_limit(sys.maxsize)\
TO:   csv.field_size_limit(131071)
<pre>
sed -i 's/csv.field_size_limit(sys.maxsize)/csv.field_size_limit(131071)/' /home/$USUARIO/miniconda3/envs/$ENTORNO/lib/python3.7/site-packages/wikimapper/processor.py
</pre>


## Ejecución y creación del dataset
Primero descargamos los tripletes de Wikidata (paso más lento, el archivo es grande):
<pre>
bash startup.sh
</pre>

A continuación el script que se encarga de todo lo demás:
<pre>
bash extract_lan.sh es
</pre>
*Nota:* "es" es el código de idioma del español

## Interacciones

**[INTERACCIÓN 1]***
<pre>
The following packages will be SUPERSEDED by a higher-priority channel:
  ca-certificates    pkgs/main::ca-certificates-2023.05.30~ --> conda-forge::ca-certificates-2023.5.7-h56e8100_0
Proceed ([y]/n)?y
</pre>

**[INTERACCIÓN 2]***\
Puede que aparezca el siguiente mensaje:

<pre>
info: downloading installer
Warning: Not enforcing strong cipher suites for TLS, this is potentially less secure

Rust Visual C++ prerequisites

Rust requires a linker and Windows API libraries but they don't seem to be
available.

These components can be acquired through a Visual Studio installer.

1) Quick install via the Visual Studio Community installer
   (free for individuals, academic uses, and open source).

2) Manually install the prerequisites
   (for enterprise and advanced users).

3) Don't install the prerequisites
   (if you're targeting the GNU ABI).

>1

info: downloading Visual Studio installer
info: running the Visual Studio install
info: rustup will continue once Visual Studio installation is complete

warning: operation completed successfully, but install requires reboot before it can be used (exit code 3010)

Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  C:\Users\$USUARIO\.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  C:\Users\$USUARIO\.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  C:\Users\$USUARIO\.cargo\bin

This path will then be added to your PATH environment variable by
modifying the HKEY_CURRENT_USER/Environment/PATH registry key.

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-pc-windows-msvc
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes
</pre>

Y luego este otro mensaje aparece seguro:

<pre>
1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1
Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload its PATH environment variable to include
Cargo's bin directory ($USERPROFILE\.cargo\bin).

Press the Enter key to continue.
</pre>
