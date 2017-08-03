---
title: 'Azure Active Directory Domain Services: creare il gruppo di amministratori Azure AD DC | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure classico
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: b8e5e2213f157c0332f98305266c1c6248c2ea87
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure classico
Questo articolo descrive e illustra le attività di configurazione necessarie per abilitare Azure Active Directory Domain Services (Azure AD DS) per il tenant di Azure Active Directory (Azure AD).

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>Attività 1: creare il gruppo di amministratori Azure AD DC
La prima attività consiste nel creare un gruppo amministrativo nel tenant di Azure AD. Questo gruppo amministrativo speciale è chiamato *AAD DC Administrators*. Ai membri di questo gruppo vengono concesse autorizzazioni amministrative per i computer aggiunti al dominio gestito di Azure Active Directory Domain Services. Nei computer appartenenti a un dominio viene aggiunto al gruppo degli amministratori. Inoltre, i membri di questo gruppo possono usare Desktop remoto per connettersi ai computer del dominio da remoto.  

> [!NOTE]
> Non sarà possibile esercitare i privilegi di amministratore di dominio o amministratore dell'organizzazione all'interno del dominio gestito creato con Azure Active Directory Domain Services. Nei domini gestiti questi privilegi sono riservati dal servizio e non vengono resi disponibili agli utenti all'interno del tenant. Per poter eseguire alcune operazioni con privilegi, sarà tuttavia possibile usare il gruppo amministrativo speciale creato in questa attività di configurazione. Queste operazioni prevedono l'aggiunta di computer al dominio, l'appartenenza al gruppo di amministrazione su computer aggiunti al dominio e la configurazione di Criteri di gruppo.
>

In questa attività di configurazione verrà creato il gruppo amministrativo al quale verranno aggiunti uno o più utenti nella directory. Per creare il gruppo amministrativo per Azure Active Directory Domain Services, seguire questa procedura:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com).
2. Selezionare il pulsante **Active Directory** nel riquadro sinistro.
3. Selezionare il tenant (directory) di Azure AD per il quale si desidera abilitare Azure Active Directory Domain Services. È possibile creare solo un dominio per ogni directory di Azure AD.

    ![Selezionare la directory di Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Nella pagina di **anteprima della directory** fare clic sulla scheda **Gruppi**.
5. Per aggiungere un gruppo al tenant di Azure AD fare clic su **Aggiungi gruppo** nel riquadro attività nella parte inferiore della finestra.

    ![Pulsante Aggiungi gruppo](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. Nella finestra di dialogo **Aggiungi gruppo** creare un gruppo denominato **AAD DC Administrators**, quindi impostare **Tipo di gruppo** su **Sicurezza**.

   > [!WARNING]
   > Per consentire l'accesso al dominio gestito di Azure Active Directory Domain Services, è necessario creare un gruppo con questo nome esatto.
   >
   >

    ![Finestra di dialogo Aggiungi gruppo](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. Nella casella **Descrizione** immettere una descrizione che consenta ad altri utenti di comprendere che questo gruppo concede autorizzazioni amministrative all'interno di Azure Active Directory Domain Services.
8. Dopo averlo creato, fare clic sul nome del gruppo per visualizzarne le proprietà.
9. Per aggiungere utenti come membri di questo gruppo fare clic sul pulsante **Aggiungi membri** nella parte inferiore della finestra.

    ![Pulsate Aggiungi membri gruppo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. Nella finestra di dialogo **Aggiungi membri** selezionare gli utenti da includere in questo gruppo e fare clic sul segno di spunta in basso a destra.

    ![Aggiungere utenti al gruppo di amministratori](./media/active-directory-domain-services-getting-started/add-group-members.png)


## <a name="next-step"></a>Passaggio successivo
[Attività 2: creare o selezionare una rete virtuale di Azure](active-directory-ds-getting-started-vnet.md)

