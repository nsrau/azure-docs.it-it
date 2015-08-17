## Ripetibilità durante la copia

Quando si copiano dati da e in archivi relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti.

**Nota:**In base ai criteri di ripetizione dei tentativi specificati, è possibile ripetere automaticamente l'esecuzione di una sezione in Data Factory di Azure. È consigliabile impostare un criterio di ripetizione come protezione dagli errori temporanei. Di conseguenza la ripetibilità è un aspetto importante a cui occorre prestare attenzione durante lo spostamento dei dati.

**Come origine:**

Nella maggior parte dei casi, durante la lettura da archivi relazionali, si desidera leggere solo i dati corrispondenti a tale sezione. Un modo per eseguire questa operazione è utilizzare le variabili WindowStart e WindowEnd disponibili in Azure Data Factory. Informazioni su variabili e funzioni di Data Factory di Azure nell’articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md)seguente. Esempio:
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

La query precedente consente di leggere dati "MyTable" che rientrano nell'intervallo di durata della sezione. La riesecuzione di questa sezione garantirà sempre questo comportamento.

In altri casi, si consiglia di leggere l'intera tabella (ad esempio per lo spostamento di una sola vota) e definire sqlReaderQuery come segue:

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!---HONumber=August15_HO6-->