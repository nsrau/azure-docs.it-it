---
title: Creare una risorsa cluster del servizio Kubernetes di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di Azure Kubernetes Service (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017865"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster del servizio Kubernetes di Azure

1. Passare al [servizio Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selezionare **Crea**.

1. Nella scheda **Informazioni di base** digitare le informazioni seguenti:

    |Impostazione|Valore|
    |--|--|
    |Subscription|Scegliere la sottoscrizione appropriata.|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile.|
    |Nome del cluster Kubernetes|Immettere un nome (minuscolo).|
    |Region|Selezionare una località limitrofa.|
    |Versione di Kubernetes|Qualsiasi valore è contrassegnato come **(impostazione predefinita)**.|
    |Prefisso nome DNS|Creato automaticamente, ma è possibile eseguire l'override di.|
    |Dimensioni nodo|DS2 standard v2:<br>`2 vCPUs`, `7 GB`|
    |Numero di nodi|Lasciare il dispositivo di scorrimento sul valore predefinito.|

1. Nella scheda **pool di nodi** lasciare i **nodi virtuali** e i **set di scalabilità di VM** impostati sui rispettivi valori predefiniti.
1. Nella scheda **autenticazione** lasciare l' **entità servizio** e **abilitare RBAC** impostato sui valori predefiniti.
1. Nella scheda **rete** immettere le selezioni seguenti:

    |Impostazione|Valore|
    |--|--|
    |Routing di applicazioni HTTP|No|
    |Configurazione della rete|Basic|

1. Nella scheda **Integrations (integrazioni** ) assicurarsi che il **monitoraggio dei contenitori** sia impostato su **abilitato** e lasciare **log Analytics area di lavoro** come valore predefinito.
1. Nella scheda **tag** lasciare vuote le coppie nome/valore per il momento.
1. Selezionare **revisione e creazione**.
1. Al termine della convalida, selezionare **Crea**.

> [!NOTE]
> Se la convalida ha esito negativo, la causa potrebbe essere un errore di "entità servizio". Tornare alla scheda **Authentication (autenticazione** ), quindi tornare a **Review + create**, in cui la convalida deve essere eseguita e quindi passata.
