## <a name="what-is-table-storage"></a>Informazioni sull'Archiviazione tabelle
Il servizio Archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Di seguito sono riportati gli usi più comuni per il servizio Archiviazione tabelle:

* Archiviazione di terabyte di dati strutturati in grado di servire applicazioni su scala Web
* Archiviazione di set di dati che non richiedono join complessi, chiavi esterne o stored procedure e che possono essere denormalizzati per l'accesso rapido
* Esecuzione rapida di query sui dati mediante un indice cluster
* Accesso ai dati tramite il protocollo OData e query LINQ con librerie .NET WCF Data Service

È possibile usare il servizio Archiviazione tabelle per archiviare e interrogare grandi set di dati strutturati non relazionali, con tabelle scalabili in base all'aumento della domanda.

## <a name="table-storage-concepts"></a>Concetti relativi all'Archiviazione tabelle
L'Archiviazione tabelle contiene i componenti seguenti:

![Diagramma dei componenti di Archiviazione tabelle][Table1]

* **Formato URL:** gli account di archiviazione tabelle di Azure usano questo formato: `http://<storage account>.table.core.windows.net/<table>`

  Gli account di API di tabella di Azure Cosmos DB usano questo formato: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  È possibile fare riferimento direttamente alle tabelle di Azure utilizzando questo indirizzo con il protocollo OData. Per altre informazioni, vedere [OData.org][OData.org].
* **Account:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/common/storage-scalability-targets.md) . 

    L'accesso ad Azure Cosmos DB avviene esclusivamente tramite un account di API di tabella. Per i dettagli sulla creazione di un account di API di tabella, vedere [Creare un account di API di tabella](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account).
* **Tabella**: una tabella è una raccolta di entità. Le tabelle non impongono uno schema sulle entità, pertanto una singola tabella può contenere entità che presentano set di proprietà diversi.  
* **Entità**: un'entità è un set di proprietà, simile a una riga di database. Un'entità in Archiviazione di Azure può avere una dimensione massima di 1 MB. Un'entità in Azure Cosmos DB può avere una dimensione massima di 2MB.
* **Proprietà**: una proprietà è una coppia nome-valore. Ogni entità può includere fino a 252 proprietà per l'archiviazione dei dati. Ogni entità dispone inoltre di tre proprietà di sistema che specificano una chiave di partizione, una chiave di riga e un timestamp. Le entità con la stessa chiave di partizione possono essere interrogate più rapidamente e inserite o aggiornate in operazioni atomiche. La chiave di riga di un'entità ne rappresenta l'identificatore univoco all'interno di una partizione.

Per informazioni sulle proprietà e i tipi di tabelle, vedere [Informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
