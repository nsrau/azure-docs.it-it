---
title: Personalizzare la reimpostazione della password self-service - Azure Active DirectoryCustomize self-service password reset - Azure Active Directory
description: Informazioni su come personalizzare le opzioni di visualizzazione e esperienza utente per la reimpostazione della password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394254"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalizzare l'esperienza utente per la reimpostazione della password self-service di Azure Active DirectoryCustomize the user experience for Azure Active Directory self-service password reset

La reimpostazione della password self-service (SSPR) offre agli utenti di Azure Active Directory (Azure AD) la possibilità di modificare o reimpostare la password, senza alcun coinvolgimento dell'amministratore o dell'help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

Per migliorare l'esperienza SSPR per gli utenti, è possibile personalizzare l'aspetto della pagina di reimpostazione della password, delle notifiche tramite posta elettronica o delle pagine di accesso. Queste opzioni di personalizzazione ti consentono di rendere chiaro all'utente che si trovano nel posto giusto e di dare loro fiducia che accedono alle risorse aziendali.
    
In questo articolo viene illustrato come personalizzare il collegamento di posta elettronica SSPR per gli utenti, il marchio aziendale e il collegamento alla pagina di accesso di ADFS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizzare il collegamento "Contatta l'amministratore"

Per consentire agli utenti di contattare gli utenti per ricevere assistenza per la reimpostazione della password self-service, nel portale di reimpostazione della password viene visualizzato il collegamento "Contatta l'amministratore". Se un utente seleziona questo collegamento, esegue una delle due operazioni seguenti:If a user selects this link, it does one of two things:

* Se questo collegamento di contatto viene lasciato nello stato predefinito, viene inviato un messaggio di posta elettronica agli amministratori e viene chiesto di fornire assistenza nella modifica della password dell'utente. Il messaggio di posta elettronica di esempio seguente mostra questo messaggio di posta elettronica predefinito:

    ![Richiesta di esempio per reimpostare i messaggi di posta elettronica inviati all'amministratore](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizzato, invia l'utente a una pagina Web o a un indirizzo di posta elettronica specificato dall'amministratore per assistenza.
    * Se si personalizza questa impostazione, è consigliabile impostare questa opzione su un elemento che gli utenti hanno già familiarità con il supporto tecnico.

    > [!WARNING]
    > Se si personalizza questa impostazione con un indirizzo di posta elettronica e un account che richiede la reimpostazione della password, l'utente potrebbe non essere in grado di richiedere assistenza.

### <a name="default-email-behavior"></a>Comportamento di posta elettronica predefinito

L'e-mail di contatto predefinita viene inviata ai destinatari nel seguente ordine:

1. Se viene assegnato il ruolo di *amministratore del supporto tecnico* o di amministratore della *password,* gli amministratori con questi ruoli ricevono una notifica.
1. Se non viene assegnato alcun amministratore del supporto tecnico o dell'amministratore della password, gli amministratori con il ruolo di *amministratore utente* ricevono una notifica.
1. Se nessuno dei ruoli precedenti viene assegnato, gli *amministratori globali* ricevono una notifica.

In tutti i casi, viene inviata una notifica a un massimo di 100 destinatari totali.

Per altre informazioni sui diversi ruoli di amministratore e su come assegnarli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Disabilitare i messaggi di posta elettronica "Contatta l'amministratore"

Se l'organizzazione non desidera notificare agli amministratori le richieste di reimpostazione della password, è possibile utilizzare le opzioni di configurazione seguenti:

* Personalizzare il collegamento al supporto tecnico per fornire un URL Web o mailto: indirizzo che gli utenti possono usare per ottenere assistenza. Questa opzione si trova in **Modifica password** > **Personalizzazione** > Indirizzo di**posta elettronica o URL personalizzato.**
* Abilitare la reimpostazione della password self-service per tutti gli utenti. Questa opzione si trova in**Proprietà** **reimpostazione** > password . Se non si vuole che gli utenti reimpostino le proprie password, è possibile definire l'ambito di accesso a un gruppo vuoto. *Questa opzione non è consigliata.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalizzare la pagina di accesso e il pannello di accesso

È possibile personalizzare la pagina di accesso, ad esempio per aggiungere un logo che viene visualizzato insieme all'immagine adatta al marchio dell'azienda. Per altre informazioni su come configurare la personalizzazione aziendale, vedere Aggiungere il marchio aziendale alla pagina di [accesso in Azure AD.](../fundamentals/customize-branding.md)

Gli elementi grafici scelti vengono visualizzati nelle circostanze seguenti:

* Dopo che l'utente immette il proprio nome utente
* Se l'utente accede all'URL personalizzato:
   * Passando il `whr` parametro alla pagina di reimpostazione della password,`https://login.microsoftonline.com/?whr=contoso.com`
   * Passando il `username` parametro alla pagina di reimpostazione della password,`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome della directory

Per rendere le cose più facili da usare, è possibile modificare il nome dell'organizzazione nel portale e nelle comunicazioni automatizzate. Per modificare l'attributo del nome della directory nel portale di Azure, passare a**Proprietà**di **Azure Active Directory.** >  Questa opzione di nome descrittivo dell'organizzazione è la più visibile nei messaggi di posta elettronica automatizzati, come negli esempi seguenti:This friendly organization name option is the most visible in automated emails, as in the following examples:

* Il nome descrittivo nel messaggio di posta elettronica, ad esempio "*Microsoft per conto della demo CONTOSO*"
* La riga dell'oggetto nell'e-mail, ad esempio " CONTOSO demo account codice di*verifica e-mail*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalizzare la pagina di accesso di ADFSCustomize the AD FS sign-in page

Se si utilizza Active Directory Federation Services (ADFS) per gli eventi di accesso utente, è possibile aggiungere un collegamento alla pagina di accesso utilizzando le indicazioni fornite nell'articolo [Aggiungi descrizione pagina di accesso](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Fornire agli utenti un collegamento alla pagina in cui immettere *https://passwordreset.microsoftonline.com*il flusso di lavoro SSPR, ad esempio . Per aggiungere un collegamento alla pagina di accesso di ADFS, utilizzare il comando seguente nel server ADFS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'utilizzo di SSPR nell'ambiente, vedere Opzioni di report per la gestione delle password di [Azure AD.](howto-sspr-reporting.md)

Se l'utente o gli utenti hanno problemi con SSPR, vedere Risolvere i problemi relativi alla [reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
