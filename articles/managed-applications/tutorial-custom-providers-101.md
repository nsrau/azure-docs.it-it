---
title: Creare azioni e risorse personalizzate in Azure
description: Questa esercitazione illustra come creare azioni e risorse personalizzate in Azure Resource Manager e come integrarle in flussi di lavoro personalizzati per modelli di Azure Resource Manager, interfaccia della riga di comando di Azure, Criteri di Azure e log attività.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799190"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Creare azioni e risorse personalizzate in Azure

I provider personalizzati consentono di personalizzare i flussi di lavoro in Azure. Un provider personalizzato è un contratto tra Azure e un `endpoint`. Consente l'aggiunta di nuove API personalizzate in Azure Resource Manager per abilitare nuove funzionalità di distribuzione e gestione. Questa esercitazione illustra un semplice esempio su come aggiungere nuove azioni e risorse in Azure e come integrarle.

Questa esercitazione è suddivisa nei passaggi seguenti:

- Configurare Funzioni di Azure per i provider personalizzati di Azure
- Creazione di un endpoint RESTful per provider personalizzati
- Creazione e utilizzo del provider personalizzato

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurare Funzioni di Azure per i provider personalizzati di Azure

Questa parte dell'esercitazione include istruzioni dettagliate su come configurare Funzioni di Azure per l'uso con provider personalizzati. I provider personalizzati possono essere usati con qualsiasi URL pubblico.

- [Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Creazione di un endpoint RESTful per provider personalizzati

Questa parte dell'esercitazione include istruzioni dettagliate per la creazione di un endpoint RESTful per i provider personalizzati.

- [Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Creazione e utilizzo del provider personalizzato

Questa parte dell'esercitazione include istruzioni dettagliate su come creare un provider personalizzato e usare le relative azioni e risorse personalizzate.

- [Creare e usare un provider personalizzato di Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati e su come crearli. Procedere con il passaggio successivo dell'esercitazione:

- [Esercitazione: Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md)

Per informazioni di riferimento o una guida di avvio rapido, ecco alcuni collegamenti utili:

- [Guida introduttiva: Creare un provider personalizzato e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Procedure: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
