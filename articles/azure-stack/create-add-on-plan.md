---
title: In questo articolo descrive come aggiornare i piani e offerte di Azure Stack | Microsoft Docs
description: Questo articolo descrive come visualizzare e modificare le offerte di Azure Stack e i piani esistenti.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: f6b85d2197a4db9cdfc526e5857e4b6ce707beb2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076792"
---
# <a name="azure-stack-add-on-plans"></a>Piani aggiuntivi di Azure Stack

Un operatore di Azure Stack, si creare piani aggiuntivi per modificare un [ *piano base* ](azure-stack-create-plan.md) quando si desidera offrire servizi aggiuntivi o estendere *computer*, *archiviazione* , oppure *rete* quote oltre all'offerta iniziale piani base. Piani aggiuntivi modificare il piano di base e sono le estensioni facoltative che gli utenti possono scegliere di sottoscrivere. 

Vi sono casi quando la combinazione di tutti gli elementi in un singolo piano è ottima. In altri casi è consigliabile avere una base piano e quindi offrire i servizi aggiuntivi utilizzando piani aggiuntivi. Ad esempio, è possibile decidere di offrire servizi IaaS come parte di un piano di base, con tutti i servizi PaaS considerati come i piani aggiuntivi.

È di un altro motivo per usare i piani aggiuntivi per consentire agli utenti di tenere in considerazione dell'utilizzo delle risorse. A tale scopo, è possibile iniziare con un piano di base che include le quote di dimensioni relativamente ridotte (a seconda dei servizi necessarie). Quindi, come gli utenti raggiungono la capacità, si sarebbe un avviso che hanno stato utilizzato l'allocazione delle risorse in base al piano assegnato. Da qui, gli utenti può quindi selezionare un piano aggiuntivo che fornisce le risorse aggiuntive.

> [!NOTE]
> Quando non si desidera usare un piano del componente aggiuntivo per estendere una quota, è possibile anche effettuare [modificare la configurazione originale della quota](azure-stack-quota-types.md#to-edit-a-quota). 

Quando un utente aggiunge un piano del componente aggiuntivo a una sottoscrizione di offerta esistente, le risorse aggiuntive potrebbero richiedere fino a un'ora da visualizzare. 

## <a name="create-an-add-on-plan"></a>Creare un piano aggiuntivo
Piani aggiuntivi vengono creati tramite la modifica di un'offerta esistente:

1. Accedere al portale di amministrazione di Azure Stack come amministratore del cloud.
2. Seguire la stessa procedura usata per [creare un nuovo piano base](azure-stack-create-plan.md) per creare un nuovo piano di offerta di servizi che non sono stati offerti in precedenza. In questo esempio, servizi di Key Vault (Microsoft. keyvault) verranno inclusi nel nuovo piano.
3. Nel portale di amministrazione, fare clic su **offre** e quindi selezionare l'offerta venga aggiornato con un piano del componente aggiuntivo.

   ![](media/create-add-on-plan/1.PNG)

4.  Scorrere verso il basso le proprietà di offerta e selezionare **piani aggiuntivi**. Fare clic su **Aggiungi**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Selezionare il piano da aggiungere. In questo esempio, il piano viene chiamato **piano di insieme di credenziali delle chiavi**. Dopo aver selezionato il piano, fare clic su **seleziona** per aggiungere il piano all'offerta. Si dovrebbe ricevere una notifica che il piano è stato aggiunto all'offerta.
   
    ![](media/create-add-on-plan/3.PNG)

6. Esaminare l'elenco dei componenti aggiuntivi elencati piani inclusi con l'opzione per verificare che il componente aggiuntivo di nuovo piano.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Passaggi successivi
[Creare un'offerta](azure-stack-create-offer.md)