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
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877831"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster del servizio Kubernetes di Azure

1. Passare al [servizio Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selezionare **Crea**.

1. Nella scheda informazioni di **base** immettere le informazioni seguenti:

    |Impostazione|valore|
    |--|--|
    |Subscription|Scegliere la sottoscrizione appropriata.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|
    |Nome del cluster Kubernetes|Immettere un nome (minuscolo).|
    |Region|Selezionare una località vicina.|
    |Versione di Kubernetes|Qualsiasi valore è contrassegnato come **(impostazione predefinita)**.|
    |Prefisso nome DNS|Creato automaticamente, ma è possibile eseguire l'override di.|
    |Dimensioni nodo|DS2 standard v2:<br>`2 vCPUs`, `7 GB`|
    |Numero di nodi|Lasciare il dispositivo di scorrimento sul valore predefinito.|

1. Nella scheda **scala** lasciare i **nodi virtuali** e i **set di scalabilità di VM** impostati sui rispettivi valori predefiniti.
1. Nella scheda **autenticazione** lasciare l' **entità servizio** e **abilitare RBAC** impostato sui valori predefiniti.
1. Nella scheda **rete** immettere le selezioni seguenti:

    |Impostazione|Valore|
    |--|--|
    |Routing di applicazioni HTTP|No|
    |Configurazione delle impostazioni di rete|Basic|

1. Nella scheda **monitoraggio** verificare che **Enable container Monitoring** sia impostato su **Sì**e lasciare **log Analytics area di lavoro** come valore predefinito.
1. Nella scheda **tag** lasciare vuote le coppie nome/valore per il momento.
1. Selezionare **revisione e creazione**.
1. Al termine della convalida, selezionare **Crea**.

> [!NOTE]
> Se la convalida ha esito negativo, la causa potrebbe essere un errore di "entità servizio". Tornare alla scheda **Authentication (autenticazione** ), quindi tornare a **Review + create**, in cui la convalida deve essere eseguita e quindi passata.
