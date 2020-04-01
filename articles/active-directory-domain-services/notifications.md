---
title: Notifiche tramite posta elettronica per Servizi di dominio Azure AD Documenti Microsoft'
description: Informazioni su come configurare le notifiche tramite posta elettronica per avvisare l'utente sui problemi in un dominio gestito di Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: ff5b0b430de53a67f4de0dacbd76a38a7de6e284
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475802"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configurare le notifiche di posta elettronica per i problemi in Servizi di dominio Azure Active DirectoryConfigure email notifications for issues in Azure Active Directory Domain Services

L'integrità di un dominio gestito di Servizi di dominio Azure Active Directory viene monitorata dalla piattaforma Azure.The health of an Azure Active Directory Domain Services (Azure AD DS) managed domain is monitored by the Azure platform. La pagina dello stato di integrità nel portale di Azure mostra tutti gli avvisi per il dominio gestito. Per assicurarsi che i problemi vengano risolti in modo tempestivo, è possibile configurare le notifiche tramite posta elettronica per la segnalazione degli avvisi di integrità non appena vengono rilevati nel dominio gestito di Servizi di dominio Active Directory di Azure.To make sure issues are to risposta to a timely manner, email notifications can be configured to report on health alerts as soon as they're detected in the Azure AD DS managed domain.

Questo articolo illustra come configurare i destinatari delle notifiche di posta elettronica per un dominio gestito di Servizi di dominio Active Directory di Azure.This article shows you how to configure email notification recipients for an Azure AD DS managed domain.

## <a name="email-notification-overview"></a>Panoramica delle notifiche e-mail

Per avvisare l'utente di problemi con un dominio gestito di Azure AD DS, è possibile configurare le notifiche tramite posta elettronica. Queste notifiche di posta elettronica specificano il dominio gestito di Servizi di dominio Active Directory di Azure in cui è presente l'avviso, oltre a fornire l'ora del rilevamento e un collegamento alla pagina di integrità nel portale di Azure.These email notifications specify the Azure AD DS managed domain that the alert is present on, as well as giving the time of detection and a link to the health page in the Azure portal. È quindi possibile seguire i consigli di risoluzione dei problemi forniti per risolvere i problemi.

La notifica tramite posta elettronica di esempio seguente indica che è stato generato un avviso o un avviso critico nel dominio gestito di Azure AD DS:The following example email notification indicates a critical warning or alert was generated on the Azure AD DS managed domain:

![Esempio di notifica di posta elettronica](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Assicurarsi sempre che il messaggio di posta elettronica provenga da un mittente Microsoft verificato prima di fare clic sui collegamenti nel messaggio. Le notifiche e-mail `azure-noreply@microsoft.com` provengono sempre dall'indirizzo.

### <a name="why-would-i-receive-email-notifications"></a>Perché si ricevono notifiche di posta elettronica?

Servizi di dominio Active Directory di Azure invia notifiche tramite posta elettronica per gli aggiornamenti importanti sul dominio gestito. Queste notifiche sono solo per problemi urgenti che influiscono sul servizio e devono essere affrontate immediatamente. Ogni notifica di posta elettronica viene attivata da un avviso nel dominio gestito di Servizi di dominio Active Directory di Azure.Each email notification is triggered by an alert on the Azure AD DS managed domain. Gli avvisi vengono visualizzati anche nel portale di Azure e possono essere visualizzati nella pagina Integrità di Servizi di dominio Active Directory di [Azure.][check-health]

Azure AD DS non invia messaggi di posta elettronica per scopi pubblicitari, aggiornamenti o vendite.

### <a name="when-will-i-receive-email-notifications"></a>Quando si ricevono notifiche di posta elettronica?

Una notifica viene inviata immediatamente quando viene trovato un nuovo avviso in un dominio gestito di Azure AD DS.A notification is sent immediately when a [new alert][troubleshoot-alerts] is found on an Azure AD DS managed domain. Se l'avviso non viene risolto, ogni quattro giorni vengono inviate notifiche e-mail aggiuntive come promemoria.

### <a name="who-should-receive-the-email-notifications"></a>A chi vengono inviate le notifiche di posta elettronica?

L'elenco dei destinatari di posta elettronica per Servizi di dominio Active Directory di Azure deve essere composto da utenti in grado di amministrare e apportare modifiche al dominio gestito. Questa lista di e-mail dovrebbe essere considerato come il vostro "primi soccorritori" a eventuali avvisi e problemi.

È possibile aggiungere fino a cinque destinatari di posta elettronica aggiuntivi per le notifiche e-mail. Se si desidera noto più di cinque destinatari per le notifiche tramite posta elettronica, creare una lista di distribuzione e aggiungerla all'elenco delle notifiche.

È anche possibile scegliere di fare in modo che tutti *gli amministratori globali* della directory di Azure AD e ogni membro del gruppo *AAD DC Administrators* riceva notifiche tramite posta elettronica. Servizi di dominio Active Directory di Azure invia una notifica solo a un massimo di 100 indirizzi di posta elettronica, incluso l'elenco degli amministratori globali e degli amministratori di AAD DC.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

La procedura seguente illustra come esaminare i destinatari delle notifiche di posta elettronica esistenti o aggiungere altri destinatari:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD.**
1. Selezionare il dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio *aaddscontoso.com*.
1. Sul lato sinistro della finestra delle risorse di Azure AD DS selezionare **Impostazioni di notifica**. Vengono visualizzati i destinatari esistenti per le notifiche e-mail.
1. Per aggiungere un destinatario di posta elettronica, immettere l'indirizzo di posta elettronica nella tabella dei destinatari aggiuntivi.
1. Al termine, selezionare **Salva** nella barra di spostamento dall'alto.

> [!WARNING]
> Quando si modificano le impostazioni di notifica, le impostazioni di notifica per l'intero dominio gestito di Azure AD DS vengono aggiornate, non solo se stessi.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>È stata ricevuta una notifica di posta elettronica per un avviso, ma nel portale di Azure non è presente alcun avviso. Che cosa è successo?

Se un avviso viene risolto, l'avviso viene cancellato dal portale di Azure.If an alert is resolved, the alert is cleared from the Azure portal. Il motivo più probabile è che un altro utente che riceve notifiche tramite posta elettronica ha risolto l'avviso nel dominio gestito di Servizi di dominio Active Directory di Azure oppure è stato risolto automaticamente dalla piattaforma Azure.The most likely reason is that someone else who receives email notifications resolved the alert on the Azure AD DS managed domain, or it was autoresolved by Azure platform.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Perché non è possibile modificare le impostazioni di notifica?

Se non è possibile accedere alla pagina delle impostazioni di notifica nel portale di Azure, non si dispone delle autorizzazioni necessarie per modificare il dominio gestito di Servizi di dominio Active Directory di Azure.If you're unable to access the notification settings page in the Azure portal, you don't have the permissions to edit the Azure AD DS managed domain. È necessario contattare un amministratore globale per ottenere le autorizzazioni per modificare la risorsa di Servizi di dominio Active Directory di Azure o essere rimosso dall'elenco dei destinatari.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Non si ricevono notifiche di posta elettronica anche se è stato fornito l'indirizzo di posta elettronica. Perché?

Controlla la tua cartella spam o spazzatura nella tua e-mail per la notifica e assicurati di consentire al mittente di `azure-noreply@microsoft.com`.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione di alcuni dei problemi che possono essere segnalati, vedere Risolvere gli avvisi in un dominio gestito di Azure AD DS.For more information on troubleshooting some of the issues that may be reported, see [Resolve alerts on an Azure AD DS managed domain][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
