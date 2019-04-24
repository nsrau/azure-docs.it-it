---
title: 'Azure Active Directory Domain Services: impostazioni delle notifiche | Microsoft Docs'
description: Impostazioni di notifica per Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 85fcd28b5964cbe0246a7c79fa6d24d756f7bd35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416782"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Impostazioni di notifica in Azure AD Domain Services

Le notifiche per Azure AD Domain Services consentono di essere aggiornati non appena viene rilevato un avviso di integrità nel dominio gestito.  

Questa funzionalità è disponibile solo per i domini gestiti non presenti nelle reti virtuali classiche.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Come controllare le impostazioni di notifica di posta elettronica per Azure AD Domain Services

1. Accedere alla pagina [Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
2. Scegliere il dominio gestito nella tabella.
3. Nel menu di spostamento a sinistra scegliere **Impostazioni notifiche**.

Nella pagina sono elencati tutti i destinatari delle notifiche di posta elettronica per Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>Che aspetto ha una notifica di posta elettronica?

L'immagine seguente illustra un esempio di notifica:

![Esempio di notifica di posta elettronica](./media/active-directory-domain-services-alerts/email-alert.png)

Il messaggio di posta elettronica specifica il dominio gestito per cui è stato attivato l'avviso, data e ora del rilevamento e un collegamento alla pagina di integrità di Azure AD Domain Services nel portale di Azure.

> [!WARNING]
> Verificare sempre che il messaggio sia stato inviato da un mittente Microsoft verificato prima di fare clic sui collegamenti in esso contenuti. L'indirizzo di provenienza è sempre azure-noreply@microsoft.com.


## <a name="why-would-i-receive-email-notifications"></a>Perché si ricevono notifiche di posta elettronica?

Azure AD Domain Services invia notifiche di posta elettronica per segnalare aggiornamenti importanti sul dominio.  Queste notifiche vengono usate solo per aspetti urgenti che influiscono sul servizio e che devono essere risolti immediatamente. Ogni notifica di posta elettronica viene attivata da un avviso per il dominio gestito specifico. Questi avvisi verranno visualizzati anche nel portale di Azure e possono essere esaminati nella [pagina di integrità di Azure AD Domain Services](active-directory-ds-check-health.md).

Azure Active Directory Domain Services non invia i messaggi a questo elenco per finalità di vendita, pubblicità o aggiornamenti.

## <a name="when-will-i-receive-email-notifications"></a>Quando si ricevono notifiche di posta elettronica?

Quando si rileva un [nuovo avviso](active-directory-ds-troubleshoot-alerts.md) per il dominio gestito viene inviata immediatamente una notifica. Se l'avviso non viene risolto, ogni quattro giorni viene inviata una notifica di posta elettronica come promemoria.

## <a name="who-should-receive-the-email-notifications"></a>A chi vengono inviate le notifiche di posta elettronica?


 È consigliabile che l'elenco di destinatari di posta elettronica per Azure AD Domain Services includa gli utenti che possono amministrare e apportare modifiche al dominio gestito. Questo elenco deve essere considerato come la prima risorsa per rispondere agli eventuali problemi riscontrati. Se gli indirizzi da aggiungere sono più di cinque, è consigliabile creare una lista di distribuzione da aggiungere all'elenco di notifiche.

È possibile aggiungere fino a cinque indirizzi aggiuntivi per le notifiche relative ad Azure AD Domain Services. È possibile anche fare in modo che tutti gli amministratori globali di directory e ogni membro del gruppo di amministratori AAD DC ricevano le notifiche di posta elettronica per Azure AD Domain Services. Azure AD Domain Services invierà notifiche solo a un massimo di 100 indirizzi, incluso l'elenco di amministratori globali e di amministratori AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Come aggiungere un destinatario aggiuntivo

> [!WARNING]
> Quando si modificano le impostazioni di notifica, la modifica viene applicata all'intero dominio gestito e non solo alle impostazioni personali.

1. Accedere alla pagina [Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
2. Fare clic sul dominio gestito.
3. Nel menu di spostamento a sinistra fare clic su **Impostazioni notifiche**.
4. Per aggiungere un indirizzo di posta elettronica, digitare l'indirizzo nella tabella dei destinatari aggiuntivi.
5. Fare clic su "Salva" nel riquadro di spostamento in alto.

## <a name="frequently-asked-questions"></a>Domande frequenti

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>È stata ricevuta una notifica di posta elettronica per un avviso, ma nel portale di Azure non è presente alcun avviso. Che cosa è successo?

Un avviso già risolto non è più visualizzato nel portale di Azure. Probabilmente un altro utente che riceve le notifiche di posta elettronica ha risolto l'avviso nel dominio gestito o la notifica è stata risolta automaticamente da Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Perché non è possibile modificare le impostazioni di notifica?

Se non si è in grado di accedere alla pagina delle impostazioni di notifica nel portale di Azure, non si dispone delle autorizzazioni per modificare Azure AD Domain Services. È necessario contattare l'amministratore globale per ottenere le autorizzazioni per modificare le risorse di Azure AD Domain Services o per essere rimossi dall'elenco di destinatari.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Non si ricevono notifiche di posta elettronica anche se è stato fornito l'indirizzo di posta elettronica. Perché?

Controllare se la notifica è presente nella cartella della posta indesiderata e assicurarsi che il mittente sia stato aggiunto all'elenco di elementi consentiti (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
- [Risolvere i problemi correlati agli avvisi nel dominio gestito](active-directory-ds-troubleshoot-alerts.md)
- [Altre informazioni su Azure AD Domain Services](active-directory-ds-overview.md)
- [Contattare il team del prodotto](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
