---
title: Creare un'offerta nello Stack di Azure | Documenti Microsoft
description: Come un amministratore del cloud, informazioni su come creare un'offerta per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 2666aacbbaf44ae9b3bcf2df480e835457e6f449
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider di presentano agli utenti per l'acquisto o sottoscrivere. Questo documento viene illustrato come creare un'offerta che include il [piano creato](azure-stack-create-plan.md) nell'ultimo passaggio. Questa offerta fornisce ai sottoscrittori la possibilità di eseguire il provisioning di macchine virtuali.

1. Accedi al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external) > fare clic su **New** > **Tenant offre + piani**  >   **Offrono**.

   ![](media/azure-stack-create-offer/image01.png)
2. Nel **offrono nuove** riquadro, compilare **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è il nome descrittivo dell'offerta. Questo nome descrittivo è l'unica informazione sull'offerta che gli utenti visualizzeranno durante la sottoscrizione. Pertanto, assicurarsi di utilizzare un nome intuitivo che consente all'utente di capire ciò che viene fornito con l'offerta. e può essere visualizzato solo dall'amministratore. È il nome che gli amministratori utilizzano per funzionare con l'offerta come risorsa di gestione risorse di Azure.

   ![](media/azure-stack-create-offer/image01a.png)
3. Fare clic su **Base piani** per aprire la **pianificare** riquadro, selezionare i piani che si desidera includere nell'offerta e quindi fare clic su **selezionare**. Fare clic su **Crea** per creare l'offerta.

   ![](media/azure-stack-create-offer/image02.png)
4. Fare clic su **tutte le risorse**, cercare la nuova offerta, fare clic su nuova offerta, fare clic su **modifica dello stato**, quindi fare clic su **pubblica**.

   ![](media/azure-stack-create-offer/image03.png)

Offerte devono essere resa pubbliche per gli utenti ottenere la visualizzazione completa per la sottoscrizione. Offerte possono essere:

* **Pubblica**: visibile agli utenti.
* **Privato**: visibile solo agli amministratori di cloud. Utile durante l'elaborazione il piano o l'offerta, o se l'amministratore del cloud desidera [ogni sottoscrizione per gli utenti di creare](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
* **Rimossi**: chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile utilizzare autorizzazioni rimosse per evitare future sottoscrizioni, ma lasciare invariati i sottoscrittori correnti.

Modifiche per l'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, potrebbe essere necessario disconnessioni per visualizzare la nuova sottoscrizione nel selettore"sottoscrizione" durante la creazione di gruppi di risorse o risorse.

> [!NOTE]
>È possibile creare anche offerte predefinite, i piani e le quote tramite PowerShell, come illustrato nel [file Leggimi di amministratore del servizio Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Passaggi successivi
[Creazione di sottoscrizioni](azure-stack-subscribe-plan-provision-vm.md)      
[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
