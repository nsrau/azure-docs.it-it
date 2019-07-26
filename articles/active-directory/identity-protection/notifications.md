---
title: Notifiche di Azure Active Directory Identity Protection| Documentazione Microsoft
description: Informazioni su come le notifiche agevolano le attività di analisi.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335359"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica per la gestione del rischio utente e degli eventi di rischio:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.

## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](../reports-monitoring/concept-user-at-risk.md)** . È consigliabile controllare immediatamente gli utenti a rischio.

La configurazione di questo avviso consente di specificare a quale livello di rischio utente si desidera generare l'avviso. Il messaggio di posta elettronica verrà generato quando il livello di rischio dell'utente raggiunge gli elementi specificati. Tuttavia, non si riceveranno nuovi utenti a rischio di avvisi di posta elettronica rilevati per questo utente dopo il passaggio a questo livello di rischio utente. Se, ad esempio, si impostano i criteri per l'avviso sul rischio di un utente medio e l'utente John passa a un livello di rischio medio, si riceverà il messaggio di posta elettronica di rischio rilevato per John. Tuttavia, non si riceverà un avviso per il rischio di un secondo utente se Giorgio passa ad alto rischio o presenta eventi di rischio aggiuntivi.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/notifications/01.png)

### <a name="configuration"></a>Configurazione

Gli amministratori possono impostare:

- **Il livello di rischio utente che attiva la generazione di questo messaggio di posta elettronica** : per impostazione predefinita, il livello di rischio è impostato sul rischio "elevato".
- **I destinatari di questo messaggio di posta elettronica**: per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.  

Per aprire la finestra di dialogo correlata, fare clic su **Avvisi** nella sezione **Impostazioni** della pagina **Identity Protection**.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di riepilogo settimanale contiene un riepilogo dei nuovi eventi di rischio.  
Sono inclusi:

- Utenti a rischio
- Attività sospette
- Vulnerabilità rilevate.
- Collegamenti ai report correlati in Identity Protection.

    ![Correzione](./media/notifications/400.png "Correzione")

### <a name="configuration"></a>Configurazione

Gli amministratori possono disattivare l'invio del messaggio di posta elettronica digest settimanale.

![Rischi utente](./media/notifications/62.png "Rischi utente")

Per aprire la finestra di dialogo correlata, fare clic su **Digest settimanale** nella sezione **Impostazioni** della pagina **Identity Protection**.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/notifications/04.png)

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
