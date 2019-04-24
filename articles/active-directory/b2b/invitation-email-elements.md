---
title: Gli elementi di posta elettronica di invito B2B - Azure Active Directory | Microsoft Docs
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7015abcfe3c53e2180d617bd2c78ecd44c42af7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412835"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. È possibile usarli per aumentare l'attendibilità del destinatario. È possibile aggiungere elementi di legittimità e riprova sociale al messaggio di posta elettronica, per assicurarsi che il destinatario scelga di selezionare con tranquillità il pulsante **Get Started** (Inizia) per accettare l'invito. L'attendibilità è uno strumento fondamentale per ridurre i problemi di condivisione. Il messaggio di posta elettronica deva anche avere un aspetto accattivante.

![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica
Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Subject
L'oggetto del messaggio di posta elettronica segue il modello seguente: Ti invitiamo all'organizzazione &lt;nometenant&gt;

### <a name="from-address"></a>Indirizzo del mittente.
Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn.  È consigliabile chiarire chi è il mittente dell'invito e a quale azienda appartiene, oltre a mettere in evidenza che il messaggio proviene da un indirizzo di posta elettronica Microsoft. Il formato è: Microsoft Invitations <invites@microsoft.com> oppure &lt;il nome visualizzato del mittente dell'invito&gt; dalla &lt;nometenant&gt; (tramite Microsoft) <invites@microsoft.com>.

### <a name="reply-to"></a>Rispondi a
Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="branding"></a>Personalizzazione
Nei messaggi di posta elettronica di invito vengono usate le informazioni personalizzate distintive dell'azienda eventualmente impostate per il tenant. Se si vogliono sfruttare i vantaggi offerti da questa funzionalità, [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) sono disponibili i dettagli su come configurarla. Il logo banner viene visualizzato nel messaggio di posta elettronica. Per ottenere risultati ottimali, seguire le istruzioni relative a qualità e dimensioni dell'immagine, disponibili [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal). Il nome dell'azienda viene anche visualizzato nella chiamata all'azione.

### <a name="call-to-action"></a>Chiamata all'azione
La chiamata all'azione è costituita da due parti: la spiegazione del motivo per cui il destinatario ha ricevuto il messaggio e l'indicazione delle successive azioni richieste al destinatario.
- La sezione "perché" può essere indirizzata usando il modello seguente: Si è stati invitati ad accedere alle applicazioni nell'organizzazione &lt;nometenant&gt;

- La sezione relativa alle "successive azioni richieste al destinatario" è indicata dalla presenza del pulsante **Get Started** (Inizia). Se per aggiungere il destinatario non è stato necessario inviare inviti, questo pulsante non viene visualizzato.

### <a name="inviters-information"></a>Informazioni sul mittente dell'invito
Il nome visualizzato del mittente dell'invito è incluso nel messaggio di posta elettronica. Nel messaggio di invito verrà inclusa anche l'immagine del profilo per l'account di Azure AD, qualora sia stata impostata. Entrambi consentono di rendere il messaggio di posta elettronica più attendibile per il destinatario.

Se non è ancora stata impostata un'immagine del profilo, al posto dell'immagine viene visualizzata un'icona con le iniziali del mittente dell'invito:

  ![Screenshot che mostra l'invito con il mittente dell'invito iniziali visualizzate](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Corpo
Il corpo contiene il messaggio digitato dal mittente dell'invito quando [viene invitato un utente guest alla directory, al gruppo o all'app](add-users-administrator.md) oppure [usando l'API di invito](customize-invitation-api.md). Si tratta di un'area di testo, che quindi non supporta l'elaborazione dei tag HTML per motivi di sicurezza.

  ![Screenshot che illustra il corpo del messaggio di posta elettronica di invito](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Sezione piè di pagina
Il piè di pagina contiene il marchio aziendale di Microsoft e indica al destinatario se il messaggio di posta elettronica è stato inviato da un alias non monitorato. 

Casi speciali:

- Il mittente dell'invito non dispone di un indirizzo di posta elettronica nella tenancy di invito

  ![Schermata di quando un mittente dell'invito non ha l'indirizzo di posta elettronica nella tenancy di invito](media/invitation-email-elements/inviter-no-email.png)


- Il destinatario non deve riscattare l'invito

  ![Schermata di quando il destinatario non deve riscattare l'invito](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Come viene determinata la lingua
La lingua presentata all'utente guest nel messaggio di posta elettronica di invito è determinata dalle impostazioni seguenti. Queste impostazioni sono elencate in base all'ordine di precedenza. Se un'impostazione non è configurata, viene usata l'impostazione successiva per determinare la lingua. 
- Proprietà **messageLanguage** dell'oggetto [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se viene usata l'API di creazione dell'invito
-   Proprietà **preferredLanguage** specificata nell'[oggetto utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) del guest
-   **Lingua delle notifiche** impostata nelle proprietà del tenant principale dell'utente guest (solo per i tenant di Azure AD)
-   **Lingua delle notifiche** impostata nelle proprietà del tenant della risorsa

Se non è configurata alcuna di queste impostazioni, come lingua verrà usato l'inglese (Stati Uniti) per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere gli utenti per la Collaborazione B2B senza un invito](add-user-without-invite.md)
