---
title: Creare un'offerta nello Stack di Azure | Documenti Microsoft
description: Come un amministratore del cloud, informazioni su come creare un'offerta per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider di presentano agli utenti per l'acquisto o sottoscrivere. Questo documento viene illustrato come creare un'offerta che include il [piano creato](azure-stack-create-plan.md) nell'ultimo passaggio. Questa offerta fornisce ai sottoscrittori la possibilità di eseguire il provisioning di macchine virtuali.

1. Accedi al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external) > fare clic su **New** > **Tenant offre + piani**  >   **Offrono**.

   ![](media/azure-stack-create-offer/image01.png)
2. Nel **offrono nuove** pannello, compilare **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è nome descrittivo dell'offerta ed è l'unica informazione sull'offerta che gli utenti visualizzeranno quando la sottoscrizione. Pertanto, assicurarsi di utilizzare un nome intuitivo che consente all'utente di capire ciò che viene fornito con l'offerta. e può essere visualizzato solo dall'amministratore. È il nome che gli amministratori utilizzano per funzionare con l'offerta come risorsa di gestione risorse di Azure.

   ![](media/azure-stack-create-offer/image01a.png)
3. Fare clic su **Base piani** e, nel **pianificare** pannello, selezionare i piani che si desidera includere nell'offerta e quindi fare clic su **selezionare**. Fare clic su **Crea** per creare l'offerta.

   ![](media/azure-stack-create-offer/image02.png)
4. Fare clic su **tutte le risorse**, cercare la nuova offerta, fare clic su nuova offerta, fare clic su **modifica dello stato**, quindi fare clic su **pubblica**.

   ![](media/azure-stack-create-offer/image03.png)

Offerte devono essere resa pubbliche per gli utenti ottenere la visualizzazione completa per la sottoscrizione. Offerte possono essere:

* **Pubblica**: visibile agli utenti.
* **Privato**: visibile solo agli amministratori di cloud. Utile durante l'elaborazione il piano o l'offerta, o se l'amministratore del cloud deve approvare tutte le sottoscrizioni.
* **Rimossi**: chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile utilizzare autorizzazioni rimosse per evitare future sottoscrizioni, ma lasciare invariati i sottoscrittori correnti.

Modifiche per l'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, potrebbe essere necessario disconnessioni per visualizzare la nuova sottoscrizione nel selettore"sottoscrizione" durante la creazione di gruppi di risorse o risorse.

> [!NOTE]
>È possibile creare anche offerte predefinite, i piani e le quote tramite PowerShell, come illustrato nel [file Leggimi di amministratore del servizio Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Passaggi successivi
[Sottoscrivere un'offerta e quindi eseguire il provisioning di una VM](azure-stack-subscribe-plan-provision-vm.md)
