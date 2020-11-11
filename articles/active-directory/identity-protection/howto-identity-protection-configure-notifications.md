---
title: Notifiche di Azure Active Directory Identity Protection
description: Informazioni su come le notifiche agevolano le attività di analisi.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489489"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatici che consentono di gestire i rischi utente e i rilevamenti dei rischi:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.

## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](./overview-identity-protection.md)**. È consigliabile controllare immediatamente gli utenti a rischio.

La configurazione di questo avviso consente di specificare a quale livello di rischio utente si desidera generare l'avviso. Il messaggio di posta elettronica verrà generato quando il livello di rischio dell'utente raggiunge gli elementi specificati. Se, ad esempio, si impostano i criteri in modo da ricevere un avviso sul rischio di un utente medio e il Punteggio di rischio dell'utente di John passa a un rischio medio a causa di un rischio di accesso in tempo reale, gli utenti riceveranno un messaggio di posta elettronica a rischio rilevato. Se l'utente dispone di rilevamenti di rischio successivi che fanno sì che il calcolo del livello di rischio dell'utente sia il livello di rischio specificato (o superiore), si riceveranno ulteriori utenti a rischio di messaggi di posta elettronica rilevati quando il Punteggio di rischio utente viene ricalcolato. Se, ad esempio, un utente passa a un livello di rischio medio il 1 ° gennaio, si riceverà una notifica tramite posta elettronica se le impostazioni sono impostate in modo da avvisare il rischio medio. Se lo stesso utente dispone di un altro rilevamento dei rischi del 5 gennaio, che è anche il rischio medio e il Punteggio di rischio utente viene ricalcolato ed è ancora medio, si riceverà un'altra notifica tramite posta elettronica. 

Tuttavia, verrà inviata una notifica di posta elettronica aggiuntiva solo se l'ora in cui si è verificato il rischio (che ha causato la modifica del livello di rischio utente) è più recente rispetto al momento dell'invio dell'ultimo messaggio di posta elettronica. Ad esempio, un utente accede il 1 ° gennaio alle 05:00 e non esiste alcun rischio in tempo reale (ovvero non viene generato alcun messaggio di posta elettronica a causa di tale accesso). Dieci minuti dopo, alle 5:10 AM, lo stesso utente esegue di nuovo l'accesso con un elevato rischio in tempo reale, causando il passaggio al livello di rischio dell'utente e l'invio di un messaggio di posta elettronica. Quindi, alle 5:15 AM, il Punteggio di rischio offline per l'accesso originale al 5 AM viene modificato a rischio elevato a causa dell'elaborazione dei rischi offline. Un utente aggiuntivo contrassegnato per il rischio di posta elettronica non viene inviato, dal momento in cui il primo accesso è stato prima del secondo accesso che ha già attivato una notifica di posta elettronica.

Per evitare un sovraccarico dei messaggi di posta elettronica, si riceverà un solo messaggio di posta elettronica entro un periodo di tempo di 5 secondi. Questo ritardo indica che se più utenti passano al livello di rischio specificato durante lo stesso periodo di tempo di 5 secondi, verrà aggregato e inviato un messaggio di posta elettronica per rappresentare la modifica a livello di rischio per tutti questi.

Se l'organizzazione ha abilitato la correzione automatica, come descritto nell'articolo, l' [esperienza utente con Azure ad Identity Protection](concept-identity-protection-user-experience.md) esiste la possibilità che l'utente possa risolvere il rischio prima di avere l'opportunità di esaminare il problema. È possibile visualizzare gli utenti a rischio e gli accessi a rischio che sono stati corretti aggiungendo "corretti" al filtro di **stato del rischio** nei report **utenti** a rischio o **accessi a rischio** .

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurare gli utenti a rischio di avvisi rilevati

Gli amministratori possono impostare:

- **Il livello di rischio utente che attiva la generazione di questo messaggio di posta elettronica** : per impostazione predefinita, il livello di rischio è impostato sul rischio "elevato".
- **I destinatari di questo messaggio di posta elettronica** : gli utenti nei ruoli amministratore globale, amministratore della sicurezza o ruolo di lettura per la sicurezza vengono aggiunti automaticamente a questo elenco. Si tenta di inviare messaggi di posta elettronica ai primi 20 membri di ogni ruolo. Se un utente è registrato in PIM per elevare a uno di questi ruoli su richiesta, riceverà **solo i messaggi di posta elettronica se vengono elevati al momento dell'invio del messaggio di posta elettronica**.
   - Facoltativamente, è possibile **aggiungere un messaggio di posta elettronica personalizzato qui** gli utenti definiti devono disporre delle autorizzazioni appropriate per visualizzare i report collegati nel portale di Azure.

Configurare gli utenti a rischio di posta elettronica nel **portale di Azure** in **Azure Active Directory**  >  **sicurezza**  >  di **Identity Protection**  >  **utenti a rischio di avvisi rilevati**.

## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di posta elettronica del digest settimanale contiene un riepilogo dei nuovi rilevamenti dei rischi.  
Sono inclusi:

- Nuovi utenti a rischio rilevati
- Sono stati rilevati nuovi accessi a rischio (in tempo reale)
- Collegamenti ai report correlati in Identity Protection.

![Messaggio di posta elettronica di riepilogo settimanale](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Gli utenti con ruoli di amministratore globale, amministratore della sicurezza o di Reader di sicurezza vengono aggiunti automaticamente a questo elenco. Si tenta di inviare messaggi di posta elettronica ai primi 20 membri di ogni ruolo. Se un utente è registrato in PIM per elevare a uno di questi ruoli su richiesta, riceverà **solo i messaggi di posta elettronica se vengono elevati al momento dell'invio del messaggio di posta elettronica**

### <a name="configure-weekly-digest-email"></a>Configurare la posta elettronica del digest settimanale

In qualità di amministratore, è possibile attivare o disattivare l'invio di un messaggio di posta elettronica del digest settimanale e scegliere gli utenti assegnati a ricevere il messaggio di posta elettronica.

Configurare la posta elettronica del digest settimanale nella **portale di Azure** in **Azure Active Directory**  >  digest di **sicurezza** di  >  **Identity Protection**  >  **settimanale**.

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
