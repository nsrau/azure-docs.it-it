
I limiti seguenti si applicano ai Backup di Azure.

| Identificatore limite | Limite predefinito |
|---|---|
|Numero di server/macchine che possono essere registrati con ogni insieme di credenziali|50|
|Dimensioni di un'origine dati per i dati archiviati nel servizio di archiviazione Azure insieme di credenziali|1,65 max TB<sup>1</sup>|
|Numero di insiemi di credenziali di backup creati in ogni sottoscrizione di Azure|25|
|Numero di volte in cui è possibile pianificare backup al giorno|3 al giorno per Windows Server/Client<br/>2 al giorno per SCDPM|
|Numero di punti di ripristino che possono essere creati|366<sup>2</sup>|
|Dischi dati collegati a una macchina virtuale di Azure per il backup|5|

- <sup>1</sup>il limite di 1,65 TB non si applica ai backup VM IaaS.
- <sup>2</sup>È possibile usare qualsiasi permutazione per arrivare a un numero inferiore a 366.

<!---HONumber=August15_HO6-->