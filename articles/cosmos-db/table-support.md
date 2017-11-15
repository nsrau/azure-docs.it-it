---
title: Supporto di Archiviazione tabelle di Azure in Azure Cosmos DB | Microsoft Docs
description: Informazioni sull'interazione tra l'API di tabella di Azure Cosmos DB e le tabelle di Archiviazione di Azure.
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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Sviluppo con l'API di tabella di Azure Cosmos DB e Archiviazione tabelle di Azure

L'API di tabella di Azure Cosmos DB e Archiviazione tabelle di Azure condividono lo stesso modello di dati di tabella ed espongono le stesse operazioni di creazione, eliminazione, aggiornamento ed esecuzione di query tramite i relativi SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Sviluppo con l'API di tabella di Azure Cosmos DB

Attualmente l'[API di tabella di Azure Cosmos DB](table-introduction.md) dispone di un .NET SDK, ovvero [Windows Azure Storage Premium Table (anteprima)](https://aka.ms/premiumtablenuget). Questa libreria include le stesse classi e firme di metodi disponibili nella versione pubblica di [Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ma permette anche di connettersi agli account Azure Cosmos DB tramite l'API di tabella (anteprima). Per una guida introduttiva e un'esercitazione che usa l'API di tabella di Azure Cosmos DB, vedere gli articoli seguenti:
- Guida introduttiva: [Azure Cosmos DB: Creare un'applicazione .NET tramite l'API di tabella](create-table-dotnet.md)
- Esercitazione: [Azure Cosmos DB: sviluppare con l'API Table in .NET](tutorial-develop-table-dotnet.md)

Per altre informazioni sull'uso dell'API di tabella, vedere l'articolo relativo alle [domande frequenti sullo sviluppo con l'API di tabella](faq.md#develop-with-the-table-api-preview).

## <a name="developing-with-the-azure-table-storage"></a>Sviluppo con Archiviazione tabelle di Azure

I numerosi SDK e le esercitazioni relative a [Archiviazione tabelle di Azure](table-storage-overview.md) sono disponibili nella sezione relativa ad [Archiviazione tabelle di Azure](table-storage-overview.md). Questi articoli sono sottoposti ad aggiornamento in base alla disponibilità dell'interoperabilità tra gli SDK di Archiviazione tabelle di Azure e le API di Azure Cosmos DB.  





