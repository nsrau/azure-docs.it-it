---
title: Notifiche di Azure Active Directory Identity Protection
description: Informazioni su come le notifiche agevolano le attività di analisi.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120122"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica che consentono di gestire i rilevamenti dei rischi e dei rischi degli utenti:Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.

## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](../reports-monitoring/concept-user-at-risk.md)**. È consigliabile controllare immediatamente gli utenti a rischio.

La configurazione per questo avviso consente di specificare a quale livello di rischio utente si desidera generare l'avviso. L'e-mail verrà generata quando il livello di rischio dell'utente raggiunge quello che hai specificato; Tuttavia, non si riceveranno nuovi utenti a rischio rilevati avvisi di posta elettronica per questo utente dopo il passaggio a questo livello di rischio utente. Ad esempio, se si imposta il criterio per l'avviso sul rischio medio per gli utenti e l'utente John passa a rischio medio, si riceveranno gli utenti a rischio rilevati e-mail per John. Tuttavia, non si riceverà un secondo utente a rischio rilevato avviso se John passa a rischio elevato o ha rilevamenti di rischio aggiuntivi.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurare gli utenti a rischio di avvisi rilevati

Gli amministratori possono impostare:

- Il livello di **rischio utente che attiva la generazione di questo messaggio di posta elettronica:** per impostazione predefinita, il livello di rischio è impostato su "Alto".
- **I destinatari di questo messaggio di posta elettronica**: per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.
   - Facoltativamente, è possibile aggiungere altri messaggi di posta elettronica per ricevere notifiche di avviso questa funzionalità è un'anteprima e gli utenti definiti devono disporre delle autorizzazioni appropriate per visualizzare i report collegati nel portale di Azure.Optionally you can Add **additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configurare gli utenti a rischio di posta elettronica nel **portale di Azure** in Utenti di Azure Active **Directory** > **Security** > Identity**Protection** > a rischio vengono**rilevati avvisi.**

## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

L'e-mail digest settimanale contiene un riepilogo dei nuovi rilevamenti dei rischi.  
 Sono inclusi:

- Utenti a rischio.
- Attività sospette
- Vulnerabilità rilevate.
- Collegamenti ai report correlati in Identity Protection.

![Messaggio di posta elettronica di riepilogo settimanale](./media/howto-identity-protection-configure-notifications/400.png)

Per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.

### <a name="configure-weekly-digest-email"></a>Configurare la posta elettronica digest settimanale

In qualità di amministratore, puoi attivare o disattivare l'invio di un'e-mail digest settimanale e scegliere gli utenti assegnati per ricevere l'e-mail.

Configurare la posta elettronica del digest settimanale nel **portale di Azure** in Azure Active **Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
