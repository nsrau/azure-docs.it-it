---
title: Copia ripetibile in Azure Data Factory | Microsoft Docs
description: "Informazioni su come evitare duplicati, anche se una sezione che copia i dati viene eseguita più volte."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0519a9c60386f8fea0047a661e48f008d3141c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Copia ripetibile in Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione.  
 
> [!NOTE]
> Gli esempi seguenti sono per Azure SQL, ma sono applicabili a qualsiasi archivio dati che supporti set di dati rettangolari. Potrebbe essere necessario modificare il **tipo** di origine e la proprietà **query** (ad esempio, query invece di sqlReaderQuery) per l'archivio dati.   

In genere, durante la lettura da archivi relazionali, si preferisce leggere solo i dati corrispondenti a tale sezione. Un modo per eseguire questa operazione è usare le variabili di sistema WindowStart e WindowEnd disponibili in Azure Data Factory. Per altre informazioni su variabili e funzioni di Azure Data Factory consultare l'articolo [Azure Data Factory - Funzioni e variabili di sistema](data-factory-functions-variables.md) seguente. Esempio: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Questa query legge i dati che rientrano nell'intervallo di durata della sezione (WindowStart-> WindowEnd) della tabella MyTable. Inoltre la riesecuzione di questa sezione garantirà sempre la lettura degli stessi dati. 

In altri casi, si consiglia di leggere l'intera tabella e definire sqlReaderQuery come segue:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Scrittura ripetibile in SqlSink
Quando si copiano dati in un **database di SQL Server/SQL di Azure** da altri archivi dati, è necessario mantenere criteri di ripetibilità per evitare risultati imprevisti. 

Quando si copiano dati in un database SQL Server/SQL di Azure, per impostazione predefinita l'attività di copia accoda i dati alla tabella di sink. Ad esempio, si copiano dati da un file con estensione CSV (valori delimitati da virgole) contenente due record per la tabella seguente in un Database SQL di Azure o SQL Server. Quando viene eseguita una sezione, i due record vengono copiati nella tabella di SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Si supponga di aver trovato degli errori nel file di origine e di aver aggiornato la quantità di Down Tube da 2 a 4. Se si esegue nuovamente la sezione di dati per quel periodo manualmente, saranno disponibili due nuovi record accodati al database di SQL Server/SQL di Azure. Questo esempio presuppone che in nessuna delle colonne della tabella sia presente il vincolo di chiave primaria.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Per evitare questo comportamento, è necessario specificare la semantica UPSERT usando uno dei due meccanismi seguenti:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Meccanismo 1: uso di sqlWriterCleanupScript
È possibile usare la proprietà **sqlWriterCleanupScript** per cancellare i dati dalla tabella sink prima di inserire i dati durante l'esecuzione di una sezione. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando viene eseguita una sezione, lo script di pulizia viene eseguito prima per eliminare i dati che corrispondono alla sezione della tabella SQL. Tramite l'attività di copia i dati vengono quindi inseriti nella tabella SQL. Se la sezione viene eseguita di nuovo, la quantità viene aggiornata in base alle esigenze.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Si supponga, ad esempio, che il record Flat Washer venga rimosso dal file con estensione csv originale. Se la sezione viene eseguita nuovamente, si ottiene il risultato seguente: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

L'attività di copia ha eseguito lo script di pulizia per eliminare i dati corrispondenti alla sezione. Quindi, ha letto l'input dal file con estensione CSV (che conteneva solo un record) e lo ha inserito nella tabella. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Meccanismo 2: uso di sliceIdentifierColumnName
> [!IMPORTANT]
> Attualmente sliceIdentifierColumnName non è supportato per Azure SQL Data Warehouse. 

Il secondo meccanismo per ottenere la ripetibilità prevede la disponibilità di una colonna dedicata (sliceIdentifierColumnName) nella tabella di destinazione. Questa colonna viene usata da Data factory di Azure per garantire che l'origine e la destinazione rimangano sincronizzate. Questo approccio può essere usato solo quando è disponibile una certa flessibilità nella modifica o nella definizione dello schema della tabella SQL di destinazione. 

La colonna viene usata da Azure Data Factory per scopi di ripetibilità e nel corso del processo Azure Data Factory non apporterà alcuna modifica allo schema della tabella. Per applicare questo approccio, è possibile seguire questa procedura:

1. Definire una colonna di tipo **binario (32)** nella tabella SQL di destinazione. in cui non sia presente alcun vincolo. Ai fini di questo esempio, la colonna viene denominata AdfSliceIdentifier.


    Tabella di origine:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabella di destinazione: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Usarla nell'attività di copia come segue:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory popolerà la colonna in base alle proprie esigenze affinché l'origine e la destinazione risultino sincronizzate. I valori della colonna non possono essere usati al di fuori di questo contesto. 

Analogamente al meccanismo 1, l'attività di copia cancella automaticamente i dati della sezione specificata dalla tabella SQL di destinazione. Vengono quindi inseriti i dati dall'origine nella tabella di destinazione. 

## <a name="next-steps"></a>Passaggi successivi
Rivedere gli articoli seguenti sul connettore per gli esempi JSON completi: 

- [Database SQL di Azure](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)