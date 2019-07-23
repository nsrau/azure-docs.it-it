---
title: Creare una risorsa cluster del servizio Kubernetes di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377405"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster del servizio Kubernetes di Azure

1. Passare al [servizio Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selezionare **Crea**.

1. Nella scheda informazioni di **base** immettere le informazioni seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Scegliere la sottoscrizione appropriata.|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile.|
    |Nome del cluster Kubernetes|Immettere un nome (minuscolo).|
    |Region|Selezionare una località vicina.|
    |Versione di Kubernetes|1.12.8 (impostazione predefinita).|
    |Prefisso nome DNS|Creato automaticamente, ma è possibile eseguire l'override di.|
    |Dimensioni del nodo|DS2 standard v2:<br>`2 vCPUs`, `7 GB`|
    |Numero di nodi|Lasciare il dispositivo di scorrimento sul valore predefinito.|

1. Nella scheda **scala** lasciare i **nodi virtuali** e i **set di scalabilità di macchine virtuali (anteprima)** impostati sui valori predefiniti.
1. Nella scheda **autenticazione** lasciare l' **entità servizio** e **abilitare RBAC** impostato sui valori predefiniti.
1. Nella scheda **rete** immettere le selezioni seguenti:

    |Impostazione|Value|
    |--|--|
    |Routing HTTP dell'applicazione|No|
    |Configurazione delle impostazioni di rete|Basic|

1. Nella scheda **monitoraggio** verificare che **Enable container Monitoring** sia impostato su **Sì**e lasciare **log Analytics area di lavoro** come valore predefinito.
1. Nella scheda **tag** lasciare vuote le coppie nome/valore per il momento.
1. Selezionare **revisione e creazione**.
1. Al termine della convalida, selezionare **Crea**.

> [!NOTE]
> Se la convalida ha esito negativo, la causa potrebbe essere un errore di "entità servizio". Tornare alla scheda **Authentication (autenticazione** ), quindi tornare a **Review + create**, in cui la convalida deve essere eseguita e quindi passata.
