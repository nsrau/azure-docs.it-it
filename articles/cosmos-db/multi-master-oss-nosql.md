---
title: Uso del multimaster di Azure Cosmos DB con database NoSQL open source
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963901"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Uso del multimaster di Azure Cosmos DB con database NoSQL open source

Il supporto multimaster di Azure Cosmos DB è un'implementazione nativa sul lato server disponibile per tutte le offerte di database NoSQL open source supportate da Cosmos DB. È anche accessibile per tutti gli SDK supportati da Cosmos DB.

Azure Cosmos DB è il primo servizio al mondo a offrire il supporto multimaster per questi database NoSQL open source.

|Modello  |Supporto  |
|---------|---------|
|MongoDB  | Attivo-attivo  |
|Grafico  | Attivo-attivo |
|Cassandra  | Attivo-attivo   |

## <a name="use-mongodb-clients-with-multi-master"></a>Usare client MongoDB con multimaster

La funzionalità multimaster è abilitata per gli account API MongoDB nello stesso modo in cui è abilitata per le altre API di Azure Cosmos DB. Dopo aver abilitato multimaster per gli account API MongoDB, ogni istanza di un'applicazione client può selezionare l'area preferita per letture e scritture. Dal punto di vista del driver MongoDB, l'area preferita viene visualizzata al client come area primaria del set di repliche. In questo modo, ogni area del database distribuito può fungere da area primaria del set di repliche. La funzionalità multimaster di Azure Cosmos DB consente di ridurre notevolmente le latenze di scrittura per le applicazioni MongoDB distribuite a livello globale. 

### <a name="set-the-primary-region"></a>Impostare l'area primaria

Ogni istanza di un client MongoDB può selezionare l'area primaria aggiungendo `@<preferred_primary_region_name>` al campo "nome applicazione" accettato dal driver client MongoDB. La maggior parte dei driver accettano questa informazione nella stringa di connessione, ad esempio:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Dopo la connessione, può verificarsi uno dei casi seguenti:

* Se il nome dell'area preferita è disponibile come area di scrittura, Azure Cosmos DB la seleziona come area primaria.

* Se il nome dell'area preferita non è specificato, Azure Cosmos DB seleziona un'area predefinita come area primaria.

* Se viene specificato un nome di area preferita, ma tale area non è disponibile come area di scrittura per l'account del database, Azure Cosmos DB selezionerà l'area di scrittura più vicina disponibile come area primaria.

Alcuni driver, ad esempio il driver NodeJS indirizzeranno sempre prima di tutto le scritture all'host specificato nella stringa di connessione iniziale. Per questi driver, per assicurarsi che le scritture vengano indirizzate all'area preferita, oltre al nome dell'app, è necessario modificare il nome DNS all'interno della stringa di connessione in modo da includere il nome dell'area. Assicurarsi di specificare il nome dell'area senza spazi. Ad esempio: 

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Modalità di risoluzione dei conflitti

La modalità di risoluzione dei conflitti per gli account API MongoDB di Azure Cosmos DB prevede sempre la priorità dell'ultima scrittura (LWW, Last-Write-Wins), usando il timestamp del server di area che accettato la scrittura.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato presentato il supporto multimaster per gli account API MongoDB di Azure Cosmos DB. Esaminare quindi le risorse seguenti:

* [How to enable multi-master for Azure Cosmos DB accounts (Come abilitare il multimaster per gli account di Azure Cosmos DB)](enable-multi-master.md)

* [Understanding conflict resolution in Azure Cosmos DB (Informazioni sulla risoluzione dei conflitti in Azure Cosmos DB)](multi-master-conflict-resolution.md)
