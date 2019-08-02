---
title: Modificare le informazioni del gruppo - Azure Active Directory | Microsoft Docs
description: Istruzioni su come modificare le informazioni del gruppo usando Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561871"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Modificare le informazioni del gruppo usando Azure Active Directory

Con Azure Active Directory (Azure AD) è possibile modificare le impostazioni di un gruppo, ad esempio aggiornarne il nome, la descrizione o il tipo di appartenenza.

## <a name="to-edit-your-group-settings"></a>Per modificare le impostazioni del gruppo
1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory** e quindi **Gruppi**.

    Verrà visualizzata la pagina **Gruppi - Tutti i gruppi** con tutti i gruppi attivi.

3. Dalla pagina **Gruppi - Tutti i gruppi** digitare quanti più caratteri possibile del nome del gruppo nella casella **Cerca**. Ai fini di questo articolo, viene eseguita la ricerca per il gruppo **MDM policy - West**.

    I risultati della ricerca vengono visualizzati sotto la casella **Cerca** e si aggiornano a mano a mano che si aggiungono caratteri.

    ![Pagina Tutti i gruppi con il testo della ricerca nella casella Cerca](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selezionare il gruppo **MDM policy - West** e selezionare **Proprietà** dall'area **Gestione**.

    ![Pagina Panoramica gruppo, con l'opzione membro e le informazioni evidenziate](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Aggiornare le informazioni in **Impostazioni generali** in base alle esigenze, tra cui:

    ![Impostazioni delle proprietà per un gruppo](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nome gruppo.** Modificare il nome del gruppo esistente.
    
    - **Descrizione gruppo.** Modificare la descrizione del gruppo esistente.

    - **Tipo gruppo.** Non è possibile modificare il tipo di gruppo dopo che è stato creato. Per modificare **Tipo gruppo**, è necessario eliminare il gruppo e crearne uno nuovo.
    
    - **Tipo di appartenenza.** Modificare il tipo di appartenenza. Per altre informazioni sui diversi tipi di appartenenza disponibili, vedere [Procedura: Creare un gruppo di base e aggiungere membri tramite il portale di Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **ID oggetto.** Non è possibile modificare l'ID oggetto, ma è possibile copiarlo per usarlo nei comandi di PowerShell per il gruppo. Per altre informazioni sull'uso dei cmdlet di PowerShell, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

- [Visualizzare gruppi e membri](active-directory-groups-view-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Aggiungere o rimuovere membri di un gruppo](active-directory-groups-members-azure-portal.md)

- [Gestire le regole dinamiche per gli utenti in un gruppo](../users-groups-roles/groups-create-rule.md)

- [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)

- [Gestire l'accesso alle risorse con i gruppi](active-directory-manage-groups.md)

- [Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
