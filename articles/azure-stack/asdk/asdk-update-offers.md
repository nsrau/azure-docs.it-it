---
title: "In questa esercitazione, è illustrato come aggiornare Azure Stack offerte e piani | Documenti Microsoft"
description: In questo articolo viene descritto come visualizzare e modificare offerte Azure Stack e i piani esistenti.
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
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Esercitazione: aggiornare le offerte e i piani
Come operatore dello Stack di Azure, creare piani che contengono i servizi desiderati e quote applicabili per gli utenti per la sottoscrizione. Questi *piani di base* contengono i servizi dei componenti di base per essere offerti a utenti e può essere presente solo un piano di basa per ogni offerta. Se è necessario modificare l'offerta, è possibile utilizzare *piani di componente aggiuntivo* che consentono di modificare il piano per estendere il computer, spazio di archiviazione, o di rete quote inizialmente offerte con il piano basa. 

Anche se la combinazione di tutti gli elementi in un singolo piano può essere ottimale in alcuni casi, si desidera disporre di una base piano e di offrire servizi aggiuntivi in uso piani di componente aggiuntivo. Ad esempio, è possibile decidere di offrire servizi IaaS come parte di un piano di basa, con tutti i servizi PaaS considerati i piani di componente aggiuntivo. Combinazioni sono utilizzabili per controllare il consumo di risorse limitata ASDK nell'ambiente in uso. Ad esempio, se si desidera che gli utenti fare attenzione al loro utilizzo delle risorse, è possibile che un piano di basa relativamente piccolo (a seconda dei servizi necessarie) e come accedere, gli utenti della capacità, potrebbe essere avvisati che essi hanno già utilizzato l'allocazione delle risorse in base al piano assegnato. Da qui, gli utenti possono selezionare un piano di componenti aggiuntivi disponibili per le risorse aggiuntive. 

> [!NOTE]
> Quando un utente aggiunge un piano del componente aggiuntivo a una sottoscrizione di offerta esistente, le risorse aggiuntive potrebbero richiedere un'ora da visualizzare. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un piano del componente aggiuntivo 
> * Sottoscrivere il piano del componente aggiuntivo

## <a name="create-an-add-on-plan"></a>Creare un piano del componente aggiuntivo
**I piani di componente aggiuntivo** vengono creati tramite la modifica di un'offerta esistente.

1. Accedi ai [portale Azure Stack](https://adminportal.local.azurestack.external) come un amministratore del cloud.
2. Seguire gli stessi passaggi utilizzati in precedenza per [creare un piano di basa](asdk-offer-services.md) per creare un nuovo piano che offre servizi che non sono stati forniti in precedenza. In questo esempio, servizi chiave dell'insieme di credenziali (Microsoft.KeyVault) verranno inclusi nel piano.
3. Nel portale di amministrazione, fare clic su **offre** e quindi selezionare l'offerta venga aggiornato con un piano del componente aggiuntivo.

   ![](media/asdk-update-offers/1.PNG)

4.  Scorrere fino alla fine delle proprietà offerta e selezionare **piani di componente aggiuntivo**. Fare clic su **Aggiungi**.
   
    ![](media/asdk-update-offers/2.PNG)

5. Selezionare il piano da aggiungere. In questo esempio viene chiamato il piano **piano insieme di credenziali chiave**, quindi fare clic su **selezionare** per aggiungere il piano per l'offerta. Si riceverà una notifica che il piano è stato aggiunto correttamente per l'offerta.
   
    ![](media/asdk-update-offers/3.PNG)

6. Esaminare l'elenco dei componenti aggiuntivi elencati piani inclusi con l'opzione per verificare che il componente aggiuntivo nuovo piano.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>Sottoscrivere il piano del componente aggiuntivo
È necessario eseguire l'accesso al portale per gli utenti dello Stack di Azure per estendere una sottoscrizione di Azure Stack esistente con un piano del componente aggiuntivo.

Usare questi passaggi per individuare altre risorse che sono stati offerti dall'operatore dello Stack di Azure e aggiungere un piano del componente aggiuntivo per un'offerta che precedentemente sottoscritto.

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external).
2. Per trovare la sottoscrizione da estendere con piano di componente aggiuntivo, fare clic su **altri servizi**, fare clic su **sottoscrizioni**, quindi selezionare la sottoscrizione.
   
    ![](media/asdk-update-offers/5.PNG)

3.  Nella panoramica di sottoscrizione, fare clic su **Aggiungi piano**.
   
    ![](media/asdk-update-offers/6.PNG)

4. Nel pannello Aggiungi piano, selezionare il piano di componente aggiuntivo da aggiungere alla sottoscrizione. In questo esempio, **piano insieme di credenziali chiave** sia selezionata. Quindi, si riceverà una notifica che il piano del componente aggiuntivo è stato acquisito corretta e che è necessario aggiornare il portale per accedere alla sottoscrizione aggiornata.
   
    ![](media/asdk-update-offers/7.PNG)

5. Infine, esaminare i piani di componenti aggiuntivi associati con la propria sottoscrizione per assicurarsi che il piano del componente aggiuntivo è stato aggiunto.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un piano del componente aggiuntivo 
> * Sottoscrivere il piano del componente aggiuntivo

> [!div class="nextstepaction"]
> [Creare una macchina virtuale da un modello](asdk-create-vm-template.md)

