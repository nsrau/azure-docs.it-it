---
title: Copiare o clonare una data factory in Azure Data Factory
description: Informazioni su come copiare o clonare una data factory in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552971"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiare o clonare una data factory in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come copiare o clonare una data factory in Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casi d'uso per la clonazione di una data factory

Ecco alcune delle circostanze in cui potrebbe essere utile copiare o clonare una data factory:

- **Spostare data factory** in una nuova area. Se si vuole spostare il Data Factory in un'area diversa, il modo migliore consiste nel creare una copia nell'area di destinazione ed eliminarne una esistente.

- **Ridenominazione Data Factory**. Azure non supporta la ridenominazione di risorse. Se si desidera rinominare una data factory, è possibile clonare il data factory con un nome diverso ed eliminare quello esistente.

- **Modifiche di debug** quando le funzionalità di debug non sono sufficienti. Nella maggior parte degli scenari, è possibile usare [debug](iterative-development-debugging.md). In altri casi, è più sensato testare le modifiche apportate in un ambiente sandbox clonato. Ad esempio, il modo in cui le pipeline ETL con parametri si comporteranno quando un trigger viene attivato al momento dell'arrivo dei file rispetto alla finestra temporale a cascata, potrebbe non essere facilmente verificabile tramite debug. In questi casi, potrebbe essere necessario clonare un ambiente sandbox per la sperimentazione. Poiché Azure Data Factory addebitato principalmente il numero di esecuzioni, una seconda Factory non comporta addebiti aggiuntivi.

## <a name="how-to-clone-a-data-factory"></a>Come clonare una Data factory

1. Come prerequisito, è necessario innanzitutto creare la data factory di destinazione dal portale di Azure.

1. In modalità GIT:
    1. Ogni volta che si pubblica dal portale, il modello di Gestione risorse della factory viene salvato in GIT nel ramo di pubblicazione di ADF \_
    1. Connettere la nuova factory allo _stesso_ repository e compilare dal ramo di \_ pubblicazione di ADF. Le risorse, ad esempio pipeline, set di impostazioni e trigger, porteranno attraverso

1. Se si è in modalità Live:
    1. Data Factory interfaccia utente consente di esportare l'intero payload del data factory in un file di modello Gestione risorse e in un file di parametri. È possibile accedervi dal pulsante **modello ARM \ esporta gestione risorse modello** nel portale.
    1. È possibile apportare le modifiche appropriate al file dei parametri e scambiare i nuovi valori per la nuova factory
    1. Successivamente, è possibile distribuirlo tramite i metodi di distribuzione standard di Gestione risorse modello.

1. Se si dispone di un IntegrationRuntime SelfHosted nella Factory di origine, è necessario precrearlo con lo stesso nome nella Factory di destinazione. Se si vuole condividere la Integration Runtime SelfHosted tra diversi Factory, è possibile usare il modello pubblicato [qui](create-shared-self-hosted-integration-runtime-powershell.md) in condivisione di SelfHosted IR.

1. Per motivi di sicurezza, il modello di Gestione risorse generato non conterrà informazioni segrete, ad esempio le password per i servizi collegati. Pertanto, è necessario fornire le credenziali come parametri di distribuzione. Se le credenziali vengono inserite manualmente, è consigliabile recuperare le stringhe di connessione e le password da Azure Key Vault. [Altre informazioni](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Passaggi successivi

Esaminare le indicazioni per la creazione di una data factory nel portale di Azure in [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md).
