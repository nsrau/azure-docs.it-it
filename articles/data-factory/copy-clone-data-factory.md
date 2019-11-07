---
title: Copiare o clonare una data factory in Azure Data Factory
description: Informazioni su come copiare o clonare una data factory in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: bafe70655f05b5dda32f51393591f82b4b5625f1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678119"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiare o clonare una data factory in Azure Data Factory

Questo articolo descrive come copiare o clonare una data factory in Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casi d'uso per la clonazione di una data factory

Ecco alcune delle circostanze in cui potrebbe essere utile copiare o clonare una data factory:

-   **Ridenominazione di risorse**. Azure non supporta la ridenominazione di risorse. Se si vuole rinominare una data factory, è possibile clonarla con un nome diverso, eliminando quella esistente.

-   **Modifiche di debug** quando le funzionalità di debug non sono sufficienti. Per testare le modifiche, in alcuni casi, è consigliabile testarle in una factory diversa prima di applicarle alla principale. Nella maggior parte degli scenari, è possibile usare Debug. Tuttavia, le modifiche relative ai trigger potrebbero non essere testabili facilmente senza eseguirne l'archiviazione; ad esempio il comportamento delle modifiche quando un trigger viene invocato automaticamente o in un certo intervallo d tempo. In questi casi,risulta particolarmente utile clonare la factory applicando lì le modifiche. La seconda factory non comporta costi aggiuntivi, poiché gli addebiti per Azure Data Factory dipendono principalmente dal numero di esecuzioni.

## <a name="how-to-clone-a-data-factory"></a>Come clonare una Data factory

1. L'interfaccia utente di Data Factory nel portale di Azure consente di esportare l'intero payload della data factory in un modello di Resource Manager, assieme a un file di parametro che consente di modificare i valori desiderati durante la clonazione della factory.

1. Il prerequisito consiste nel creare la data factory di destinazione dal portale di Azure.

1. Se si dispone di un IntegrationRuntime SelfHosted nella Factory di origine, è necessario precrearlo con lo stesso nome nella Factory di destinazione. Se si vuole condividere il SelfHosted IRs tra diversi Factory, è possibile usare il modello pubblicato [qui](source-control.md#best-practices-for-git-integration).

1. Ogni volta che si pubblica dal portale in modalità GIT, il modello di Resource Manager della factory viene salvato in GIT nel ramo adf_publish del repository.

1. Per altri scenari, può essere scaricato il modello di Resource Manager facendo clic sul pulsante nel portale **Export Resource Manager template (Esporta modello di Resource Manager)** .

1. Dopo aver scaricato il modello di Resource Manager, è possibile distribuirlo tramite i metodi standard di distribuzione in Resource Manager.

1. Per motivi di sicurezza, il modello di Resource Manager generato non contiene informazioni segrete, ad esempio le password per i servizi collegati. Di conseguenza, è necessario fornire queste password come parametri di distribuzione. Se non si vuole fornire questi parametri, sarà necessario ottenere le stringhe di connessione e le password dei servizi collegati di Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le indicazioni per la creazione di una data factory nel portale di Azure in [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md).
