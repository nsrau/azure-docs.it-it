---
title: Aggiungere le informazioni sulla privacy dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere le informazioni sulla privacy dell'organizzazione nella sezione Proprietà di Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876185"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Aggiungere le informazioni sulla privacy dell'organizzazione tramite Azure Active Directory
Questo articolo illustra come un amministratore tenant può aggiungere informazioni relative alla privacy al tenant di Azure Active Directory (Azure AD) di un'organizzazione, tramite il portale di Azure.This article explains how a tenant admin can add privacy-related info to an organization's Azure Active Directory (Azure AD) tenant, through the Azure portal.

Ti consigliamo vivamente di aggiungere sia il tuo contatto per la privacy globale che l'informativa sulla privacy della tua organizzazione, in modo che i dipendenti interni e gli ospiti esterni possano rivedere le tue norme. Poiché le informative sulla privacy sono esclusive e personalizzate per ogni attività, è consigliabile contattare un legale per assistenza.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Aggiungere le informazioni sulla privacy in Azure AD
Aggiungere le informazioni sulla privacy dell'organizzazione nella sezione **Proprietà** di Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Per accedere all'area Proprietà e aggiungere le informazioni sulla privacy

1.    Accedere al portale di Azure come amministratore tenant.

2.    Nella barra di spostamento a sinistra, selezionare **Azure Active Directory** e quindi selezionare **Proprietà**.

    Verrà visualizzata la sezione **Proprietà**.

    ![Sezione Proprietà di Azure Active Directory che evidenzia le informazioni sulla privacy](media/active-directory-properties-area/properties-area.png)

3.    Aggiungere le informazioni sulla privacy per i dipendenti:

    - **Contatto tecnico**. Inserire l'indirizzo di posta elettronica della persona da contattare per supporto tecnico all'interno dell'organizzazione.
    
    - **Contatto privacy globale**. Inserire l'indirizzo di posta elettronica della persona da contattare per le richieste di informazioni sulla privacy dei dati personali. Questa persona viene contattata anche da Microsoft qualora venga riscontrata una violazione dei dati. Se l'elenco non contiene nessun contatto, Microsoft contatterà gli amministratori globali.

    - **URL dell'informativa sulla privacy**. Digitare il collegamento al documento dell'organizzazione che descrive il modo in cui l'organizzazione gestisce la privacy dei dati degli ospiti interni ed esterni.

        >[!Important]
        >Se non includi la tua informativa sulla privacy o il tuo contatto per la privacy, gli ospiti esterni visualizzeranno il testo nella casella **Verifica autorizzazioni** che ** <indica che _il nome dell'organizzazione_> non ha fornito collegamenti alle loro condizioni per la revisione**. Ad esempio, un utente guest visualizza questo messaggio quando riceve un invito per accedere a un'organizzazione tramite la collaborazione B2B.

        ![Finestra di dialogo per la verifica delle autorizzazioni di Collaborazione B2B con messaggio](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](../b2b/redemption-experience.md)
- [Aggiungere o modificare informazioni sul profilo per un utente in Azure Active Directory](active-directory-users-profile-azure-portal.md)
