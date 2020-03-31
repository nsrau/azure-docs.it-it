---
title: Auto-revisione di un pacchetto di accesso nella gestione dei diritti di Azure AD
description: Informazioni su come esaminare l'accesso degli utenti ai pacchetti di accesso alla gestione dei diritti nelle verifiche di accesso ad Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967763"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Auto-revisione di un pacchetto di accesso nella gestione dei diritti di Azure AD

La gestione dei diritti di Azure AD semplifica il modo in cui le aziende gestiscono l'accesso a gruppi, applicazioni e siti di SharePoint.Azure AD entitlement management simplifies how enterprises manage access to groups, applications, and SharePoint sites. In questo articolo viene descritto come un utente esegue una revisione automatica dei pacchetti di accesso assegnati.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Per eseguire una verifica di accesso, è innanzitutto necessario aprire la verifica di accesso. Utilizzare la procedura seguente per trovare e aprire la verifica di accesso:

1. È possibile ricevere un messaggio di posta elettronica da Microsoft che chiede di controllare l'accesso. Individuare l'e-mail per aprire la verifica di accesso. Ecco un esempio di e-mail che richiede una revisione dell'accesso: 
    
    ![Verifica di lettura della posta elettronica con un utente e-revisore](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Fare clic sul collegamento **Controlla accesso.**

1. Puoi anche andare https://myaccess.microsoft.com direttamente a trovare le recensioni di accesso in sospeso se non ricevi un'email.  (Per il governo `https://myaccess.microsoft.us` degli Stati Uniti, utilizzare invece.)

1. Fare clic su **Revisioni** di accesso sulla barra di spostamento sinistra per visualizzare un elenco di verifiche di accesso in sospeso assegnate all'utente.


1.  Fai clic sulla recensione che desideri iniziare.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Una volta aperta la verifica di accesso, è possibile visualizzare l'accesso. Utilizzare la procedura seguente per eseguire la verifica dell'accesso:Use the following procedure to do the access review:

1.  Decidere se è ancora necessario accedere al pacchetto di accesso. Ad esempio, il progetto su cui si sta lavorando non è completo, pertanto è comunque necessario accedere per continuare a lavorare sul progetto.

1.  Fare clic su **Sì** per mantenere l'accesso o su **No** per rimuoverlo.
    >[!NOTE]
    >Se hai dichiarato che non è più necessario accedere, non sei rimosso immediatamente dal pacchetto di accesso. Al termine della revisione, la revisione verrà rimossa al termine della revisione o se un amministratore interrompe la revisione.

1.  Se si è fatto clic su **Sì**, potrebbe essere necessario includere un'istruzione di giustificazione nella casella **Motivo.**

1.  Fare clic su **Submit**.

Puoi tornare alla revisione se cambi idea e decidi di modificare la risposta prima della fine della recensione.

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare l'accesso ai pacchetti di accessoReview access to access packages](entitlement-management-access-reviews-review-access.md) 
