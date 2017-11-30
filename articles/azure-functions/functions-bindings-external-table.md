---
title: Binding di tabelle esterne in Funzioni di Azure (Anteprima) | Microsoft Docs
description: Uso di binding di tabelle esterne in Funzioni di Azure
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7b226aa4ec71535aa0222389aacd74764a80021a
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Binding di tabelle esterne in Funzioni di Azure (Anteprima)
Questo articolo illustra come gestire i dati tabulari sul provider SaaS (ad esempio, Sharepoint, Dynamics) all'interno della funzione con binding incorporati. Funzioni di Azure supporta i binding di input e output per le tabelle esterne.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Connessioni API

I binding di tabella usano connessioni API esterne per l'autenticazione con i provider SaaS di terzi. 

Quando si assegna un binding è possibile creare una nuova connessione API o usare una connessione API esistente nello stesso gruppo di risorse

### <a name="supported-api-connections-tables"></a>Connessioni API supportate (Tabella)

|Connettore|Trigger|Input|Output|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Fogli Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> È possibile usare le connessioni alle tabelle esterne anche nelle [App per la logica di Azure](https://docs.microsoft.com/azure/connectors/apis-list)

### <a name="creating-an-api-connection-step-by-step"></a>Creazione di una connessione API: procedura dettagliata

1. Creare una funzione > funzione personalizzata ![Creare una funzione personalizzata](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Modello scenario `Experimental`  >  `ExternalTable-CSharp` > Creare un nuovo `External Table connection` 
 ![Scegliere il modello di input tabella](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Scegliere il provider SaaS > scegliere o creare una connessione ![Configurare una connessione SaaS](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Selezionare la connessione API > creare la funzione ![funzione Crea tabella](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Selezionare `Integrate` > `External Table`
    1. Configurare la connessione per usare la tabella di destinazione. Queste impostazioni variano tra i provider SaaS. Sono indicate qui di seguito in [impostazioni dell'origine dati](#datasourcesettings)
![Configura tabella](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Utilizzo

Questo esempio si connette a una tabella denominata "Contatto" con le colonne Id, LastName e FirstName. Il codice elenca le entità Contatto nella tabella e registra i nomi e i cognomi.

### <a name="bindings"></a>Associazioni
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId` deve essere vuoto per i binding di tabella.

`ConnectionAppSettingsKey` identifica l'impostazione dell'app che memorizza la stringa di connessione dell'API. L'impostazione dell'app viene creata automaticamente quando si aggiunge una connessione API nell'interfaccia utente integrata.

Un connettore tabulare fornisce set di dati e ogni set di dati contiene tabelle. Il nome del set di dati predefinito è "default". I titoli di un set di dati e di una tabella in diversi provider SaaS sono elencati di seguito:

|Connettore|Set di dati|Tabella|
|:-----|:---|:---| 
|**SharePoint**|Sito|Elenco SharePoint
|**SQL**|Database|Tabella 
|**Fogli Google**|Foglio di calcolo|Foglio di lavoro 
|**Excel**|File di Excel|Foglio 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilizzo in C# #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## Impostazioni dell'origine dati

### <a name="sql-server"></a>SQL Server

Lo script per creare e popolare la tabella Contact è qui di seguito. dataSetName è "default".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Fogli Google
In Documenti Google, creare un foglio di calcolo con un foglio di lavoro denominato `Contact`. Il connettore non può usare il nome visualizzato del foglio di calcolo. Il nome interno (in grassetto) deve essere usato come dataSetName, ad esempio: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Aggiungere i nomi di colonna `Id`, `LastName`, `FirstName` alla prima riga, quindi inserire dati nelle righe successive.

### <a name="salesforce"></a>Salesforce
dataSetName è "default".

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
