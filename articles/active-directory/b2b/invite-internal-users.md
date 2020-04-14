---
title: Invitare gli utenti interni alla collaborazione B2B - Azure ADInvite internal users to B2B collaboration - Azure AD
description: Se si dispone di account utente interni per partner, distributori, fornitori, fornitori e altri guest, è possibile passare alla collaborazione B2B di Azure AD invitandoli ad accedere con le proprie credenziali esterne o con il proprio account di accesso. Usare PowerShell o l'API di invito di Microsoft Graph.Use either PowerShell or the Microsoft Graph invitation API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097de14a3451e8d352dceb17436ae8423aa06533
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265941"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Invitare gli utenti interni alla collaborazione B2B

|     |
| --- |
| Invitare gli utenti interni a usare la collaborazione B2B è una funzionalità di anteprima pubblica di Azure Active Directory.Inviting internal users to use B2B collaboration is a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Prima della disponibilità della collaborazione B2B di Azure AD, le organizzazioni potevano collaborare con distributori, fornitori, fornitori e altri utenti guest impostando le credenziali interne per loro. Se si dispone di utenti guest interni come questo, è possibile invitarli a usare la collaborazione B2B in modo da poter sfruttare i vantaggi di Azure AD B2B. Gli utenti guest B2B saranno in grado di utilizzare le proprie identità e credenziali per accedere e non sarà necessario mantenere le password o gestire i cicli di vita degli account.

L'invio di un invito a un account interno esistente consente di mantenere l'ID oggetto, l'UPN, le appartenenze ai gruppi e le assegnazioni delle app dell'utente. Non è necessario eliminare e invitare nuovamente l'utente o riassegnare le risorse. Per invitare l'utente, utilizzerai l'API di invito per passare sia l'oggetto utente interno che l'indirizzo di posta elettronica dell'utente guest insieme all'invito. Quando l'utente accetta l'invito, il servizio B2B modifica l'oggetto utente interno esistente in un utente B2B. In futuro, l'utente deve accedere ai servizi di risorse cloud usando le credenziali B2B. Possono comunque usare le credenziali interne per accedere alle risorse locali, ma è possibile impedirlo reimpostando o modificando la password nell'account interno.

> [!NOTE]
> L'invito è unidirezionale. È possibile invitare utenti interni a utilizzare la collaborazione B2B, ma non è possibile rimuovere le credenziali B2B dopo l'aggiunta. Per modificare l'utente a un utente solo interno, è necessario eliminare l'oggetto utente e crearne uno nuovo.

Durante l'anteprima pubblica, il metodo descritto in questo articolo per invitare gli utenti interni alla collaborazione B2B non può essere utilizzato in questi casi:While in public preview, the method described in this article for inviting internal users to B2B collaboration can't be used in these instances:

- All'utente interno è già stata assegnata una licenza di Exchange.
- L'utente proviene da un dominio configurato per la federazione diretta nella directory.
- L'utente interno è un account solo cloud e il relativo account principale non è in Azure AD.

In questi casi, se l'utente interno deve essere cambiato in un utente B2B, è necessario eliminare l'account interno e inviare all'utente un invito per la collaborazione B2B.

**Utenti sincronizzati locali:** per gli account utente sincronizzati tra l'ambiente locale e il cloud, la directory locale rimane l'origine dell'autorità dopo essere stati invitati a usare la collaborazione B2B. Qualsiasi modifica apportata all'account locale verrà sincronizzata con l'account cloud, inclusa la disabilitazione o l'eliminazione dell'account. Pertanto, non è possibile impedire all'utente di accedere al proprio account locale mantenendo l'account cloud semplicemente eliminando l'account locale. È invece possibile impostare la password dell'account locale su un GUID casuale o su un altro valore sconosciuto.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Come invitare utenti interni alla collaborazione B2B

È possibile usare PowerShell o l'API di invito per inviare un invito B2B all'utente interno. Assicurarsi che l'indirizzo di posta elettronica che si desidera utilizzare per l'invito sia impostato come indirizzo di posta elettronica esterno nell'oggetto utente interno.

- Per un utente solo cloud, utilizzare l'indirizzo di posta elettronica nella proprietà User.OtherMails per l'invito.
- Per un utente sincronizzato locale, è necessario utilizzare il valore nella proprietà User.Mail per l'invito.
- Il dominio nella proprietà Mail dell'utente deve corrispondere all'account che sta utilizzando per accedere. In caso contrario, alcuni servizi, ad esempio Teams, non saranno in grado di autenticare l'utente.

Per impostazione predefinita, l'invito invierà all'utente un messaggio di posta elettronica per informarlo che sono stati invitati, ma è possibile eliminare questo messaggio di posta elettronica e inviarne uno personalizzato.

> [!NOTE]
> Per inviare il proprio messaggio di posta elettronica o altre comunicazioni, è possibile usare New-AzureADMSInvitation con -SendInvitationMessage:$false per invitare gli utenti in modo invisibile all'utente e quindi inviare il proprio messaggio di posta elettronica all'utente convertito. Vedere API e personalizzazione della [collaborazione B2B di Azure AD.](customize-invitation-api.md)

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Usare PowerShell per inviare un invito B2BUse PowerShell to send a B2B invitation

Utilizzare il comando seguente per invitare l'utente alla collaborazione B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Usare l'API di invito per inviare un invito B2BUse the invitation IS to send a B2B invitation

L'esempio seguente illustra come chiamare l'API di invito per invitare un utente interno come utente B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>"",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>""
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {"id": "<<ID for the user you want to convert>>"}
}
```

La risposta all'API è la stessa risposta che si ottiene quando si invita un nuovo utente guest alla directory.

## <a name="next-steps"></a>Passaggi successivi

- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)