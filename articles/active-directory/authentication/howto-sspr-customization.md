---
title: Personalizzare la reimpostazione della password self-service-Azure Active Directory
description: Informazioni su come personalizzare le opzioni per la visualizzazione e l'esperienza dell'utente per Azure AD la reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8690b1a4b8527a397fcf4c04892e167b5332358d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035401"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalizzare l'esperienza utente per la reimpostazione della password self-service Azure Active Directory

La reimpostazione della password self-service (SSPR) consente agli utenti di Azure Active Directory (Azure AD) di modificare o reimpostare la password, senza alcun intervento da parte di amministratore o di help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

Per migliorare l'esperienza di SSPR per gli utenti, è possibile personalizzare l'aspetto della pagina di reimpostazione della password, le notifiche tramite posta elettronica o le pagine di accesso. Queste opzioni di personalizzazione consentono di chiarire all'utente che si trovano nel posto giusto e offrono loro fiducia nell'accesso alle risorse aziendali.
    
Questo articolo illustra come personalizzare il collegamento di posta elettronica SSPR per utenti, informazioni personalizzate distintive dell'azienda e collegamento alla pagina di accesso AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizzare il collegamento "Contatta l'amministratore"

Per consentire agli utenti di ottenere assistenza per la reimpostazione della password self-service, viene visualizzato il collegamento "contattare l'amministratore" nel portale di reimpostazione della password. Se un utente seleziona questo collegamento, esegue una delle due operazioni seguenti:

* Se il collegamento al contatto viene lasciato nello stato predefinito, viene inviato un messaggio di posta elettronica agli amministratori e viene chiesto di fornire assistenza per la modifica della password dell'utente. Nel messaggio di posta elettronica di esempio seguente viene visualizzato questo messaggio di posta elettronica predefinito:

    ![Richiesta di esempio per reimpostare la posta elettronica inviata all'amministratore](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizzato, invia l'utente a una pagina Web o a un indirizzo di posta elettronica specificato dall'amministratore per assistenza.
    * Se si Personalizza questa impostazione, è consigliabile impostarla su un elemento con cui gli utenti hanno già familiarità per il supporto.

    > [!WARNING]
    > Se si Personalizza questa impostazione con un indirizzo di posta elettronica e un account che richiedono la reimpostazione della password, l'utente potrebbe non essere in grado di richiedere assistenza.

### <a name="default-email-behavior"></a>Comportamento predefinito della posta elettronica

Il messaggio di posta elettronica di contatto predefinito viene inviato ai destinatari nell'ordine seguente:

1. Se viene assegnato il ruolo di *amministratore del supporto tecnico* o la *password* , agli amministratori con questi ruoli viene inviata una notifica.
1. Se non è stato assegnato alcun amministratore del supporto tecnico o della password, gli amministratori con il ruolo di *amministratore utente* riceveranno una notifica.
1. Se nessuno dei ruoli precedenti viene assegnato, viene inviata una notifica agli *amministratori globali* .

In tutti i casi, viene inviata una notifica a un massimo di 100 destinatari totali.

Per altre informazioni sui diversi ruoli di amministratore e su come assegnarli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Disabilitare i messaggi di posta elettronica "Contatta l'amministratore"

Se l'organizzazione non vuole inviare notifiche agli amministratori sulle richieste di reimpostazione della password, è possibile usare le opzioni di configurazione seguenti:

* Personalizzare il collegamento al supporto tecnico per fornire un URL Web o mailto: indirizzo che gli utenti possono usare per ottenere assistenza. Questa opzione si trova in **reimpostazione password**  >  **Customization**  >  **personalizzato indirizzo di posta elettronica o URL del supporto tecnico**.
* Abilitare la reimpostazione self-service della password per tutti gli utenti. Questa opzione si trova in proprietà di **reimpostazione della password**  >  **Properties**. Se non si vuole che gli utenti reimpostino le proprie password, è possibile definire l'ambito di accesso a un gruppo vuoto. *Questa opzione non è consigliata.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalizzare la pagina di accesso e il pannello di accesso

È possibile personalizzare la pagina di accesso, ad esempio per aggiungere un logo che viene visualizzato insieme all'immagine che soddisfa le informazioni personalizzate distintive dell'azienda. Per ulteriori informazioni su come configurare le informazioni personalizzate distintive dell'azienda, vedere Aggiungere informazioni personalizzate distintive [dell'azienda alla pagina di accesso in Azure ad](../fundamentals/customize-branding.md).

Gli elementi grafici scelti vengono visualizzati nelle circostanze seguenti:

* Dopo che l'utente immette il proprio nome utente
* Se l'utente accede all'URL personalizzato:
   * Passando il `whr` parametro alla pagina di reimpostazione della password, ad esempio`https://login.microsoftonline.com/?whr=contoso.com`
   * Passando il `username` parametro alla pagina di reimpostazione della password, ad esempio`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome della directory

Per rendere le cose più semplici da usare, è possibile modificare il nome dell'organizzazione nel portale e nelle comunicazioni automatiche. Per modificare l'attributo nome directory nella portale di Azure, passare a **Azure Active Directory**  >  **proprietà**. L'opzione nome descrittivo dell'organizzazione è la più visibile nei messaggi di posta elettronica automatizzati, come negli esempi seguenti:

* Nome descrittivo nel messaggio di posta elettronica, ad esempio "*Microsoft per conto di Contoso demo*"
* Riga dell'oggetto nel messaggio di posta elettronica, ad esempio "*codice di verifica della posta elettronica dell'account demo Contoso*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalizzare la pagina di accesso AD FS

Se si usa Active Directory Federation Services (AD FS) per gli eventi di accesso degli utenti, è possibile aggiungere un collegamento alla pagina di accesso usando le istruzioni riportate nell'articolo per [aggiungere la descrizione della pagina di accesso](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Fornire agli utenti un collegamento alla pagina per l'immissione del flusso di lavoro SSPR, ad esempio *https://passwordreset.microsoftonline.com* . Per aggiungere un collegamento alla pagina di accesso AD FS, usare il comando seguente nel server AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'utilizzo di SSPR nell'ambiente, vedere [Opzioni di Reporting per la gestione delle password Azure ad](howto-sspr-reporting.md).

Se gli utenti hanno problemi con SSPR, vedere risolvere i problemi di [reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
