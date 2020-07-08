---
title: Invitare utenti interni a collaborazione B2B-Azure AD
description: Se si dispone di account utente interni per partner, distributori, fornitori, fornitori e altri Guest, è possibile passare a Azure AD collaborazione B2B invitandoli ad accedere con le proprie credenziali esterne o con l'account di accesso. Usare PowerShell o l'API di invito Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551376"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Invitare gli utenti interni alla collaborazione B2B

> [!NOTE]
> Invitare gli utenti interni a usare la collaborazione B2B è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prima della disponibilità di Azure AD collaborazione B2B, le organizzazioni potevano collaborare con i distributori, i fornitori, i fornitori e gli altri utenti Guest impostando le relative credenziali interne. Se gli utenti Guest interni sono simili a questo, è possibile invitare gli utenti a usare la collaborazione B2B per poter sfruttare i vantaggi di Azure AD B2B. Gli utenti Guest B2B saranno in grado di usare le proprie identità e credenziali per l'accesso e non sarà necessario gestire le password o gestire i cicli di vita dell'account.

L'invio di un invito a un account interno esistente consente di mantenere l'ID oggetto, l'UPN, l'appartenenza ai gruppi e le assegnazioni di app dell'utente. Non è necessario eliminare manualmente e invitare di nuovo l'utente o riassegnare le risorse. Per invitare l'utente, si userà l'API di invito per passare l'oggetto utente interno e l'indirizzo di posta elettronica dell'utente Guest insieme all'invito. Quando l'utente accetta l'invito, il servizio B2B imposta l'oggetto utente interno esistente su un utente B2B. In futuro, l'utente deve accedere ai servizi risorse cloud usando le credenziali B2B. Possono comunque usare le proprie credenziali interne per accedere alle risorse locali, ma è possibile evitare questo problema reimpostando o modificando la password nell'account interno.

> [!NOTE]
> L'invito è unidirezionale. È possibile invitare gli utenti interni a usare la collaborazione B2B, ma non è possibile rimuovere le credenziali B2B dopo che sono state aggiunte. Per ripristinare un utente solo interno, è necessario eliminare l'oggetto utente e crearne uno nuovo.

Durante l'anteprima pubblica, il metodo descritto in questo articolo per invitare gli utenti interni alla collaborazione B2B non può essere usato nelle istanze seguenti:

- All'utente interno è già stata assegnata una licenza di Exchange.
- L'utente è da un dominio configurato per la Federazione diretta nella directory.
- L'utente interno è un account solo cloud e il relativo account principale non è in Azure AD.

In questi casi, se l'utente interno deve essere modificato in un utente B2B, è necessario eliminare l'account interno e inviare all'utente un invito per la collaborazione B2B.

**Utenti sincronizzati locali**: per gli account utente sincronizzati tra l'ambiente locale e il cloud, la directory locale rimane l'origine dell'autorità dopo che questi sono stati invitati a usare la collaborazione B2B. Qualsiasi modifica apportata all'account locale verrà sincronizzata con l'account cloud, inclusa la disabilitazione o l'eliminazione dell'account. Pertanto, non è possibile impedire all'utente di accedere al proprio account locale mantenendo il proprio account cloud semplicemente eliminando l'account locale. È invece possibile impostare la password dell'account locale su un GUID casuale o un altro valore sconosciuto.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Come invitare gli utenti interni alla collaborazione B2B

È possibile usare PowerShell o l'API di invito per inviare un invito B2B all'utente interno. Verificare che l'indirizzo di posta elettronica che si vuole usare per l'invito sia impostato come indirizzo di posta elettronica esterno per l'oggetto utente interno.

- Per un utente solo cloud, usare l'indirizzo di posta elettronica nella proprietà User. OtherMails per l'invito.
- Per un utente sincronizzato locale, è necessario usare il valore nella proprietà User. mail per l'invito.
- Il dominio nella proprietà della posta dell'utente deve corrispondere all'account usato per l'accesso. In caso contrario, alcuni servizi, ad esempio i team, non saranno in grado di autenticare l'utente.

Per impostazione predefinita, l'invito invierà all'utente un messaggio di posta elettronica che informa che è stato invitato, ma è possibile eliminarlo e inviare il proprio messaggio.

> [!NOTE]
> Per inviare un messaggio di posta elettronica o un'altra comunicazione, è possibile usare New-AzureADMSInvitation con-SendInvitationMessage: $false per invitare gli utenti in modalità invisibile all'utente e quindi inviare un messaggio di posta elettronica all'utente convertito. Vedere [Azure ad API di collaborazione B2B e personalizzazione](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Usare PowerShell per inviare un invito B2B

Usare il comando seguente per invitare l'utente a collaborare B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Usare l'API di invito per inviare un invito B2B

Nell'esempio seguente viene illustrato come chiamare l'API di invito per invitare un utente interno come utente B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

La risposta all'API è la stessa che si ottiene quando si invita un nuovo utente guest nella directory.

## <a name="next-steps"></a>Passaggi successivi

- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
