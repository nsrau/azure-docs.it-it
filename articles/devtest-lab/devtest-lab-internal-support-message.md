---
title: Aggiungere un'istruzione di supporto interno a un lab di Azure DevTest Labs | Documenti Microsoft
description: Informazioni su come registrare un'istruzione di supporto interno per un ambiente lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: v-craic
ms.openlocfilehash: 33c2f1e9d1ffa83003b7f0dbba46a7a4588dd57a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Aggiungere un'istruzione di supporto interno a un lab di Azure DevTest Labs

Azure DevTest Labs consente di personalizzare l'ambiente lab con un'istruzione di supporto interno che offre agli utenti con le informazioni di supporto del lab. Ad esempio, è possibile fornire informazioni di contatto in modo che un utente sia in grado di raggiungere il supporto interno quando avranno bisogno di aiuto con la risoluzione dei problemi o l'accesso alle risorse nell'ambiente lab. È anche possibile fornire collegamenti a siti Web interni o domande frequenti che il team possa accedere prima di contattare il supporto tecnico.

Un'istruzione di supporto interno è progettata per consentire l'inserimento delle informazioni lab che in genere non vengono modificati troppo spesso. Per avvisare gli utenti info lab che è più temporaneo in natura, ad esempio aggiornamenti recenti a criteri lab – vedere [annuncio del Post in un ambiente lab](devtest-lab-announcements.md).

È possibile disabilitare o modificare un'istruzione di supporto dopo non è più applicabile.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Passaggi per aggiungere un'istruzione di supporto a un lab esistente

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Dall'elenco di esercitazioni, selezionare il lab in cui si desidera aggiungere un'istruzione di supporto.  
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.  

    ![Pulsante Configurazione e criteri](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A sinistra sotto **impostazioni**selezionare **supporto interno**.

    ![Pulsante di supporto interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Per creare un messaggio di supporto interne per gli utenti in questa esercitazione, impostare Enabled su **Sì**.

1. Nel **messaggio supporto** immettere l'istruzione di supporto interno che si desidera presentare agli utenti di laboratorio. Il messaggio di supporto accetta Markdown. Quando si immette il testo del messaggio, è possibile visualizzare il **anteprima** area nella parte inferiore della schermata per vedere come viene visualizzato il messaggio agli utenti.

    ![Schermata di supporto interno per creare il messaggio.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selezionare **salvare** dopo l'istruzione di supporto è pronto per l'inserimento.

Quando non si desidera visualizzare questo messaggio di supporto per gli utenti, restituisce il **supporto interno** pagina e impostare **abilitato** a **No**.

## <a name="steps-for-users-to-view-the-support-message"></a>Passaggi per gli utenti di visualizzare il messaggio di supporto

1. Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) selezionare un lab.

1. Il lab **Panoramica** area selezionare **supporto interno**.  

    ![Pulsante di supporto interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se viene registrato un messaggio di supporto, l'utente può visualizzarlo nel riquadro di supporto interno.

    ![Riquadro di supporto interno con supporto messaggio inviato](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Istruzioni di supporto interno vengono in genere utilizzate per fornire informazioni di supporto che non cambiano frequentemente. Viene inoltre illustrato come [registra un annuncio da un ambiente lab](devtest-lab-announcements.md) per informare gli utenti le modifiche temporanee o di aggiornamenti nel lab.
* [Impostare i criteri e le pianificazioni](devtest-lab-set-lab-policy.md) fornisce informazioni su come è possibile applicare altre restrizioni e le convenzioni tra la sottoscrizione utilizzando i criteri personalizzati.