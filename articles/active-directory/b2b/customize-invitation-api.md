---
title: API e personalizzazione per Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 20c824da82d6e3e66bfa2d7447c8a9573cbdce69
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985814"
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
In un contesto di solo app l'invito viene inviato solo se nell'app è presente l'ambito User.Invite.All.

Per altre informazioni, fare riferimento a: https://graph.microsoft.io/docs/authorization/permission_scopes


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

È anche possibile eseguire il check-out del riferimento all'API di invito in [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](invitation-email-elements.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere gli utenti per la Collaborazione B2B senza un invito](add-user-without-invite.md)

