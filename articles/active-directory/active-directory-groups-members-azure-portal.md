---
title: Gestire i membri per un gruppo nell&quot;anteprima di Azure Active Directory | Documentazione Microsoft
description: Come gestire gli utenti e i dispositivi che sono membri di un gruppo in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c0c411f6e2f51fa2d55d46ff92153dc8882bb38


---
# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gestire i membri per un gruppo in anteprima di Azure Active Directory
Questo articolo illustra come gestire i membri per un gruppo in anteprima di Azure Active Directory (Azure AD). [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Come è possibile trovare i membri e gestirli?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.
   
   ![Apertura di Gestione utenti](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.
   
   ![Apertura del pannello Gruppi](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare un gruppo.
5. Nel pannello **Gruppo - *nome gruppo*** selezionare **Membri**.
   
   ![Apertura del pannello Membri](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Per aggiungere membri al gruppo, nel pannello **Gruppo - Membri** selezionare **Aggiungi membri**.
   
   ![Comando Aggiungi membri](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. Nel pannello **Membri** selezionare uno o più utenti o dispositivi da aggiungere al gruppo e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello per aggiungerli al gruppo. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.
8. Per rimuovere membri dal gruppo, nel pannello **Gruppo - Membri** selezionare un membro.
9. Nel pannello ***nome membro*** selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.
   
   ![Comando Rimuovi membri](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Al termine della modifica dei membri per il gruppo, selezionare **Salva**.

## <a name="additional-information"></a>Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)




<!--HONumber=Nov16_HO3-->


