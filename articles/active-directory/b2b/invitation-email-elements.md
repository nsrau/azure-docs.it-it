---
title: Elementi dell'e-mail di invito B2B - Azure Active Directory Documenti Microsoft
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407202"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. Anche se non è necessario inviare un messaggio di posta elettronica per invitare qualcuno utilizzando la [collaborazione B2B,](add-user-without-invite.md)in questo modo fornisce all'utente tutte le informazioni necessarie per prendere una decisione sull'accettazione dell'invito. Fornisce anche loro un link a cui possono sempre fare riferimento in futuro quando hanno bisogno di tornare alle tue risorse.

![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Questo nuovo modello di messaggio di posta elettronica è ancora in fase di implementazione in tutti i tenant, pertanto alcuni tenant usano ancora una progettazione precedente. Entro la fine di maggio 2020, gli inviti di tutti i tenant utilizzeranno questo modello.

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica

Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Oggetto

L'oggetto dell'e-mail segue questo modello:

&lt;nome&gt; utente vi ha invitato ad accedere alle applicazioni all'interno della loro organizzazione.

### <a name="from-address"></a>Indirizzo del mittente.

Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn. Questo modello dovrebbe chiarire che anche invites@microsoft.comse il messaggio di posta elettronica proviene da , l'invito proviene da un'altra organizzazione. Il formato è: <invites@microsoft.com> Inviti Microsoft o &lt;Inviti&gt; <invites@microsoft.com>Microsoft per conto di nometenant . 

### <a name="reply-to"></a>Rispondi a

Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="phishing-warning"></a>Avviso di phishing

L'e-mail inizia con un breve avviso per l'utente sul phishing, avvisandolo che dovrebbero accettare solo gli inviti che si aspettano. È buona norma assicurarsi che i partner che stai invitando non saranno sorpresi dal tuo invito menzionandolo a loro in anticipo.

![Immagine dell'avviso di phishing nell'e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informazioni sul mittente dell'invito

Il messaggio di posta elettronica include informazioni sull'invitante e sull'organizzazione da cui invia l'invito. Sono inclusi il nome e l'indirizzo di posta elettronica del mittente, nonché il nome e il dominio primario associati all'organizzazione. Tutte queste informazioni dovrebbero aiutare l'invitato a prendere una decisione informata sull'accettazione dell'invito.

![Immagine delle informazioni dell'invitante nell'e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Messaggio d'invito

Se l'invito include un messaggio come parte dell'invito quando [invita un utente guest alla directory, al gruppo o all'app](add-users-administrator.md) oppure quando [utilizza l'API](customize-invitation-api.md)di invito, il messaggio viene evidenziato nella sezione principale del messaggio di posta elettronica. Sono inclusi anche il nome e l'immagine del profilo dell'invitato, se ne hanno impostato uno. Il messaggio stesso è un'area di testo, quindi per motivi di sicurezza, non elabora i tag HTML.

![Immagine del messaggio di invito nell'e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Pulsante Accetta e URL di reindirizzamento

La sezione successiva dell'e-mail contiene informazioni su dove verrà portato l'invitato dopo aver accettato l'invito, nonché un pulsante per farlo.  In futuro, l'invitato potrà sempre utilizzare questo collegamento per tornare direttamente alle risorse.

![Immagine del pulsante di accettazione e reindirizzamento dell'URL nell'e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sezione piè di pagina

Il piè di pagina contiene ulteriori informazioni sull'invito inviato. C'è sempre un'opzione per l'invitato per bloccare gli inviti futuri. Se l'organizzazione ha [impostato un'informativa sulla privacy](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), il collegamento all'istruzione viene visualizzato qui.  In caso contrario, una nota indica che l'organizzazione non ha impostato un'informativa sulla privacy.

![Immagine della sezione del piè di pagina nell'e-mail](media/invitation-email-elements/footer-section.png)
 
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
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere utenti di Collaborazione B2B senza invito](add-user-without-invite.md)
