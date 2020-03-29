---
title: Creare una risorsa cluster del servizio Azure KubernetesCreate an Azure Kubernetes Service cluster resource
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa del servizio Azure Kubernetes (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383481"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster del servizio Azure KubernetesCreate an Azure Kubernetes Service cluster resource

1. Passare al [servizio Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selezionare **Crea**.

1. Nella scheda **Nozioni di base** immettere le informazioni seguenti:

    |Impostazione|valore|
    |--|--|
    |Subscription|Scegliere la sottoscrizione appropriata.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|
    |Nome del cluster Kubernetes|Immettere un nome (minuscolo).|
    |Region|Selezionare una posizione vicina.|
    |Versione Kubernetes|Qualunque sia il valore contrassegnato come **(predefinito)**.|
    |Prefisso nome DNS|Creato automaticamente, ma è possibile eseguire l'override.|
    |Dimensioni nodo|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Conteggio nodi|Lasciare il dispositivo di scorrimento sul valore predefinito.|

1. Nella scheda **Scalabilità** lasciare **Nodi virtuali** e Set di **scalabilità della macchina virtuale** impostati sui valori predefiniti.
1. Nella scheda **Autenticazione** lasciare **Servizio entità servizio** e Abilita **RBAC** impostato sui valori predefiniti.
1. Nella scheda **Rete** immettere le opzioni seguenti:

    |Impostazione|valore|
    |--|--|
    |Routing di applicazioni HTTP|No|
    |Configurazione delle impostazioni di rete|Basic|

1. Nella scheda **Monitoraggio** verificare che **Abilita monitoraggio contenitore** sia impostato su **Sì**e lasciare il valore predefinito dell'area di lavoro **Log Analytics.**
1. Nella scheda **Tag** lasciare le coppie nome/valore vuote per il momento.
1. Selezionare **Revisione e creazione**.
1. Al termine della convalida, selezionare **Crea**.

> [!NOTE]
> Se la convalida non riesce, potrebbe essere a causa di un errore "Entità servizio". Tornare alla scheda **Autenticazione** e quindi tornare a **Revisione e creazione**, dove deve essere eseguita la convalida e quindi passare.
