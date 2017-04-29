## <a name="what-is-the-table-service"></a>Informazioni sul Servizio tabelle
Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Di seguito sono riportati gli utilizzi più comuni per il servizio tabelle.

* Archiviazione di terabyte di dati strutturati in grado di servire applicazioni su scala Web
* Archiviazione di set di dati che non richiedono join complessi, chiavi esterne o stored procedure e che possono essere denormalizzati per l'accesso rapido
* Esecuzione rapida di query sui dati mediante un indice cluster
* Accesso ai dati tramite il protocollo OData e query LINQ con librerie .NET WCF Data Service

È possibile utilizzare il servizio tabelle per archiviare e interrogare grandi set di dati strutturati non relazionali, con tabelle scalabili in base all'aumento della domanda

## <a name="table-service-concepts"></a>Concetti del servizio tabelle
Il servizio tabelle contiene i componenti seguenti:

![Table1][Table1]

* **Formato dell'URL**: il codice fa riferimento alle tabelle in un account usando il formato di indirizzo seguente:   
  http://`<storage account>`.table.core.windows.net/`<table>`  
  
  È possibile fare riferimento direttamente alle tabelle di Azure utilizzando questo indirizzo con il protocollo OData. Per altre informazioni, vedere [OData.org][OData.org]
* **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md) .
* **Tabella**: una tabella è una raccolta di entità. Le tabelle non impongono uno schema sulle entità, pertanto una singola tabella può contenere entità che presentano set di proprietà diversi. Il numero di tabelle che un account di archiviazione può contenere è limitato solo in base al limite di capacità dell'account di archiviazione.
* **Entità**: un'entità è un set di proprietà, simile a una riga di database. Un'entità può avere una dimensione massima di 1 MB.
* **Proprietà**: una proprietà è una coppia nome-valore. Ogni entità può includere fino a 252 proprietà per l'archiviazione dei dati. Ogni entità dispone inoltre di 3 proprietà di sistema che specificano una chiave di partizione, una chiave di riga e un timestamp. Le entità con la stessa chiave di partizione possono essere interrogate più rapidamente e inserite o aggiornate in operazioni atomiche. La chiave di riga di un'entità ne rappresenta l'identificatore univoco all'interno di una partizione.

Per informazioni sulle proprietà e i tipi di tabelle, vedere [Informazioni sul modello di dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
