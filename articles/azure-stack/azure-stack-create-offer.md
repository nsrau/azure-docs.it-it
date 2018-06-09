---
title: Creare un'offerta nello Stack di Azure | Documenti Microsoft
description: Come un amministratore del cloud, informazioni su come creare un'offerta per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247518"
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider di presentano agli utenti per acquistare o sottoscrivere. Questo documento illustra come creare un'offerta che include il [piano creato](azure-stack-create-plan.md). Questa offerta fornisce ai sottoscrittori la possibilità di configurare le macchine virtuali.

1. Accedi al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external) e selezionare **New** > **Tenant offre + piani** > **offrono**.

   ![Creare un'offerta](media/azure-stack-create-offer/image01.png)
  
2. Sotto **offrono nuove**, immettere un **nome visualizzato** e un **nome risorsa**, quindi in **gruppo di risorse**, selezionare **crea nuova** oppure **utilizzare esistente**. Il nome visualizzato è il nome descrittivo dell'offerta. Questo nome descrittivo è l'unica informazione sull'offerta che visualizzeranno gli utenti quando hanno eseguito la sottoscrizione a un'offerta. Utilizzare un nome intuitivo che consente agli utenti di comprendere che cosa viene fornito con l'offerta. e può essere visualizzato solo dall'amministratore. È il nome che gli amministratori utilizzano per funzionare con l'offerta come risorsa di gestione risorse di Azure.

   ![Nuova offerta](media/azure-stack-create-offer/image01a.png)
  
3. Selezionare **piani di Base** per aprire la **pianificare**. Selezionare i piani che si desidera includere nell'offerta e quindi scegliere **selezionare**. Per creare l'istruzione select offerta **crea**.

   ![selezione piano](media/azure-stack-create-offer/image02.png)
  
4. Dopo aver creato l'offerta, è possibile modificare il relativo stato. Offerte fare le dovute *pubblica* per gli utenti di ottenere l'intera vista quando hanno eseguito la sottoscrizione. Offerte possono essere:

   - **Pubblica**: visibile agli utenti.
   - **Privato**: visibili solo agli amministratori di cloud. Questa impostazione è utile durante l'elaborazione al piano o l'offerta, o se l'amministratore del cloud desidera [creare ogni sottoscrizione per gli utenti](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Rimossi**: chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile utilizzare autorizzazioni rimosse per evitare future sottoscrizioni, ma lasciare inalterate sottoscrittori correnti.

   > [!TIP]  
   > Le modifiche per l'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, gli utenti potrebbe essere necessario disconnettersi e accedere nuovamente al portale per gli utenti per visualizzare la nuova offerta.

   Per modificare lo stato dell'offerta:

   - **1803 e versioni successive**:  
     Nella panoramica dell'offerta, selezionare **lo stato di accesso facilitato**. Scegliere lo stato da usare (ad esempio *pubblico*) e quindi selezionare **salvare**.
 
     ![Selezionare lo stato di accesso facilitato](media/azure-stack-create-offer/change-state.png)

     In alternativa, dopo l'accesso di un'offerta è possibile passare alla **offrono impostazioni**. Selezionare **lo stato di accesso facilitato** per modificare lo stato.

   - **Prima della versione 1803**:  
     Selezionare **tutte le risorse**, cercare l'offerta di nuovo e quindi selezionare la nuova offerta. Selezionare **modifica dello stato**, quindi selezionare **pubblica**.

   > [!NOTE]
   > È inoltre possibile utilizzare PowerShell per creare offerte predefinite, i piani e le quote. Per altre informazioni, vedere [file Leggimi di amministratore del servizio Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>Passaggi successivi

- [Creazione di sottoscrizioni](azure-stack-subscribe-plan-provision-vm.md)
- [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
