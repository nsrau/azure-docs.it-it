---
title: Notifiche di Azure Active Directory Identity Protection| Documentazione Microsoft
description: "Informazioni su come le notifiche agevolano le attività di analisi."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica per la gestione del rischio utente e degli eventi di rischio:

- Gli utenti a rischio rilevati tramite posta elettronica
- Messaggio di posta elettronica di riepilogo settimanale

In questo articolo fornisce una panoramica di entrambe le notifiche tramite posta elettronica.


## <a name="users-at-risk-detected-email"></a>Gli utenti a rischio rilevati tramite posta elettronica

In risposta a un account rilevato a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con **utenti a rischio rilevati** come oggetto. Messaggio di posta elettronica include un collegamento per il  **[utenti contrassegno i rischi](active-directory-reporting-security-user-at-risk.md)**  report. Come procedura consigliata, è necessario individuare immediatamente gli utenti a rischio.

![Gli utenti a rischio rilevati tramite posta elettronica](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Configurazione

Come amministratore, è possibile impostare:

- **Il livello di rischio che provoca la generazione di questo messaggio di posta elettronica** -per impostazione predefinita, il livello di rischio è impostato su "Alta" rischio.
- **I destinatari di questo messaggio di posta elettronica** -per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere altri lettori di protezione amministratori globali, amministratori di sicurezza, destinatari.  


Per aprire la finestra di dialogo correlata, fare clic su **avvisi** nel **impostazioni** sezione la **Identity Protection** pagina.

![Gli utenti a rischio rilevati tramite posta elettronica](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di riepilogo settimanale contiene un riepilogo dei nuovi eventi di rischio.  
Sono inclusi:

- Utenti a rischio.

- Attività sospette

- Vulnerabilità rilevate.

- Collegamenti ai report correlati in Identity Protection.

    ![Correzione](./media/active-directory-identityprotection-notifications/400.png "Correzione")

### <a name="configuration"></a>Configurazione

Come amministratore, è possibile passare l'invio di un messaggio di posta elettronica digest settimanale.

![Rischi utente](./media/active-directory-identityprotection-notifications/62.png "Rischi utente")

Per aprire la finestra di dialogo correlata, fare clic su **settimanale** nel **impostazioni** sezione la **Identity Protection** pagina.

![Gli utenti a rischio rilevati tramite posta elettronica](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>Vedere anche 

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
