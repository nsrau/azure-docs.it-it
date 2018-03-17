---
title: In questa esercitazione, si crea un'offerta di Azure Stack | Documenti Microsoft
description: Informazioni su come creare un'offerta di Azure Stack incluse piani e le quote.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Esercitazione: offrono servizi IaaS di Azure Stack
Un amministratore di cloud di Azure Stack, è possibile creare offerte che possono sottoscrivere gli utenti (talvolta detto tenant). Con la sottoscrizione, gli utenti potranno quindi utilizzare i servizi di Azure Stack.

In questa esercitazione viene illustrato come creare un'offerta per consentire agli utenti di creare macchine virtuali in base all'immagine di Windows Server 2016 Datacenter marketplace Azure Stack caricato nel [esercitazione precedente](asdk-marketplace-item.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'offerta
> * Creare un piano
> * Impostare quote
> * Offerta insieme al ruolo public

Nello Stack di Azure, servizi vengono recapitati a utenti che utilizzano i piani, offerte e sottoscrizioni. Gli utenti possano sottoscrivere più offerte. Offerte possono avere uno o più piani e i piani possono avere uno o più servizi, come illustrato nel diagramma seguente:

![I piani, offerte e sottoscrizioni](media/asdk-offer-services/sop.png)

Come operatore dello Stack di Azure che offrono servizi, viene chiesto prima di creare l'offerta, quindi un piano e infine le quote. Dopo aver creata un'offerta, gli utenti di Azure Stack possono quindi effettuare la sottoscrizione tramite il portale per gli utenti.

## <a name="create-an-offer"></a>Creare un'offerta
**Offre** sono gruppi di uno o più piani che gli operatori di Azure Stack presentano agli utenti per l'acquisto o sottoscrivere.

1. Accedi ai [portale Azure Stack](https://adminportal.local.azurestack.external) come un amministratore del cloud.

2. Fare clic su **nuove** > **offre + piani** > **offrono**.

   ![Nuova offerta](media/asdk-offer-services/new-offer.png)

2. Nel **offrono nuove** sezione, compilare **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è pubblica nome descrittivo dell'offerta visualizzato dagli utenti. Solo l'operatore cloud è possibile visualizzare il nome di risorsa, che viene utilizzato dagli amministratori per operare con l'offerta come una risorsa di gestione risorse di Azure.

   ![Nome visualizzato](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Creare un piano
Dopo aver immesso le informazioni di base offerta, è necessario aggiungere almeno un piano di basa per l'offerta. 

**Piani** consentire gli operatori di Azure Stack ai servizi di gruppo e le rispettive quote associati, per essere offerti agli utenti.

1. Fare clic su **piani di Base**e il **piano** fare clic su **Aggiungi** per aggiungere un nuovo piano per l'offerta.

   ![Aggiungere un piano](media/asdk-offer-services/new-plan.png)

2. Nel **nuovo piano** sezione, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. Solo l'operatore cloud è possibile visualizzare il nome di risorsa utilizzato dagli operatori di cloud con il piano come una risorsa di gestione risorse di Azure.

   ![Nome del piano di visualizzazione](media/asdk-offer-services/plan-display-name.png)

3. Successivamente, selezionare i servizi da includere nel piano. Per offrire servizi IaaS, fare clic su **Services**, selezionare **Microsoft. COMPUTE**, **Microsoft. Network**, e **appartenga**e quindi Fare clic su **selezionare**.

   ![Servizi del piano](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Impostare quote
Ora che l'offerta è un piano che include i servizi, è necessario impostare le quote per ognuno di essi. 

**Le quote** determinare la quantità di risorse che un utente può utilizzare per ogni servizio incluso nel piano di offerto.

1. Fare clic su **quote**e quindi selezionare il servizio per il quale si desidera creare una quota. 

   Per iniziare, creare innanzitutto una quota per il servizio di calcolo. Nell'elenco dello spazio dei nomi, selezionare il **Microsoft. COMPUTE** dello spazio dei nomi e quindi fare clic su **Crea nuova quota**.
   
   ![Crea nuova quota](media/asdk-offer-services/create-quota.png)

2. Nel **Crea quota** sezione, digitare un nome per la quota, impostare i parametri desiderati per la quota e fare clic su **OK**.

   ![Nome della quota](media/asdk-offer-services/quota-properties.png)

3. Selezionare la quota creato per il **Microsoft. COMPUTE** servizio.

   ![Selezionare quota](media/asdk-offer-services/set-quota.png)

4. Ripetere i passaggi da 1 a 3 per impostare le quote per i servizi di rete e archiviazione e quindi fare clic su OK nella sezione delle quote. Successivamente, fare clic su **OK** sul **nuovo piano** sezione per completare l'installazione di piano. 

   ![Tutte le quote impostate](media/asdk-offer-services/all-quotas-set.png)

5. Completare la creazione dell'offerta, fare clic su **crea** nella sezione di piano. Verrà visualizzata una notifica quando l'offerta è stato creato correttamente e verrà elencata come un'offerta disponibile.

   ![Offerta creato](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Offerta insieme al ruolo public
Offerte devono essere resa pubbliche agli utenti di visualizzarli quando si seleziona un'offerta per la sottoscrizione. 

Offerte possono essere:
- **Pubblica**: visibile a tutti gli utenti.
- **Privato**: visibile solo agli amministratori di cloud. Utile durante l'elaborazione al piano o l'offerta, o se l'amministratore del cloud decide di creare ogni sottoscrizione per gli utenti.
- **Rimossi**: chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile utilizzare autorizzazioni rimosse per evitare future sottoscrizioni, ma lasciare invariati i sottoscrittori correnti.

> [!TIP]
> Le modifiche per l'offerta non sono immediatamente visibili agli utenti. Per visualizzare le modifiche, gli utenti potrebbe essere necessario disconnettersi e ripetere l'accesso per il [portale per gli utenti](https://portal.local.azurestack.external) per visualizzare la nuova offerta.

Per impostare la nuova offerta su public: 

1. Scegliere il menu del dashboard, **offre** e quindi scegliere l'offerta è stato creato.

2. Fare clic su **modifica dello stato**, quindi fare clic su **pubblica**.

   ![Stato pubblico](media/asdk-offer-services/set-public.png)

3. L'offerta saranno ora disponibile nel portale per gli utenti dello Stack di Azure.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un'offerta
> * Creare un piano
> * Impostare quote
> * Offerta insieme al ruolo public

Passare alla prossima esercitazione per imparare a sottoscrivere l'offerta che appena creata come un utente di Azure Stack.

> [!div class="nextstepaction"]
> [Sottoscrivere un'offerta](asdk-subscribe-services.md)