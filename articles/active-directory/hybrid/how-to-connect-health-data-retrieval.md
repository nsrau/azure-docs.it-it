---
title: Recupero dati per istruzioni Azure AD Connect Health | Microsoft Docs
description: Questa pagina descrive come recuperare i dati da Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463653"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Connect Health istruzioni per il recupero dei dati

Questo documento descrive come usare Azure AD Connect per recuperare dati da Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Recuperare tutti gli indirizzi di posta elettronica degli utenti configurati per gli avvisi di integrità.

Per recuperare gli indirizzi di posta elettronica per tutti gli utenti configurati in Azure AD Connect Health per ricevere avvisi, seguire questa procedura.

1.  Iniziare dal pannello integrità Azure Active Directory Connect e selezionare **Sincronizza Servizi** dalla barra di spostamento a sinistra.
 ![Servizi di sincronizzazione](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Fare clic sul riquadro **avvisi** .</br>
 ![Avviso](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Fare clic su **impostazioni di notifica**.
 ![Notifica](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  Nel pannello delle **impostazioni di notifica** è disponibile l'elenco degli indirizzi di posta elettronica che sono stati abilitati come destinatari per le notifiche degli avvisi di integrità.
 ![E-mail](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Recuperare gli account contrassegnati con AD FS tentativi di accesso con password errata

Per recuperare gli account contrassegnati con AD FS tentativi di accesso con password errata, attenersi alla procedura riportata di seguito.

1.  A partire dal pannello integrità Azure Active Directory, selezionare **Sincronizza errori**.
 ![Errori di sincronizzazione](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  Nel pannello **errori di sincronizzazione** fare clic su **Esporta**. Verrà esportato un elenco degli errori di sincronizzazione registrati.
 ![Export](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Passaggi successivi
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installazione dell'agente di Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)