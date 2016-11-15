Per determinare la versione di Python da usare per l'ambiente virtuale di Python, viene applicata la seguente priorità:

1. versione specificata nel file runtime.txt nella cartella radice
2. versione specificata dall'impostazione di Python nella configurazione dell'app Web (pannello **Impostazioni** > **Impostazioni applicazione** dell'app Web nel portale di Azure)
3. Se non viene specificata nessuna delle versioni sopra indicate, verrà usata quella predefinita, ovvero python-2.7.

I valori validi per il contenuto di 

    \runtime.txt

sono:

* python-2.7
* python-3.4

L'eventuale microversione (terza cifra) specificata verrà ignorata.



<!--HONumber=Nov16_HO2-->


