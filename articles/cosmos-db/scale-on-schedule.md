---
title: Ridimensionare il database Cosmos di Azure in base a una pianificazione usando il timer di Funzioni di AzureScale Azure Cosmos DB on a schedule by using Azure Functions timer
description: Informazioni su come scalare le modifiche della velocità effettiva in Azure Cosmos DB usando PowerShell e Funzioni di Azure.Learn how to scale changes in throughput in Azure Cosmos DB using PowerShell and Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935168"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Ridimensionare la velocità effettiva del database Cosmos di Azure usando il trigger Timer di Funzioni di AzureScale Azure Cosmos DB throughput by using Azure Functions Timer trigger

Le prestazioni di un account Cosmos di Azure si basano sulla quantità di velocità effettiva di cui è stato eseguito il provisioning espressa in unità di richiesta al secondo (RU/s). Il provisioning ha una seconda granularità e viene fatturato in base alle RU/s all'ora più elevate. Questo modello di capacità con provisioning consente al servizio fornire una velocità effettiva prevedibile e coerente, con bassa latenza e disponibilità elevata garantite. La maggior parte dei carichi di lavoro di produzione offre queste funzionalità. Tuttavia, negli ambienti di sviluppo e test in cui Azure Cosmos DB viene usato solo durante l'orario di lavoro, è possibile aumentare la velocità effettiva al mattino e ridurre la sera dopo l'orario di lavoro.

È possibile impostare la velocità effettiva tramite i modelli di [Azure Resource Manager](resource-manager-samples.md), [l'interfaccia della riga di comando di Azure](cli-samples.md)e [PowerShell](powershell-samples-sql.md)per gli account API Core (SQL) o usando gli SDK di Azure Cosmos DB specifici della lingua. Il vantaggio dell'uso di modelli di Resource Manager, dell'interfaccia della riga di comando di Azure o PowerShell è che supportano tutte le API del modello di database di Azure Cosmos.The benefit of using Resource Manager Templates, Azure CLI or PowerShell is that they support all Azure Cosmos DB model APIs.

## <a name="throughput-scheduler-sample-project"></a>Progetto di esempio dell'utilità di pianificazione della velocità effettiva

Per semplificare il processo per scalare Azure Cosmos DB in base a una pianificazione, è stato creato un progetto di esempio denominato [Azure Cosmos throughput scheduler](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Questo progetto è un'app Funzioni di Azure con due trigger timer: "ScaleUpTrigger" e "ScaleDownTrigger". I trigger eseguono uno script di PowerShell che imposta `resources.json` la velocità effettiva in ogni risorsa come definito nel file in ogni trigger. ScaleUpTrigger è configurato per l'esecuzione alle 8 del mattino UTC e ScaleDownTrigger è configurato `function.json` per l'esecuzione alle 18:00 UTC e questi orari possono essere facilmente aggiornati all'interno del file per ogni trigger.

È possibile clonare il progetto in locale, modificarlo per specificare le risorse di Azure Cosmos DB per aumentare e ridurre e la pianificazione per l'esecuzione. Successivamente è possibile distribuirlo in una sottoscrizione di Azure e proteggerlo usando l'identità del servizio gestito con autorizzazioni di controllo di accesso basato sui ruoli con il ruolo "Operatore database Cosmos di Azure" per impostare la velocità effettiva negli account di Azure Cosmos.Later you can deploy it in an Azure subscription and secure it using managed service identity with [Role-based Access Control](role-based-access-control.md) (RBAC) permissions with the "Azure Cosmos DB operator" role to set throughput on your Azure Cosmos accounts.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni e scaricare l'esempio da Utilità di [pianificazione della velocità effettiva di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
