---
title: Notifiche tramite posta elettronica per Azure AD Domain Services | Microsoft Docs '
description: Informazioni su come configurare le notifiche di posta elettronica per segnalare i problemi in un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 140edb3162d4f76c741a5692faa19325581a3ba7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704500"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configurare le notifiche di posta elettronica per i problemi in Azure Active Directory Domain Services

L'integrità di un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) viene monitorata dalla piattaforma Azure. Nella pagina stato integrità della portale di Azure vengono visualizzati tutti gli avvisi per il dominio gestito. Per garantire la risposta tempestiva ai problemi, è possibile configurare le notifiche tramite posta elettronica in modo da creare report sugli avvisi di integrità non appena vengono rilevati nel dominio gestito Azure AD DS.

Questo articolo illustra come configurare i destinatari delle notifiche tramite posta elettronica per un dominio gestito Azure AD DS.

## <a name="email-notification-overview"></a>Panoramica delle notifiche di posta elettronica

Per segnalare i problemi relativi a un dominio gestito di Azure AD DS, è possibile configurare le notifiche tramite posta elettronica. Queste notifiche di posta elettronica specificano il dominio gestito Azure AD DS in cui è presente l'avviso, oltre a fornire il tempo di rilevamento e un collegamento alla pagina di integrità nell'portale di Azure. È quindi possibile seguire i consigli per la risoluzione dei problemi forniti per risolvere i problemi.

La notifica di posta elettronica di esempio seguente indica che è stato generato un avviso o un avviso critico nel dominio gestito di Azure AD DS:

![Esempio di notifica di posta elettronica](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Assicurarsi sempre che il messaggio di posta elettronica provenga da un mittente Microsoft verificato prima di fare clic sui collegamenti nel messaggio. Le notifiche tramite posta elettronica provengono sempre dall'indirizzo `azure-noreply@microsoft.com`.

### <a name="why-would-i-receive-email-notifications"></a>Perché si ricevono notifiche di posta elettronica?

Azure AD DS invia notifiche tramite posta elettronica per aggiornamenti importanti sul dominio gestito. Queste notifiche sono solo per i problemi urgenti che influiscano sul servizio e devono essere risolti immediatamente. Ogni notifica tramite posta elettronica viene attivata da un avviso sul dominio gestito Azure AD DS. Gli avvisi vengono visualizzati anche nella portale di Azure e possono essere visualizzati nella pagina relativa allo [stato di Azure AD DS][check-health].

Azure AD DS non invia messaggi di posta elettronica per l'annuncio, gli aggiornamenti o le vendite.

### <a name="when-will-i-receive-email-notifications"></a>Quando si ricevono notifiche di posta elettronica?

Una notifica viene inviata immediatamente quando viene rilevato un [nuovo avviso][troubleshoot-alerts] in un dominio gestito di Azure AD DS. Se l'avviso non viene risolto, le notifiche di posta elettronica aggiuntive vengono inviate come promemoria ogni quattro giorni.

### <a name="who-should-receive-the-email-notifications"></a>A chi vengono inviate le notifiche di posta elettronica?

L'elenco dei destinatari di posta elettronica per Azure AD DS deve essere costituito da utenti che sono in grado di amministrare e apportare modifiche al dominio gestito. Questo elenco di messaggi di posta elettronica deve essere considerato come "prima risposta" per eventuali avvisi e problemi.

È possibile aggiungere fino a cinque destinatari di messaggi di posta elettronica aggiuntivi per le notifiche tramite posta elettronica. Se si desiderano più di cinque destinatari per le notifiche di posta elettronica, creare una lista di distribuzione e aggiungerla all'elenco di notifiche.

È anche possibile scegliere di fare in modo che tutti gli *amministratori globali* della directory Azure ad e tutti i membri del gruppo *AAD DC Administrators* ricevano le notifiche tramite posta elettronica. Azure AD DS Invia la notifica solo a un massimo di 100 indirizzi di posta elettronica, incluso l'elenco degli amministratori globali e degli amministratori di AAD DC.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per esaminare i destinatari delle notifiche di posta elettronica esistenti o aggiungere altri destinatari, seguire questa procedura:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**.
1. Selezionare il dominio gestito di Azure AD DS, ad esempio *aadds.contoso.com*.
1. Sul lato sinistro della finestra della risorsa Azure AD DS selezionare **impostazioni di notifica**. Vengono visualizzati i destinatari esistenti per le notifiche tramite posta elettronica.
1. Per aggiungere un destinatario di posta elettronica, immettere l'indirizzo di posta elettronica nella tabella destinatari aggiuntivi.
1. Al termine, selezionare **Salva** nella finestra di spostamento in alto a destra.

> [!WARNING]
> Quando si modificano le impostazioni di notifica, vengono aggiornate le impostazioni di notifica per l'intero dominio gestito Azure AD DS, non solo per se stessi.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>È stata ricevuta una notifica di posta elettronica per un avviso, ma nel portale di Azure non è presente alcun avviso. Che cosa è successo?

Se un avviso viene risolto, l'avviso viene cancellato dal portale di Azure. Il motivo più probabile è che un altro utente che riceve le notifiche tramite posta elettronica ha risolto l'avviso sul dominio gestito Azure AD DS oppure è stato risolto automaticamente dalla piattaforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Perché non è possibile modificare le impostazioni di notifica?

Se non si riesce ad accedere alla pagina delle impostazioni di notifica nella portale di Azure, non si dispone delle autorizzazioni necessarie per modificare il dominio gestito Azure AD DS. È necessario contattare un amministratore globale per ottenere le autorizzazioni per modificare Azure AD risorsa DS o essere rimosse dall'elenco di destinatari.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Non si ricevono notifiche di posta elettronica anche se è stato fornito l'indirizzo di posta elettronica. Perché?

Controllare la cartella posta indesiderata o posta indesiderata nel messaggio di posta elettronica per la notifica e assicurarsi di consentire il mittente del `azure-noreply@microsoft.com`.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione di alcuni problemi che possono essere segnalati, vedere [risolvere gli avvisi in un dominio gestito di Azure AD DS][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
