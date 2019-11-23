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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382160"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.

## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](../reports-monitoring/concept-user-at-risk.md)** . È consigliabile controllare immediatamente gli utenti a rischio.

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

Gli amministratori possono impostare:

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **I destinatari di questo messaggio di posta elettronica**: per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

The weekly digest email contains a summary of new risk detections.  
Sono inclusi:

- Utenti a rischio.
- Attività sospette
- Vulnerabilità rilevate.
- Collegamenti ai report correlati in Identity Protection.

![Messaggio di posta elettronica di riepilogo settimanale](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Vedi anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
