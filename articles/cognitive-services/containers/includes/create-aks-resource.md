---
title: Creare una risorsa cluster del servizio Kubernetes di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383481"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster del servizio Kubernetes di Azure

1. Passare al [servizio Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selezionare **Crea**.

1. Nella scheda informazioni di **base** immettere le informazioni seguenti:

    |Impostazione|Valore|
    |--|--|
    |sottoscrizione|Scegliere la sottoscrizione appropriata.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|
    |Nome del cluster Kubernetes|Immettere un nome (minuscolo).|
    |Area|Selezionare una località vicina.|
    |Versione di Kubernetes|Qualsiasi valore è contrassegnato come **(impostazione predefinita)** .|
    |Prefisso nome DNS|Creato automaticamente, ma è possibile eseguire l'override di.|
    |Dimensioni del nodo|DS2 standard v2:<br>`2 vCPUs`, `7 GB`|
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
