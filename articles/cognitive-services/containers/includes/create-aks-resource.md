---
title: Creare una risorsa cluster Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877463"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Creare una risorsa cluster Azure Kubernetes Service

1. Passare a [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) e selezionare **crea**.

1. Nel **nozioni di base** , immettere le informazioni seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Scegliere la sottoscrizione appropriata|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibili|
    |Nome del cluster Kubernetes|Immettere un nome (in minuscolo)|
    |Region|Selezionare una località nelle vicinanze|
    |Versione di Kubernetes|1.12.8 (impostazione predefinita)|
    |Prefisso del nome DNS|Create automaticamente, ma è possibile eseguire l'override|
    |Dimensioni del nodo|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Numero di nodi|Lasciare il dispositivo di scorrimento sul valore predefinito|

1. Nel **scalabilità** scheda, lasciare **nodi virtuali** e **set di scalabilità di macchine virtuali (anteprima)** impostate sui valori predefiniti.
1. Nel **Authentication** scheda, lasciare **entità servizio** e **abilitare RBAC** impostate sui valori predefiniti.
1. Nel **Networking** , immettere le selezioni seguenti:

    |Impostazione|Value|
    |--|--|
    |Routing di applicazioni HTTP|No|
    |Configurazione delle impostazioni di rete|Basic|

1. Nel **Monitoring** scheda, verificare che **Abilita monitoraggio contenitore** è impostata su **Sì**e lasciare **dell'area di lavoro di Log Analitica** come il valore predefinito.
1. Nel **tag** scheda, lasciare vuote le coppie nome/valore per il momento.
1. Selezionare **esaminare e creare**.
1. Dopo la convalida ha esito positivo, selezionare **Create**.

> [!NOTE]
> Se la convalida non riesce, è possibile a causa di un errore "Entità servizio". Tornare al **Authentication** scheda e quindi tornare alla **revisione + Crea**, in cui la convalida deve eseguire e quindi passare.
