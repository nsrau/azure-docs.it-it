---
title: Impostare i parametri dei servizi collegati in Azure Data Factory
description: Informazioni su come impostare i parametri dei servizi collegati in Azure Data Factory e passare i valori dinamici in fase di esecuzione.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b3aadab1b4af80f98c57a279b69606a02846e996
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716844"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Impostare i parametri dei servizi collegati in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È ora possibile impostare i parametri per un servizio collegato e passare i valori dinamici in fase di esecuzione. Se ad esempio si desidera connettersi a database diversi nello stesso server SQL logico, è ora possibile parametrizzare il nome del database nella definizione del servizio collegato. In questo modo si evita di dover creare un servizio collegato per ogni database nel server SQL logico. È possibile parametrizzare anche altre proprietà nella definizione del servizio collegato, ad esempio *Nome utente.*

È possibile usare l'interfaccia utente di Data Factory nell'portale di Azure o un'interfaccia di programmazione per parametrizzare i servizi collegati.

> [!TIP]
> È consigliabile di non impostare i parametri per le password o i segreti. Archiviare invece tutte le stringhe di connessione in Azure Key Vault e impostare i parametri per *Nome segreto*.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Archivi dati supportati

Al momento, la parametrizzazione del servizio collegato è supportata nell'interfaccia utente di Data Factory per gli archivi dati seguenti. Per tutti gli altri archivi dati, è possibile impostare i parametri del servizio collegato, selezionando l'icona **Codice** sulla scheda **Connessioni** e usando l'editor JSON.

- Amazon Redshift
- Azure Cosmos DB (API SQL)
- Database di Azure per MySQL
- database SQL di Azure
- Azure Synapse Analytics (in precedenza SQL Data Warehouse)
- MySQL
- Oracle
- SQL Server
- HTTP generico
- REST generico

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

![Aggiungere contenuto dinamico alla definizione del servizio collegato](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Creare un nuovo parametro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
