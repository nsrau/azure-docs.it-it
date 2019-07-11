---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare un azure kubernetes risorsa (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: eb2d609c3a7dfd769c6a19854366ac5e45fddb7d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712456"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Creare una risorsa cluster Azure Kubernetes Service (AKS)

1. Andare alla [creare](https://ms.portal.azure.com/#create/microsoft.aks) per i servizi Kubernetes.

1. Nel **nozioni di base** , immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibili|
    |Nome del cluster Kubernetes|Nome desiderato (lettere minuscolo)|
    |Region|Selezionare una località nelle vicinanze|
    |Versione di Kubernetes|1.12.8 (impostazione predefinita)|
    |Prefisso del nome DNS|Create automaticamente, ma, facoltativamente, è possibile eseguire l'override|
    |Dimensioni del nodo|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Numero di nodi|Lasciare il dispositivo di scorrimento sul valore predefinito|

1. Nel **scalabilità** scheda, lasciare il *nodi virtuali* e *set di scalabilità di macchine virtuali* i valori predefiniti.
1. Nel **Authentication** scheda, lasciare *dell'entità servizio* e *abilitare RBAC* i valori predefiniti.
1. Nel **Networking** , immettere le selezioni seguenti:

    |Impostazione|Valore|
    |--|--|
    |Routing di applicazioni HTTP|No|
    |Configurazione delle impostazioni di rete|Basic|

1. Nel **Monitoring** verificare che l'opzione *Abilita monitoraggio contenitore* è impostata su **Sì** e lasciare il *dell'area di lavoro di Log Analitica* come relativo valore predefinito
1. Nel **tag** scheda, lasciare vuote le coppie nome/valore per il momento.
1. Fare clic su **esaminare e creare**.
1. Dopo la convalida ha esito positivo, fare clic su **Create**.

> [!NOTE]
> Se la convalida non riesce, è possibile a causa di un *entità servizio* errore. Tornare al *autenticazione* scheda e successivamente *revisione + Crea* in cui la convalida deve eseguire e quindi passare.
