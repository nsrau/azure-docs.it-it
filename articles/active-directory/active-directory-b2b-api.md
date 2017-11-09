---
title: API e personalizzazione per Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: c85e05b38b4a9525e13ec510a17b7ef4841198d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API e personalizzazione per Collaborazione B2B di Azure Active Directory

Molti clienti hanno manifestato il desiderio di poter personalizzare il processo di invito in modo più adatto alla propria organizzazione. Con l'API, è possibile farlo. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funzionalità dell'API di invito
L'API offre le funzionalità seguenti:

1. Invitare un utente esterno con *qualsiasi* indirizzo di posta elettronica.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalizzare la pagina di destinazione visualizzata dagli utenti dopo avere accettato l'invito.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Scegliere di inviare il messaggio di invito standard tramite Microsoft

    ```
    "sendInvitationMessage": true
    ```

  con un messaggio personalizzabile per il destinatario

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Scegliere di mettere in copia conoscenza le persone che si vuole mantenere nel ciclo relativo all'invito di questo collaboratore.

5. Personalizzare completamente l'invito e il flusso di lavoro di onboarding scegliendo di non inviare notifiche tramite Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  In questo caso, si ottiene un URL di riscatto dall'API, che è possibile incorporare in un modello di messaggio di posta elettronica, in un messaggio istantaneo o in un altro metodo di distribuzione.

6. Gli amministratori infine possono scegliere di invitare l'utente come membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modello di autorizzazione
L'API può essere eseguita nelle modalità di autorizzazione seguenti:

### <a name="app--user-mode"></a>Modalità app + utente
In questa modalità, chiunque usi l'API deve disporre delle autorizzazioni per creare gli inviti B2B.

### <a name="app-only-mode"></a>Modalità solo app
In un contesto solo app, per garantire l'esito positivo dell'invio è necessario che l'app disponga dell'ambito User.ReadWrite.All o Directory.ReadWrite.All.

Per altre informazioni, vedere https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Ora è possibile usare PowerShell per aggiungere e invitare facilmente utenti esterni a un'organizzazione. Creare un invito tramite il cmdlet:

```
New-AzureADMSInvitation
```

È possibile usare le opzioni seguenti:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

È anche possibile controllare il riferimento all'API di invito in [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
