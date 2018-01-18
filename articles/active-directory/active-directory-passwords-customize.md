---
title: Personalizzazione per la reimpostazione della password self-service - Azure Active Directory
description: Opzioni di personalizzazione per la reimpostazione della password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 6d8a2f2106e57bdf84bc3bead70d379691b79742
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service

I professionisti IT che vogliono distribuire la reimpostazione della password self-service in Azure Active Directory (Azure AD) possono personalizzare l'esperienza in modo che corrisponda alle esigenze dei propri utenti.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizzare il collegamento "Contatta l'amministratore"

Anche se la reimpostazione della password self-service non è abilitata, gli utenti possono tuttavia usare un collegamento "Contatta l'amministratore" nel portale di reimpostazione della password. Se un utente seleziona il collegamento:
   * Invia un messaggio di posta elettronica agli amministratori e richiede assistenza nella modifica della password dell'utente. 
   * Invia gli utenti a un URL specificato per ottenere assistenza. 

È consigliabile impostare questo contatto ad esempio per un indirizzo di posta elettronica o un sito Web che gli utenti sono soliti usare per inviare domande al supporto tecnico.

![Contatto][Contact]

Questo contatto di posta elettronica viene inviato ai destinatari seguenti nell'ordine seguente:

1. Se viene assegnato il ruolo **Amministratore password**, agli amministratori con questo ruolo viene inviata una notifica.
2. Se non è stato assegnato nessun amministratore password, la notifica verrà inviata agli amministratori che hanno il ruolo **Amministratore utenti**.
3. Se nessuno dei ruoli precedenti è assegnato, la notifica verrà inviata agli **Amministratori globali**.

In tutti i casi, viene inviata una notifica a un massimo di 100 destinatari totali.

Per altre informazioni sui diversi ruoli di amministratore e su come assegnarli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Disabilitare i messaggi di posta elettronica "Contatta l'amministratore"

Se l'organizzazione non vuole inviare notifiche agli amministratori circa le richieste di reimpostazione della password, è possibile abilitare la configurazione seguente:

* Abilitare la reimpostazione self-service delle password per tutti gli utenti finali. Questa opzione è in **Reimpostazione password** > **Proprietà**.
  
  Se non si vuole che gli utenti reimpostino le proprie password, è possibile definire l'ambito di accesso a un gruppo vuoto. *Questa opzione non è consigliata.*
* Personalizzare il collegamento al supporto tecnico per fornire un URL Web o mailto: indirizzo che gli utenti possono usare per ottenere assistenza. Questa opzione si trova in **Reimpostazione password** > **Personalizzazione** > **Indirizzo di posta elettronica o URL del supporto tecnico**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizzare la pagina di accesso di AD FS per la reimpostazione della password self-service

Gli amministratori di Active Directory Federation Services (ADFS) possono aggiungere un collegamento alla pagina di accesso usando le indicazioni contenute nell'articolo [Aggiungere una descrizione della pagina di accesso](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Per aggiungere un collegamento alla pagina di accesso di AD FS, usare il comando seguente nel server AD FS. Gli utenti possono usare questa pagina per immettere il flusso di lavoro di reimpostazione della password self-service.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizzare l'aspetto della pagina di accesso e del pannello di accesso

È possibile personalizzare la pagina di accesso. È possibile aggiungere un logo da visualizzare con l'immagine appropriata per le informazioni personalizzate distintive dell'azienda.

Gli elementi grafici scelti vengono visualizzati nelle circostanze seguenti:

* Dopo che l'utente immette il proprio nome utente
* Se l'utente accede all'URL personalizzato:
    * Passando il parametro *whr* alla pagina per la reimpostazione della password, ad esempio "https://login.microsoftonline.com/?whr=contoso.com"
    * Passando il parametro *username* alla pagina per la reimpostazione della password, ad esempio "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Dettagli di grafica

Usare le impostazioni seguenti per modificare le caratteristiche visive della pagina di accesso. Andare a **Azure Active Directory** > **Informazioni personalizzate distintive dell'azienda** > **Modifica informazioni personalizzate distintive dell'azienda**:

* Questa immagine della pagina di accesso deve essere un file PNG o JPG da 1420 x 1200 pixel e non superiore a 500 KB. Per ottenere risultati ottimali, è consigliabile scegliere dimensioni di circa 200 KB.
* Il colore di sfondo della pagina di accesso viene usato nelle connessioni a latenza elevata e deve essere in formato RGB esadecimale.
* L'immagine del banner deve essere un file PNG o JPG da 60 x 280 pixel e non superiore a 10 KB.
* Il logo quadrato (tema normale e scuro) deve essere un file PNG o JPG da 240 x 240 pixel (ridimensionabili) e non superiore a 10 KB.

### <a name="sign-in-text-options"></a>Opzioni del testo di accesso

Usare le impostazioni seguenti per aggiungere alla pagina di accesso testo pertinente all'organizzazione. Andare a **Azure Active Directory** > **Informazioni personalizzate distintive dell'azienda** > **Modifica informazioni personalizzate distintive dell'azienda**:

* **Suggerimento nome utente**: sostituisce il testo di esempio *someone@example.com* con uno più appropriato per gli utenti. È consigliabile lasciare il suggerimento predefinito quando si supportano utenti interni ed esterni.
* **Testo della pagina di accesso**: può contenere un massimo di 256 caratteri. Questo testo viene visualizzato dove gli utenti eseguono l'accesso online e nell'esperienza Workplace Join di Azure AD in Windows 10. Usare questo testo per le condizioni per l'utilizzo, le istruzioni e i suggerimenti per gli utenti. 

   >[!IMPORTANT]
   >Poiché chiunque può visualizzare la pagina di accesso, non fornire informazioni riservate qui.
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>Impostazione "Mantieni l'accesso disabilitata"

Con l'opzione **Mantieni l'accesso disabilitata** gli utenti possono restare connessi quando chiudono e riaprono la finestra del browser. Questa opzione non influisce sulla durata della sessione. Andare a **Azure Active Directory** > **Informazioni personalizzate distintive dell'azienda** > **Modifica informazioni personalizzate distintive dell'azienda**.

Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dalla possibilità per gli utenti di selezionare questa casella di controllo. Se si nasconde questa opzione, gli utenti possono ottenere prompt di accesso aggiuntivi e imprevisti.

### <a name="directory-name"></a>Nome della directory

È possibile modificare l'attributo del nome della directory in **Azure Active Directory** > **Proprietà**. È possibile visualizzare un nome descrittivo per l'organizzazione nel portale e nelle comunicazioni automatizzate. Questa opzione è la più visibile nei messaggi di posta elettronica automatizzati nei formati seguenti:

* Nome descrittivo nel messaggio di posta elettronica, ad esempio "Microsoft per conto di demo CONTOSO"
* Riga dell'oggetto nel messaggio di posta elettronica, ad esempio "Codice di verifica dell'indirizzo di posta elettronica dell'account demo CONTOSO"

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Esempio di messaggio di posta elettronica per contattare l'amministratore e richiedere assistenza per la reimpostazione della password"
