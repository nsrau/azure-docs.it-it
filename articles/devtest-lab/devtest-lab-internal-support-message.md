---
title: Aggiungere un'informativa sul supporto interno in un lab di Azure DevTest Labs | Microsoft Docs
description: Informazioni su come pubblicare un'informativa sul supporto interno in un lab di Azure DevTest Labs
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Aggiungere un'informativa sul supporto interno in un lab di Azure DevTest Labs

Azure DevTest Labs consente di personalizzare un lab con un'informativa sul supporto interno per fornire agli utenti informazioni sul supporto offerto dal lab. È ad esempio possibile pubblicare informazioni di contatto per consentire agli utenti di raggiungere il supporto interno quando hanno bisogno di aiuto per risolvere problemi o accedere alle risorse nel lab. È anche possibile fornire collegamenti a siti Web interni o domande frequenti a cui il team può accedere prima di contattare il supporto.

Un'informativa sul supporto interno ha come obiettivo la pubblicazione di informazioni sul lab che in genere non cambiano molto spesso. Per comunicare agli utenti informazioni sul lab di carattere più temporaneo, ad esempio aggiornamenti recenti alle politiche del lab, vedere [Pubblicare un annuncio per un lab](devtest-lab-announcements.md).

È possibile disabilitare o modificare facilmente un'informativa sul supporto quando non è più applicabile.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Procedura per aggiungere un'informativa sul supporto a un lab esistente

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Nell'elenco di lab selezionare il lab in cui si vuole aggiungere un'informativa sul supporto.  
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.  

    ![Pulsante Configurazione e criteri](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A sinistra sotto **IMPOSTAZIONI** selezionare **Supporto interno**.

    ![Pulsante Supporto interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Per creare un messaggio del supporto interno per gli utenti nel lab, impostare Abilitato su **Sì**.

1. Nel campo **Messaggio del supporto** immettere l'informativa sul supporto interno che si vuole presentare agli utenti del lab. Nel messaggio del supporto è possibile usare markdown. Mentre si immette il testo, è possibile visualizzare l'area **Anteprima** nella parte inferiore della schermata per verificare come il messaggio apparirà agli utenti.

    ![Schermata del supporto interno per creare il messaggio.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Quando l'informativa sul supporto è pronta per essere pubblicata, selezionare **Salva**.

Quando non si vuole più mostrare questo messaggio del supporto agli utenti del lab, tornare alla pagina **Supporto interno** e impostare **Abilitato** su **No**.

## <a name="steps-for-users-to-view-the-support-message"></a>Procedura per gli utenti per visualizzare il messaggio del supporto

1. Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) selezionare un lab.

1. Nell'area **Panoramica** del lab selezionare **Supporto interno**.  

    ![Pulsante Supporto interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se è stato pubblicato un messaggio del supporto, l'utente può visualizzarlo nel riquadro Supporto interno.

    ![Riquadro Supporto interno con il messaggio del supporto pubblicato](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Le informative sul supporto interno vengono in genere usate per fornire dati sul supporto che non cambiano frequentemente. È anche possibile apprendere come [pubblicare un annuncio in un lab](devtest-lab-announcements.md) per comunicare agli utenti eventuali modifiche o aggiornamenti temporanei.
* L'articolo [Configurare criteri e pianificazioni](devtest-lab-set-lab-policy.md) contiene informazioni su come applicare altre restrizioni e convenzioni nella sottoscrizione usando criteri personalizzati.