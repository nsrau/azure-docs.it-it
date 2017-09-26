## <a name="repeatability-during-copy"></a>Ripetibilità durante la copia
Quando si copiano dati da e in  archivi relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. 

In base ai criteri di ripetizione dei tentativi specificati, è possibile ripetere automaticamente l'esecuzione di una sezione anche in Azure Data Factory. È consigliabile impostare un criterio di ripetizione come protezione dagli errori temporanei. Di conseguenza la ripetibilità è un aspetto importante a cui occorre prestare attenzione durante lo spostamento dei dati. 

**Come origine:**

> [!NOTE]
> Gli esempi seguenti sono per Azure SQL, ma sono applicabili a qualsiasi archivio dati che supporti set di dati rettangolari. Potrebbe essere necessario modificare il **tipo** di origine e la proprietà **query** (ad esempio, query invece di sqlReaderQuery) per l'archivio dati.   
> 
> 

In genere, durante la lettura da archivi relazionali, si preferisce leggere solo i dati corrispondenti a tale sezione. Un modo per eseguire questa operazione è utilizzare le variabili WindowStart e WindowEnd disponibili in Azure Data Factory. Informazioni su variabili e funzioni di Data Factory di Azure nell’articolo [Pianificazione ed esecuzione](../articles/data-factory/v1/data-factory-scheduling-and-execution.md) seguente. Esempio: 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Questa query legge dati "MyTable" che rientrano nell'intervallo di durata della sezione. La riesecuzione di questa sezione garantirà sempre questo comportamento. 

In altri casi, si consiglia di leggere l'intera tabella (ad esempio per lo spostamento di una sola vota) e definire sqlReaderQuery come segue:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```
