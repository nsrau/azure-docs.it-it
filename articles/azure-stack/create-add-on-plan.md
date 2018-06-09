---
title: In questo articolo, è illustrato come aggiornare offerte Azure Stack e i piani | Documenti Microsoft
description: In questo articolo viene descritto come visualizzare e modificare offerte Azure Stack e i piani esistenti.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238415"
---
# <a name="azure-stack-add-on-plans"></a>Piani di componente aggiuntivo Azure Stack
Come operatore dello Stack di Azure, creare piani che contengono i servizi desiderati e quote applicabili per gli utenti per la sottoscrizione. Questi [ *i piani di base* ](azure-stack-create-plan.md) contengono i servizi dei componenti di base per essere offerti a utenti e può essere presente solo un piano di basa per ogni offerta. Se è necessario modificare l'offerta, è possibile utilizzare *piani di componente aggiuntivo* che consentono di modificare il piano per estendere il computer, spazio di archiviazione, o di rete quote inizialmente offerte con il piano basa. 

Anche se la combinazione di tutti gli elementi in un singolo piano può essere ottimale in alcuni casi, si desidera disporre di una base piano e di offrire servizi aggiuntivi in uso piani di componente aggiuntivo. Ad esempio, è possibile decidere di offrire servizi IaaS come parte di un piano di basa, con tutti i servizi PaaS considerati i piani di componente aggiuntivo. Combinazioni sono utilizzabili per controllare il consumo di risorse nell'ambiente dello Stack di Azure. Ad esempio, se si desidera che gli utenti fare attenzione al loro utilizzo delle risorse, è possibile che un piano di basa relativamente piccolo (a seconda dei servizi necessarie) e come accedere, gli utenti della capacità, potrebbe essere avvisati che essi hanno già utilizzato l'allocazione delle risorse in base al piano assegnato. Da qui, gli utenti possono selezionare un piano di componenti aggiuntivi disponibili per le risorse aggiuntive. 

> [!NOTE]
> Quando un utente aggiunge un piano del componente aggiuntivo a una sottoscrizione di offerta esistente, le risorse aggiuntive potrebbero richiedere un'ora da visualizzare. 

## <a name="create-an-add-on-plan"></a>Creare un piano del componente aggiuntivo
I piani di componente aggiuntivo vengono creati mediante la modifica di un'offerta esistente:

1. Accedere al portale di amministrazione di Azure Stack come un amministratore del cloud.
2. La stessa procedura utilizzata per [creare un nuovo piano basa](azure-stack-create-plan.md) per creare un nuovo piano che offre servizi che non sono stati forniti in precedenza. In questo esempio, servizi chiave dell'insieme di credenziali (Microsoft.KeyVault) verranno inclusi nel nuovo piano.
3. Nel portale di amministrazione, fare clic su **offre** e quindi selezionare l'offerta venga aggiornato con un piano del componente aggiuntivo.

   ![](media/create-add-on-plan/1.PNG)

4.  Scorrere fino alla fine delle proprietà offerta e selezionare **piani di componente aggiuntivo**. Fare clic su **Aggiungi**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Selezionare il piano da aggiungere. In questo esempio viene chiamato il piano **piano insieme di credenziali chiave**, quindi fare clic su **selezionare** per aggiungere il piano per l'offerta. Si riceverà una notifica che il piano è stato aggiunto correttamente per l'offerta.
   
    ![](media/create-add-on-plan/3.PNG)

6. Esaminare l'elenco dei componenti aggiuntivi elencati piani inclusi con l'opzione per verificare che il componente aggiuntivo nuovo piano.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Passaggi successivi
[Creare un'offerta](azure-stack-create-offer.md)