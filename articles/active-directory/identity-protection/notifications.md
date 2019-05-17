---
title: Notifiche di Azure Active Directory Identity Protection| Documentazione Microsoft
description: Informazioni su come le notifiche agevolano le attività di analisi.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784349"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica per la gestione del rischio utente e degli eventi di rischio:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.


## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](../reports-monitoring/concept-user-at-risk.md)**. È consigliabile controllare immediatamente gli utenti a rischio.

La configurazione per questo avviso consente di specificare il livello di rischio utente per l'avviso da generare. Verrà generato il messaggio di posta elettronica quando raggiunge il livello di rischio dell'utente specificato; Tuttavia, non riceverai i nuovi utenti in avvisi di posta elettronica rilevato rischio per questo utente dopo lo spostamento in questo livello di rischio utente. Ad esempio, se si imposta il criterio di avviso nel rischio utente medio e l'utente che sposta per rischio medio, si riceveranno gli utenti alla posta elettronica di rischio rilevato per John. Tuttavia, non riceverai un secondo per gli utenti a avviso rilevato rischio se John quindi passa a rischio elevato o dispone di eventi di rischio aggiuntivo.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/notifications/01.png)


### <a name="configuration"></a>Configurazione

Gli amministratori possono impostare:

- **Il livello di rischio utente che attiva la generazione di questo messaggio di posta elettronica** -per impostazione predefinita, il livello di rischio è impostato su "Alta" rischio.
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
