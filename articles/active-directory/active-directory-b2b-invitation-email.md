---
title: Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.custom: seohack1
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 05/23/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 0d26fc4758b2defc3f1b8b5643daafdac4d2baac
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. È possibile usarli per aumentare l'attendibilità del destinatario. È possibile aggiungere elementi di legittimità e riprova sociale al messaggio di posta elettronica, per assicurarsi che il destinatario scelga di selezionare con tranquillità il pulsante **Get Started** (Inizia) per accettare l'invito. L'attendibilità è uno strumento fondamentale per ridurre i problemi di condivisione. Il messaggio di posta elettronica deva anche avere un aspetto accattivante.

![Messaggio di posta elettronica di invito per la collaborazione B2B](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica
Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Oggetto
Il contenuto dell'oggetto del messaggio di posta elettronica è basato su questo modello: Ti invitiamo all'organizzazione &lt;nometenant&gt;

### <a name="from-address"></a>Indirizzo del mittente.
Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn.  È consigliabile chiarire chi è il mittente dell'invito e a quale azienda appartiene, oltre a mettere in evidenza che il messaggio proviene da un indirizzo di posta elettronica Microsoft. Il formato è: &lt;nome visualizzato del mittente dell'invito&gt; da &lt;nometenant&gt; (tramite Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Rispondi a
Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="branding"></a>Personalizzazione
Nei messaggi di posta elettronica di invito vengono usate le informazioni personalizzate distintive dell'azienda eventualmente impostate per il tenant. Se si vogliono sfruttare i vantaggi offerti da questa funzionalità, [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) sono disponibili i dettagli su come configurarla. Il logo banner viene visualizzato nel messaggio di posta elettronica. Per ottenere risultati ottimali, seguire le istruzioni relative a qualità e dimensioni dell'immagine, disponibili [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal). Il nome dell'azienda viene anche visualizzato nella chiamata all'azione.

### <a name="call-to-action"></a>Chiamata all'azione
La chiamata all'azione è costituita da due parti: la spiegazione del motivo per cui il destinatario ha ricevuto il messaggio e l'indicazione delle successive azioni richieste al destinatario.
- La sezione relativa al "motivo" può essere basata sul seguente modello: Hai ricevuto l'invito per accedere alle applicazioni nell'organizzazione &lt;nometenant&gt;

- La sezione relativa alle "successive azioni richieste al destinatario" è indicata dalla presenza del pulsante **Get Started** (Inizia). Se per aggiungere il destinatario non è stato necessario inviare inviti, questo pulsante non viene visualizzato.

### <a name="inviters-information"></a>Informazioni sul mittente dell'invito
Il nome visualizzato del mittente dell'invito è incluso nel messaggio di posta elettronica. Nel messaggio di invito verrà inclusa anche l'immagine del profilo per l'account di Azure AD, qualora sia stata impostata. Entrambi consentono di rendere il messaggio di posta elettronica più attendibile per il destinatario.

Se non è ancora stata impostata un'immagine del profilo, al posto dell'immagine viene visualizzata un'icona con le iniziali del mittente dell'invito:

  ![visualizzazione iniziali del mittente dell'invito](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Corpo
Il corpo contiene il messaggio digitato dal mittente dell'invito oppure passato tramite l'API di invito. Si tratta di un'area di testo, che quindi non supporta l'elaborazione dei tag HTML per motivi di sicurezza.

### <a name="footer-section"></a>Sezione piè di pagina
Il piè di pagina contiene il marchio aziendale di Microsoft e indica al destinatario se il messaggio di posta elettronica è stato inviato da un alias non monitorato. Casi speciali:

- Il mittente dell'invito non dispone di un indirizzo di posta elettronica nella tenancy di invito

  ![l'immagine del profilo del mittente dell'invito non è associata a un indirizzo di posta elettronica nella tenancy di invito](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- Il destinatario non deve riscattare l'invito

  ![caso in cui il destinatario non deve riscattare l'invito](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è la collaborazione B2B di Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](active-directory-b2b-iw-add-users.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
