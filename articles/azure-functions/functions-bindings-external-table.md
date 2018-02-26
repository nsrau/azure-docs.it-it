---
title: Associazione di tabelle esterne per Funzioni di Azure (sperimentale)
description: Uso di associazioni di tabelle esterne in Funzioni di Azure
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
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Associazione di tabelle esterne per Funzioni di Azure (sperimentale)

Questo articolo illustra come usare i dati tabulari in provider SaaS, come SharePoint e Dynamics, in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per le tabelle esterne.

> [!IMPORTANT]
> Le associazioni di tabelle esterne sono sperimentali e potrebbero non diventare mai disponibili a livello generale. Sono incluse solo in Funzioni di Azure 1.x e non è prevista la loro aggiunta in Funzioni di Azure 2. x. Per gli scenari che richiedono l'accesso ai dati nei provider SaaS, è consigliabile usare [app per la logica che chiamano funzioni](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Connessioni API

Le associazioni di tabella usano connessioni API esterne per l'autenticazione con i provider SaaS di terze parti. 

Quando si assegna un'associazione, è possibile creare una nuova connessione API o usare una connessione API esistente nello stesso gruppo di risorse.

### <a name="available-api-connections-tables"></a>Connessioni API disponibili (tabelle)

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
> È possibile usare le connessioni alle tabelle esterne anche in [App per la logica di Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Creazione di una connessione API: procedura dettagliata

1. Nel portale di Azure per l'app per le funzioni selezionare il segno di addizione (**+**) per creare una funzione.

1. Nella casella **Scenario** selezionare **Sperimentale**.

1. Selezionare **Tabella esterna**.

1. Selezionare un linguaggio.

2. In **Connessione tabella esterna** selezionare una connessione esistente oppure selezionare **nuova**.

1. Per una nuova connessione, configurare le impostazioni e quindi scegliere **Autorizza**.

1. Selezionare **Crea** per creare la funzione.

1. Selezionare **Integrazione > Tabella esterna**.

1. Configurare la connessione per usare la tabella di destinazione. Queste impostazioni variano in base ai provider SaaS. Nella sezione seguente sono disponibili alcuni esempi.

## <a name="example"></a>Esempio

Questo esempio si connette a una tabella denominata "Contatto" con le colonne Id, LastName e FirstName. Il codice elenca le entità Contatto nella tabella e registra i nomi e i cognomi.

Ecco il file *function.json*:

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

Ecco il codice script C#:

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
        //retrieve table values
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

### <a name="sql-server-data-source"></a>Origine dati SQL Server

Ecco uno script per creare una tabella in SQL Server da usare con questo esempio. `dataSetName` è "default".

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

### <a name="google-sheets-data-source"></a>Origine dati Fogli Google

Per creare una tabella da usare con questo esempio in Documenti Google, creare un foglio di calcolo con un foglio di lavoro denominato `Contact`. Il connettore non può usare il nome visualizzato del foglio di calcolo. Il nome interno (in grassetto) deve essere usato come dataSetName, ad esempio: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Aggiungere i nomi di colonna `Id`, `LastName`, `FirstName` alla prima riga, quindi inserire dati nelle righe successive.

### <a name="salesforce"></a>Salesforce

Per usare questo esempio con Salesforce, `dataSetName` è "default".

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*.

|Proprietà di function.json | DESCRIZIONE|
|---------|----------------------|
|**type** | Il valore deve essere impostato su `apiHubTable`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**connessione**| Identifica l'impostazione dell'app che archivia la stringa di connessione API. L'impostazione dell'app viene creata automaticamente quando si aggiunge una connessione API nell'interfaccia utente integrata.|
|**dataSetName**|Nome del set di dati che contiene la tabella da leggere.|
|**tableName**|Nome della tabella|
|**entityId**|Deve essere vuoto per le associazioni di tabella.

Un connettore tabulare fornisce set di dati e ogni set di dati contiene tabelle. Il nome del set di dati predefinito è "default". I titoli di un set di dati e di una tabella in diversi provider SaaS sono elencati di seguito:

|Connettore|Set di dati|Tabella|
|:-----|:---|:---| 
|**SharePoint**|Sito|Elenco SharePoint
|**SQL**|Database|Tabella 
|**Fogli Google**|Foglio di calcolo|Foglio di lavoro 
|**Excel**|File di Excel|Foglio 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
