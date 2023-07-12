# TFG
Este repositorio ha sido creado para ilustrar la parte experimental y práctica de mi Trabajo Fin de Grado, de título «Implementación de un modelo de extracción de relaciones semánticas basado en modelos de lenguaje para el español».


## Descripción de cada paso a ejecutar para la creación del entorno en Windows

1) Instalar Miniconda
	+ Descargar paquete para Windows de versión Conda 23.5.0 https://docs.conda.io/en/latest/miniconda.html
	+ Abrir e instala con los parámetros de configuración por defecto (*)
	+ Abrir la terminal de conda, llamado «Anaconda Prompt (miniconda3)»

	(*) También se puede marcar la opción de "añadir al PATH" para poder usar conda desde la terminal nativa (CMD)
2) Definir variables de entorno (USUARIO y ENTORNO)
   	+ Nombre del usuario donde se ha instalado Conda --> USUARIO
   	+ Nombre del entorno Conda que se creará en el siguiente paso --> ENTORNO
   	
   	*Sintaxis:* set NOMBRE_VARIABLE=valor_variable
3) Crear entorno Python 3.7 y activarlo
	+ conda create --name %ENTORNO% python=3.7
	+ Proceed ([y]/n)? y
	+ conda activate %ENTORNO%
4) Instalar Git y Bash (mismo paquete)
	+ Se puede consultar en: https://anaconda.org/conda-forge/git-bash
	+ conda install -c conda-forge git-bash
	+ Proceed ([y]/n)? y


## Instalación de librerías necesarias

1) Instalación librerías torch (conda):
   <pre>
	conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c conda-forge  
   </pre>
   **[INTERACCIÓN 1]**

2) Instalación librerías (pip):
   <pre>
	pip install transformers sentencepiece protobuf
   </pre>

3) Actualización paquetes a la última versión (!):
	<pre>
	pip install -U pip setuptools wheel
	</pre>
	+ pip: error
	  ERROR: To modify pip, please run the following command:
	  C:\Users\%USUARIO%\miniconda3\envs\%ENTORNO%\python.exe -m pip install -U pip setuptools wheel

4) Instalación Rust:
   <pre>
    curl https://sh.rustup.rs -sSf | sh 
   </pre>
   **[INTERACCIÓN 2]**

5) Cerrar y abrir la consola de nuevo, activando el entorno otra vez:
	<pre>
	conda activate %ENTORNO%
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

[?] Y ahora sustituimos las apariciones de UTF-8 por latin1 (ISO-8859-1):
<pre>
sed -i 's/utf-8/iso-8859-1/g' wikiextractor/wikiextractor/WikiExtractor.py
</pre>

### Modificaciones al código librería:
**wikimapper\download.py:{línea 52}**\
ADD:     wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"
<pre>
sed -i '52i\    wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"' C:\Users\%USUARIO%\miniconda3\envs\%ENTORNO%\Lib\site-packages\wikimapper\download.py
</pre>

**wikimapper\download.py:{línea 54}**\
FROM:    for dump in [pages_dump, page_props_dump, redirects_dump]:\
  TO:    for dump in [pages_dump, page_props_dump, redirects_dump, wikipedia_dump]:
<pre>
sed -i 's/for dump in \[pages_dump, page_props_dump, redirects_dump\]:/for dump in \[pages_dump, page_props_dump, redirects_dump, wikipedia_dump\]:/' C:\Users\%USUARIO%\miniconda3\envs\%ENTORNO%\Lib\site-packages\wikimapper\download.py
</pre>

**wikimapper\processor.py:{línea 117}**\
FROM: csv.field_size_limit(sys.maxsize)\
TO:   csv.field_size_limit(131071)
<pre>
sed -i 's/csv.field_size_limit(sys.maxsize)/csv.field_size_limit(131071)/' C:\Users\%USUARIO%\miniconda3\envs\%ENTORNO%\lib\site-packages\wikimapper\processor.py
</pre>


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

  C:\Users\%USUARIO%\.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  C:\Users\%USUARIO%\.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  C:\Users\%USUARIO%\.cargo\bin

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
Cargo's bin directory (%USERPROFILE%\.cargo\bin).

Press the Enter key to continue.
</pre>
