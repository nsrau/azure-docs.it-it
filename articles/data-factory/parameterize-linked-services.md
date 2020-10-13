---
title: Impostare i parametri dei servizi collegati in Azure Data Factory
description: Informazioni su come impostare i parametri dei servizi collegati in Azure Data Factory e passare i valori dinamici in fase di esecuzione.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 081d19cc845750f1392e2c1a14229a51d0df4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276458"
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

È possibile parametrizzare qualsiasi tipo di servizio collegato.
Quando si crea il servizio collegato nell'interfaccia utente, Data Factory fornisce un'esperienza di parametrizzazione predefinita per i tipi di connettori seguenti. Nel pannello di creazione/modifica del servizio collegato è possibile trovare opzioni per i nuovi parametri e aggiungere contenuto dinamico.

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

Per gli altri tipi, è possibile parametrizzare il servizio collegato modificando JSON nell'interfaccia utente:

- Nel pannello di creazione/modifica del servizio collegato-> espandere "avanzate" nella parte inferiore della casella di controllo > selezionare "specifica contenuto dinamico in formato JSON"-> specificare il payload JSON del servizio collegato. 
- In alternativa, dopo aver creato un servizio collegato senza parametrizzazione, in [Hub di gestione](author-visually.md#management-hub) -> servizi collegati-> trovare il servizio collegato specifico-> fare clic su "codice" (pulsante " {} ") per modificare il JSON. 

Vedere l' [esempio JSON](#json) per aggiungere la ` parameters` sezione per definire i parametri e fare riferimento al parametro usando ` @{linkedService().paraName} ` .

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
