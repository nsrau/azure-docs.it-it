---
title: Aggiungere le informazioni sulla privacy dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere le informazioni sulla privacy dell'organizzazione nella sezione Proprietà di Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b4e3585f25a0b081e30c5524678a0f7bb27942
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187271"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Aggiungere le informazioni sulla privacy dell'organizzazione tramite Azure Active Directory
Questo articolo illustra come un amministratore tenant può aggiungere le informazioni sulla privacy ai tenant di Azure Active Directory (Azure AD) dell'organizzazione, tramite il portale di Azure.

È consigliabile aggiungere sia il contatto privacy globale sia l'informativa sulla privacy della propria organizzazione, in modo che i dipendenti interni e gli utenti guest possano leggere l'informativa. Poiché le informative sulla privacy sono esclusive e personalizzate per ogni attività, è consigliabile contattare un legale per assistenza.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Aggiungere le informazioni sulla privacy in Azure AD
Aggiungere le informazioni sulla privacy dell'organizzazione nella sezione **Proprietà** di Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Per accedere all'area Proprietà e aggiungere le informazioni sulla privacy

1.  Accedere al portale di Azure come amministratore tenant.

2.  Nella barra di spostamento a sinistra, selezionare **Azure Active Directory** e quindi selezionare **Proprietà**.

    Verrà visualizzata la sezione **Proprietà**.

    ![Sezione Proprietà di Azure Active Directory che evidenzia le informazioni sulla privacy](media/active-directory-properties-area/properties-area.png)

3.  Aggiungere le informazioni sulla privacy per i dipendenti:

    - **Contatto tecnico**. Inserire l'indirizzo di posta elettronica della persona da contattare per supporto tecnico all'interno dell'organizzazione.
    
    - **Contatto privacy globale**. Inserire l'indirizzo di posta elettronica della persona da contattare per le richieste di informazioni sulla privacy dei dati personali. Questa persona viene contattata anche da Microsoft qualora venga riscontrata una violazione dei dati. Se l'elenco non contiene nessun contatto, Microsoft contatterà gli amministratori globali.

    - **URL dell'informativa sulla privacy**. Inserire il collegamento al documento dell'organizzazione che descrive come viene gestita la privacy dei dati degli interni e dei guest esterni.

        >[!Important]
        >Se l'informativa sulla privacy o il contatto del responsabile della privacy non vengono inclusi, il guest esterno visualizzerà un messaggio di testo nella casella **Verifica autorizzazioni**, ad esempio  **< _nome dell'azienda_> non ha fornito collegamenti alle rispettive condizioni per la verifica da parte dell'utente.** Ad esempio, un utente guest visualizza questo messaggio quando riceve un invito per accedere a un'organizzazione tramite la collaborazione B2B.

        ![Finestra di dialogo per la verifica delle autorizzazioni di Collaborazione B2B con messaggio](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](https://aka.ms/b2bredemption)
- [Aggiungere o modificare informazioni sul profilo per un utente in Azure Active Directory](active-directory-users-profile-azure-portal.md)
