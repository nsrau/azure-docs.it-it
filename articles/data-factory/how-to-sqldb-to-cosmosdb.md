---
title: Eseguire la migrazione delle tabelle del database SQL di Azure ad Azure CosmosDB con Azure Data Factory
description: Usare uno schema di database normalizzato esistente dal database SQL di Azure ed eseguire la migrazione a un contenitore denormalizzato di Azure CosmosDB con Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 3d2ef6fb0cd7af444b9bff755eee4eee70d03d15
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691891"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Eseguire la migrazione dello schema del database normalizzato dal database SQL di Azure al contenitore denormalizzato di Azure CosmosDB

Questa guida illustra come eseguire uno schema del database normalizzato esistente nel database SQL di Azure e come convertirlo in uno schema denormalizzato di CosmosDB di Azure per il caricamento in Azure CosmosDB.

Gli schemi SQL vengono in genere modellati utilizzando la terza forma normale, ottenendo schemi normalizzati che forniscono livelli elevati di integrità dei dati e meno valori di dati duplicati. Le query possono unire le entità tra le tabelle per la lettura. CosmosDB è ottimizzato per le transazioni super veloci e per l'esecuzione di query all'interno di una raccolta o di un contenitore tramite schemi denormalizzati con dati indipendenti all'interno di un documento.

Utilizzando Azure Data Factory, verrà compilata una pipeline che utilizza un singolo flusso di dati di mapping per leggere da due tabelle normalizzate del database SQL di Azure che contengono chiavi primarie ed esterne come relazione tra entità. ADF unirà tali tabelle in un singolo flusso usando il motore di Spark del flusso di dati, raccoglie le righe unite in join in matrici e genera singoli documenti puliti da inserire in un nuovo contenitore di Azure CosmosDB.

In questa guida verrà creato un nuovo contenitore in tempo reale denominato "Orders" che ```SalesOrderHeader``` utilizzerà ```SalesOrderDetail``` le tabelle e del database di esempio standard SQL Server AdventureWorks. Tali tabelle rappresentano le transazioni di vendita ```SalesOrderID```unite da. Ogni record di dettaglio univoco ha una propria chiave primaria ```SalesOrderDetailID```di. La relazione tra l'intestazione e il ```1:M```dettaglio è. Si aggiungerà ad ```SalesOrderID``` ADF e quindi si eseguirà il Rolling di ogni record dei dettagli correlati in una matrice denominata "Detail".

La query SQL rappresentativa per questa guida è la seguente:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Il contenitore CosmosDB risultante incorpora la query interna in un unico documento e ha un aspetto simile al seguente:

![Raccolta](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare **+ nuova pipeline** per creare una nuova pipeline.

2. Aggiungere un'attività flusso di dati

3. Nell'attività flusso di dati selezionare **nuovo flusso di dati di mapping**.

4. Questo grafico del flusso di dati verrà costruito sotto

![Grafico del flusso di dati](media/data-flow/cosmosb1.png)

5. Definire l'origine per "SourceOrderDetails". Per set di dati, creare un nuovo set di dati del ```SalesOrderDetail``` database SQL di Azure che punti alla tabella.

6. Definire l'origine per "SourceOrderHeader". Per set di dati, creare un nuovo set di dati del ```SalesOrderHeader``` database SQL di Azure che punti alla tabella.

7. Nell'origine principale aggiungere una trasformazione colonna derivata dopo "SourceOrderDetails". Chiamare la nuova trasformazione "TypeCast". È necessario arrotondare la ```UnitPrice``` colonna ed eseguirne il cast a un tipo di dati Double per CosmosDB. Impostare la formula su: ```toDouble(round(UnitPrice,2))```.

8. Aggiungere un'altra colonna derivata e denominarla "MakeStruct". Qui verrà creata una struttura gerarchica che conterrà i valori della tabella details. Tenere presente che i dettagli ```M:1``` sono una relazione con l'intestazione. Assegnare un nome alla ```orderdetailsstruct``` nuova struttura e creare la gerarchia in questo modo, impostando ogni sottocolonna sul nome della colonna in ingresso:

![Create Structure](media/data-flow/cosmosb9.png)

9. A questo punto, passiamo all'origine dell'intestazione Sales. Aggiungere una trasformazione join. Per il lato destro selezionare "MakeStruct". Lasciare impostato su inner join e scegliere ```SalesOrderID``` entrambi i lati della condizione di join.

10. Fare clic sulla scheda Anteprima dati nel nuovo join aggiunto per poter visualizzare i risultati fino a questo punto. Verranno visualizzate tutte le righe di intestazione unite con le righe di dettaglio. Questo è il risultato del join creato da ```SalesOrderID```. Verranno quindi combinati i dettagli delle righe comuni nello struct dei dettagli e verranno aggregate le righe comuni.

![Join](media/data-flow/cosmosb4.png)

11. Prima di poter creare le matrici per denormalizzare queste righe, è necessario prima rimuovere le colonne indesiderate e assicurarsi che i valori dei dati corrispondano ai tipi di dati CosmosDB.

12. Aggiungere una trasformazione Select e impostare il mapping del campo su un aspetto simile al seguente:

![Scrubber colonne](media/data-flow/cosmosb5.png)

13. A questo punto è possibile eseguire il cast di una colonna ```TotalDue```di valuta. Come è stato fatto in precedenza nel passaggio 7, impostare la formula ```toDouble(round(TotalDue,2))```su:.

14. Qui è possibile denormalizzare le righe raggruppando in base alla chiave ```SalesOrderID```comune. Aggiungere una trasformazione aggregazione e impostare il raggruppamento su ```SalesOrderID```.

15. Nella formula aggregate aggiungere una nuova colonna denominata "Details" e usare questa formula per raccogliere i valori nella struttura creata in precedenza denominata ```orderdetailsstruct```:. ```collect(orderdetailsstruct)```

16. La trasformazione aggregazione restituirà solo le colonne che fanno parte delle formule aggregate o Group by. Quindi, è necessario includere anche le colonne dell'intestazione Sales. A tale scopo, aggiungere un modello di colonna nella stessa trasformazione di aggregazione. Questo modello includerà tutte le altre colonne nell'output:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Utilizzare la sintassi "This" nelle altre proprietà in modo da mantenere gli stessi nomi di colonna e utilizzare la ```first()``` funzione come aggregato:

![Aggregate](media/data-flow/cosmosb6.png)

18. È ora possibile completare il flusso di migrazione aggiungendo una trasformazione del sink. Fare clic su "nuovo" accanto a DataSet e aggiungere un set di dati CosmosDB che punti al database CosmosDB. Per la raccolta, chiameremo "Orders" (ordini) e non sarà disponibile alcuno schema e nessun documento perché verrà creato in tempo reale.

19. In impostazioni sink, chiave di ```\SalesOrderID``` partizione e azione di raccolta su "ricrea". Assicurarsi che la scheda mapping abbia un aspetto simile al seguente:

![Impostazioni sink](media/data-flow/cosmosb7.png)

20. Fare clic su data Preview (Anteprima dati) per assicurarsi di visualizzare queste 32 righe impostate per inserire come nuovi documenti nel nuovo contenitore:

![Impostazioni sink](media/data-flow/cosmosb8.png)

Se tutto sembra corretto, è ora possibile creare una nuova pipeline, aggiungere l'attività flusso di dati alla pipeline ed eseguirla. È possibile eseguire da debug o da un'esecuzione attivata. Dopo alcuni minuti, è necessario avere un nuovo contenitore denormalizzato di ordini denominato "Orders" nel database CosmosDB.

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.
* [Scaricare il modello di pipeline completato](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) per questa esercitazione e importare il modello nella Factory.
