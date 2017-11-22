---
title: Supporto di Archiviazione tabelle di Azure in Azure Cosmos DB | Microsoft Docs
description: Informazioni sull'interazione tra l'API Table di Azure Cosmos DB e le tabelle di Archiviazione di Azure.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Sviluppo con l'API Table di Azure Cosmos DB e Archiviazione tabelle di Azure

L'API Table di Azure Cosmos DB e Archiviazione tabelle di Azure condividono lo stesso modello di dati di tabella ed espongono le stesse operazioni di creazione, eliminazione, aggiornamento ed esecuzione di query tramite i relativi SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Sviluppo con l'API Table di Azure Cosmos DB

L'[API Table di Azure Cosmos DB](table-introduction.md) attualmente dispone di quattro SDK per lo sviluppo: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. Questa libreria include le stesse classi e firme di metodi disponibili nella versione pubblica dell'[SDK di Archiviazione di Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage), ma permette anche di connettersi agli account Azure Cosmos DB tramite l'API Table. 
- [Python SDK](table-sdk-python.md). Il nuovo SDK Python di Azure Cosmos DB è l'unico SDK che supporta l'archiviazione tabelle di Azure in Python. Questo SDK si connette sia ad Archiviazione tabelle di Azure che all'API Table di Azure Cosmos DB.
- [Java SDK](table-sdk-java.md). Questo SDK di Archiviazione di Azure è in grado di connettersi agli account di Azure Cosmos DB tramite l'API Table.
- [Node.js SDK](table-sdk-nodejs.md). Questo SDK di Archiviazione di Azure è in grado di connettersi agli account di Azure Cosmos DB tramite l'API Table.

Per altre informazioni sull'uso dell'API Table, vedere l'articolo relativo alle [domande frequenti sullo sviluppo con l'API Table](faq.md#develop-with-the-table-api).

## <a name="developing-with-the-azure-table-storage"></a>Sviluppo con Archiviazione tabelle di Azure

I numerosi SDK e le esercitazioni relative a [Archiviazione tabelle di Azure](table-storage-overview.md) sono disponibili nella sezione relativa ad [Archiviazione tabelle di Azure](table-storage-overview.md). Questi articoli sono sottoposti ad aggiornamento in base alla disponibilità dell'interoperabilità tra gli SDK di Archiviazione tabelle di Azure e le API di Azure Cosmos DB.  





