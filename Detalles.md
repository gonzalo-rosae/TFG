Se ha creado este fichero con el propósito de hacer más legible y fácil de entender el proceso actual en el que se encuentra el *script* extract_lan.sh durante su ejecución.
Estos cambios son solo para los mensajes, nada funcional. Modifican cada uno de los mensajes titulares que anuncian un nuevo paso metodológico.

<pre>
sed 's/echo "Downloading wikipedia and wikidata dumps..."/echo -e "\\n[1] Downloading wikipedia and wikidata dumps..."/' extract_lan.sh
sed 's/echo "Create wikidata database"/echo -e "\\n[2] Create wikidata database"/' extract_lan.sh
sed 's/echo "Extract abstracts"/echo -e "\\n[3] Extract abstracts"/' extract_lan.sh
sed 's/echo "Fix first and last file: "/echo -e "\\n[4] Fix first and last file: "/' extract_lan.sh
sed 's/echo "Create triplets db"/echo -e "\\n[5] Create triplets db"/' extract_lan.sh
sed 's/echo "Extract triplets to out\/$1"/echo -e "\\n[6] Extract triplets to out\/$1"/' extract_lan.sh
sed 's/echo "Clean triplets to out_clean\/$1"/echo -e "\\n[7] Clean triplets to out_clean\/$1"/' extract_lan.sh
</pre>
