# TFG
Este repositorio ha sido creado para ilustrar la parte experimental y práctica de mi Trabajo Fin de Grado, de título «Implementación de un modelo de extracción de relaciones semánticas basado en modelos de lenguaje para el español».


## Descripción de cada paso a ejecutar para la creación del entorno

1) Instalar Conda/Anaconda/Miniconda
	+ Descargar paquete: https://docs.conda.io/en/latest/miniconda.html
	+ Abrir e instalar (marcando opción de añadir al PATH {desactivada por defecto})
	+ Abrir terminal (la normal o la de conda) → igual no hace falta lo de path si se abre la terminal conda...
2) Crear entorno Python 3.7 (esta versión usaremos por defecto) y activarlo
	+ conda create --name NOMBRE_ENTORNO python=3.7
	+ Proceed ([y]/n)? y
	+ conda activate NOMBRE_ENTORNO
3) Si estamos en Windows también hay que instalar Bash
	+ Se puede consultar en: https://anaconda.org/conda-forge/git-bash
	+ conda install -c conda-forge git-bash
	+ Proceed ([y]/n)? y


## Instalación de librerías necesarias

1) conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c conda-forge
	 [INTERACCIÓN 1]

2) pip install transformers sentencepiece protobuf

3) pip install -U pip setuptools wheel
	+ setuptools y wheel: requirement already satisfied
	+ pip: error
	  ERROR: To modify pip, please run the following command:
	  C:\Users\gonza\miniconda3\envs\crocodile37\python.exe -m pip install -U pip setuptools wheel

4) curl https://sh.rustup.rs -sSf | sh
	 [INTERACCIÓN 2]

En Linux
5) source "$HOME/.cargo/env"
En Windows
5) cerrar y abrir la consola

6) pip install Cython

Moverse hasta la raíz del directorio CROCODILE si no se está ya
7) pip install -r requirements.txt


## Modificaciones al código

### Modificaciones al código librería:
C:\Users\gonza\miniconda3\envs\crocodile37\lib\site-packages\wikimapper\processor.py:{línea 117}
FROM: csv.field_size_limit(sys.maxsize)
TO:   csv.field_size_limit(131071)
o
sed -i 's/csv.field_size_limit(sys.maxsize)/csv.field_size_limit(131071)/' C:\Users\gonza\miniconda3\envs\crocodile37\lib\site-packages\wikimapper\processor.py

### Modificaciones al código repositorio:
- Rutas
- Requirements
- Fichero dump Wikipedia XML
C:\Users\gonza\miniconda3\envs\crocodile37\Lib\site-packages\wikimapper\download.py:{línea 52}
ADD:     wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"
o
sed -i '52i\wikipedia_dump = dumpname + "-pages-articles-multistream.xml.bz2"' C:\Users\gonza\miniconda3\envs\crocodile37\Lib\site-packages\wikimapper\download.py

C:\Users\gonza\miniconda3\envs\crocodile37\Lib\site-packages\wikimapper\download.py:{línea 54}
FROM:    for dump in [pages_dump, page_props_dump, redirects_dump]:
  TO:    for dump in [pages_dump, page_props_dump, redirects_dump, wikipedia_dump]:
o
sed -i 's/for dump in \[pages_dump, page_props_dump, redirects_dump\]:/for dump in \[pages_dump, page_props_dump, redirects_dump, wikipedia_dump\]:/' C:\Users\gonza\miniconda3\envs\crocodile37\Lib\site-packages\wikimapper\download.py

## Interacciones

[INTERACCIÓN 1]*
The following packages will be SUPERSEDED by a higher-priority channel:
  ca-certificates    pkgs/main::ca-certificates-2023.05.30~ --> conda-forge::ca-certificates-2023.5.7-h56e8100_0
Proceed ([y]/n)?y


[INTERACCIÓN 2]*
- Puede que aparezca el siguiente mensaje (me apareció la primera vez):

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

  C:\Users\gonza\.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  C:\Users\gonza\.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  C:\Users\gonza\.cargo\bin

This path will then be added to your PATH environment variable by
modifying the HKEY_CURRENT_USER/Environment/PATH registry key.

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-pc-windows-msvc
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes


- Y luego esto aparece seguro:

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1
Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload its PATH environment variable to include
Cargo's bin directory (%USERPROFILE%\.cargo\bin).

Press the Enter key to continue.
