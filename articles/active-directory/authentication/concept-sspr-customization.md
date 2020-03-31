---
title: Personalizzare la reimpostazione della password self-service - Azure Active DirectoryCustomize self-service password reset - Azure Active Directory
description: Opzioni di personalizzazione per la reimpostazione della password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979465"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service

I professionisti IT che vogliono distribuire la reimpostazione della password self-service in Azure Active Directory (Azure AD) possono personalizzare l'esperienza in modo che corrisponda alle esigenze dei propri utenti.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizzare il collegamento "Contatta l'amministratore"

Gli utenti self-service per la reimpostazione della password dispongono di un collegamento "Contatta l'amministratore" nel portale di reimpostazione della password. Se un utente seleziona questo collegamento, eseguirà una delle due operazioni seguenti:If a user selects this link, it will do one of two things:

* Se lasciato nello stato predefinito:
   * La posta elettronica viene inviata agli amministratori e chiede loro di fornire assistenza nella modifica della password dell'utente. Vedere [l'e-mail di esempio](#sample-email) di seguito.
* Se personalizzato:
   * Invia l'utente a una pagina Web o a un indirizzo e-mail specificato dall'amministratore per assistenza.

> [!TIP]
> Se si personalizza questa impostazione, si consiglia di impostare questo elemento per qualcosa che gli utenti hanno già familiarità con per il supporto

> [!WARNING]
> Se si personalizza questa impostazione con un indirizzo di posta elettronica e un account che richiede la reimpostazione della password, l'utente potrebbe non essere in grado di richiedere assistenza.

### <a name="sample-email"></a>Esempio di messaggio di posta elettronica

![Richiesta di esempio per reimpostare i messaggi di posta elettronica inviati all'amministratore][Contact]

Questo contatto di posta elettronica viene inviato ai destinatari seguenti nell'ordine seguente:

1. Se viene assegnato il ruolo di **amministratore del supporto tecnico** o di amministratore della **password,** gli amministratori con questi ruoli ricevono una notifica.
1. Se non vengono assegnati amministratori del supporto tecnico o amministratori di password, gli amministratori con il ruolo di **amministratore utente** ricevono una notifica.
1. Se nessuno dei ruoli precedenti viene assegnato, gli **amministratori globali** ricevono una notifica.

In tutti i casi, viene inviata una notifica a un massimo di 100 destinatari totali.

Per altre informazioni sui diversi ruoli di amministratore e su come assegnarli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Disabilitare i messaggi di posta elettronica "Contatta l'amministratore"

Se l'organizzazione non vuole inviare notifiche agli amministratori circa le richieste di reimpostazione della password, è possibile abilitare la configurazione seguente:

* Abilitare la reimpostazione self-service delle password per tutti gli utenti finali. Questa opzione si trova in**Proprietà** **reimpostazione** > password . Se non si vuole che gli utenti reimpostino le proprie password, è possibile definire l'ambito di accesso a un gruppo vuoto. *Questa opzione non è consigliata.*
* Personalizzare il collegamento al supporto tecnico per fornire un URL Web o mailto: indirizzo che gli utenti possono usare per ottenere assistenza. Questa opzione si trova in **Modifica password** > **Personalizzazione** > Indirizzo di**posta elettronica o URL personalizzato.**

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizzare la pagina di accesso di AD FS per la reimpostazione della password self-service

Gli amministratori di Active Directory Federation Services (ADFS) possono aggiungere un collegamento alla pagina di accesso usando le indicazioni contenute nell'articolo [Aggiungere una descrizione della pagina di accesso](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Per aggiungere un collegamento alla pagina di accesso di AD FS, usare il comando seguente nel server AD FS. Gli utenti possono usare questa pagina per immettere il flusso di lavoro di reimpostazione della password self-service.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizzare l'aspetto della pagina di accesso e del pannello di accesso

È possibile personalizzare la pagina di accesso. È possibile aggiungere un logo da visualizzare con l'immagine appropriata per le informazioni personalizzate distintive dell'azienda.

Gli elementi grafici scelti vengono visualizzati nelle circostanze seguenti:

* Dopo che l'utente immette il proprio nome utente
* Se l'utente accede all'URL personalizzato:
   * Passando il `whr` parametro alla pagina di reimpostazione della password,`https://login.microsoftonline.com/?whr=contoso.com`
   * Passando il `username` parametro alla pagina di reimpostazione della password,`https://login.microsoftonline.com/?username=admin@contoso.com`

Per Informazioni dettagliate su come configurare le informazioni personalizzate distintive dell'azienda, vedere l'articolo [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Nome della directory

È possibile modificare l'attributo del nome della directory in**Proprietà**di **Azure Active Directory** > . È possibile visualizzare un nome descrittivo per l'organizzazione nel portale e nelle comunicazioni automatizzate. Questa opzione è la più visibile nei messaggi di posta elettronica automatizzati nei formati seguenti:

* Nome descrittivo nel messaggio di posta elettronica, ad esempio "Microsoft per conto di demo CONTOSO"
* Riga dell'oggetto nel messaggio di posta elettronica, ad esempio "Codice di verifica dell'indirizzo di posta elettronica dell'account demo CONTOSO"

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrati per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Hai una domanda di licenza?](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Credo che qualcosa sia rotto. Come si risolvono i problemi relativi a SSPR?](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contattare l'amministratore per assistenza nella reimpostazione dell'esempio di posta elettronica della password"
