---
title: Impostare i parametri dei servizi collegati in Azure Data Factory | Microsoft Docs
description: Informazioni su come impostare i parametri dei servizi collegati in Azure Data Factory e passare i valori dinamici in fase di esecuzione.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: douglasl
ms.openlocfilehash: 874d2e7b5925bf299c3749948206401e00553736
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650448"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Impostare i parametri dei servizi collegati in Azure Data Factory

È ora possibile impostare i parametri per un servizio collegato e passare i valori dinamici in fase di esecuzione. Ad esempio, se si desidera connettersi a database diversi nello stesso server di Database SQL di Azure, è ora possibile impostare i parametri del nome del database nella definizione del servizio collegato. In questo modo l'utente non deve creare un servizio collegato per ogni database nel server di database SQL di Azure. È possibile parametrizzare anche altre proprietà nella definizione del servizio collegato, ad esempio *Nome utente.*

È possibile usare l'interfaccia utente di Data Factory nel portale di Azure o un'interfaccia di programmazione per impostare i parametri dei servizi collegati.

> [!TIP]
> È consigliabile di non impostare i parametri per le password o i segreti. Archiviare invece tutte le stringhe di connessione in Azure Key Vault e impostare i parametri per *Nome segreto*.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Archivi dati supportati

A questo punto, l'impostazione dei parametri del servizio collegato è supportata nell'interfaccia utente di Data Factory nel portale di Azure per gli archivi dati seguenti. Per tutti gli altri archivi dati, è possibile impostare i parametri del servizio collegato, selezionando l'icona **Codice** sulla scheda **Connessioni** e usando l'editor JSON.
- Database SQL di Azure
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Database di Azure per MySQL

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
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
