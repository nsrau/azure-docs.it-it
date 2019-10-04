---
title: Creare azioni e risorse personalizzate in Azure
description: Questa esercitazione descrive come creare azioni e risorse personalizzate in Azure Resource Manager. Illustra anche l'interoperabilità dei flussi di lavoro personalizzati con i modelli di Azure Resource Manager, l'interfaccia della riga di comando di Azure, Criteri di Azure e Log attività di Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172932"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Creare azioni e risorse personalizzate in Azure

Un provider personalizzato è un contratto tra Azure e un endpoint. Con i provider personalizzati, è possibile cambiare i flussi di lavoro in Azure aggiungendo nuove API in Azure Resource Manager. Con queste API personalizzate, Resource Manager consente di usare nuove funzionalità di distribuzione e gestione.

Questa esercitazione illustra un semplice esempio su come aggiungere nuove azioni e risorse in Azure e come integrarle.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurare Funzioni di Azure per i provider personalizzati di Azure

La prima parte di questa esercitazione descrive come configurare un'app per le funzioni di Azure da usare con i provider personalizzati:

- [Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md)

I provider personalizzati possono essere usati con qualsiasi URL pubblico.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Creare un endpoint RESTful per i provider personalizzati

La seconda parte di questa esercitazione descrive come creare un endpoint RESTful per i provider personalizzati:

- [Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Creare e usare un provider personalizzato

La terza parte di questa esercitazione descrive come creare un provider personalizzato e usare le relative azioni e risorse personalizzate:

- [Creare e usare un provider personalizzato](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state fornite informazioni sui provider personalizzati e su come crearli. Per continuare con l'esercitazione successiva, vedere [Esercitazione: Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md).

Per informazioni di riferimento o una guida di avvio rapido, ecco alcuni collegamenti utili:

- [Guida introduttiva: Creare un provider di risorse personalizzato e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
