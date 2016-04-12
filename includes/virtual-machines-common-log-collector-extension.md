
Per diagnosticare i problemi con un servizio cloud di Microsoft Azure è necessario raccogliere i file di log del servizio nelle macchine virtuali non appena si verificano i problemi. È possibile usare l'estensione AzureLogCollector su richiesta per eseguire una raccolta occasionale di log da una o più macchine virtuali del servizio cloud (da ruoli Web e ruoli di lavoro) e trasferire i file raccolti in un account di archiviazione di Azure, senza accedere in modalità remota ad alcuna macchina virtuale.

> [AZURE.NOTE]Le descrizioni per la maggior parte delle informazioni registrate sono disponibili all'indirizzo http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Sono disponibili due modalità di raccolta a seconda dei tipi di file da raccogliere.
- Solo log di agenti guest di Azure (GA). Questa modalità di raccolta include tutti i log relativi agli agenti guest di Azure e ad altri componenti di Azure.
- Tutti i log (Completa). Questa modalità di raccolta raccoglierà tutti i file in modalità Agenti guest, oltre a:

  - log eventi di sistema e dell'applicazione
  
  - log degli errori HTTP
  
  - log di IIS
  
  - log di installazione
  
  - altri log di sistema

In entrambe le modalità di raccolta, è possibile specificare cartelle di raccolta dati aggiuntive usando una raccolta con la struttura seguente:

- **Nome**: nome della raccolta, che verrà usato come nome della sottocartella all'interno del file ZIP da raccogliere.

- **Percorso**: percorso della cartella nella macchina virtuale in cui verrà raccolto il file.

- **Modello di ricerca**: modello dei nomi di file da raccogliere. Il valore predefinito è "*".

- **Ricorsiva**: se i file verranno raccolti in modo ricorsivo nella cartella.

<!----HONumber=AcomDC_0323_2016-->