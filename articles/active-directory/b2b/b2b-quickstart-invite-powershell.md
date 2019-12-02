---
title: 'Guida introduttiva: Aggiungere un utente guest con PowerShell - Azure AD'
description: In questa guida introduttiva si apprenderà come usare PowerShell per inviare un invito a un utente esterno di Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50c283122fe707e922275b6c1a6c576009964855
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273365"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Guida introduttiva: Aggiungere un utente guest con PowerShell

Esistono diversi modi per invitare partner esterni ad accedere alle app e ai servizi della propria organizzazione con Collaborazione B2B di Azure Active Directory. Nella precedente guida introduttiva si è appreso come aggiungere utenti guest direttamente nel portale di amministrazione di Azure Active Directory. È anche possibile usare PowerShell per aggiungere utenti guest, uno alla volta o in blocco. In questa guida introduttiva si userà il comando New-AzureADMSInvitation per aggiungere un utente guest al tenant di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-the-latest-azureadpreview-module"></a>Installare il modulo AzureADPreview più recente
Verificare che sia installata la versione più recente del modulo di Azure AD PowerShell per Graph (AzureADPreview). 

Controllare innanzitutto quali moduli sono installati. Aprire Windows PowerShell come utente con privilegi elevati (Esegui come amministratore) ed eseguire il comando seguente:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Se il modulo AzureADPreview viene visualizzato senza alcun messaggio che indica la disponibilità di una versione più recente, procedere. In caso contrario, in base al contenuto del messaggio, eseguire una delle operazioni seguenti:

- Se non viene restituito alcun risultato, eseguire il comando seguente per installare il modulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se nei risultati viene visualizzato solo il modulo AzureAD, eseguire i comandi seguenti per installare il modulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se nei risultati viene visualizzato solo il modulo AzureADPreview, ma si riceve un messaggio che indica che è disponibile una versione più recente, eseguire i comandi seguenti per aggiornare il modulo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Potrebbe essere visualizzato un prompt che indica che si sta installando il modulo da un repository non attendibile. Ciò si verifica se il repository PSGallery non è stato precedentemente impostato come repository attendibile. Premere **Y** per installare il modulo.

### <a name="get-a-test-email-account"></a>Ottenere un account di posta elettronica di test

È necessario un account di posta elettronica di test a cui inviare l'invito. L'account deve essere esterno all'organizzazione. È possibile usare qualsiasi tipo di account, incluso un account di social networking, come un indirizzo gmail.com o outlook.com.

## <a name="sign-in-to-your-tenant"></a>Accedere al tenant

Eseguire il comando seguente per connettersi al dominio del tenant:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Ad esempio: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Immettere le credenziali quando richiesto.

## <a name="send-an-invitation"></a>Inviare un invito

1. Per inviare un invito all'account di posta elettronica di test, eseguire il comando PowerShell seguente (sostituire **"Sanda"** e **sanda\@fabrikam.com** con il nome e l'indirizzo dell'account di posta elettronica di test): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. Il comando invia un invito all'indirizzo di posta elettronica specificato. Verificare l'output, che dovrebbe essere simile all'esempio seguente:

   ![Output di PowerShell che indica l'accettazione utente in sospeso](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Verificare che l'utente esista nella directory

1. Per verificare che l'utente invitato sia stato aggiunto ad Azure AD, eseguire il comando seguente:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Controllare l'output per verificare che sia elencato anche l'utente invitato, con il nome dell'entità utente nel formato *indirizzopostaelettronica*#EXT#\@*dominio*. Ad esempio, *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, in cui contoso.onmicrosoft.com è organizzazione da cui sono stati invitati gli inviti.

   ![Output di PowerShell che mostra l'utente guest aggiunto](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile eliminare l'account utente di test dalla directory. Eseguire il comando seguente per eliminare un account utente:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Ad esempio: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato invitato e aggiunto un singolo utente guest alla directory usando PowerShell. Successivamente si apprenderà come invitare utenti guest in blocco tramite PowerShell.

> [!div class="nextstepaction"]
> [Esercitazione: Invitare in blocco utenti di Collaborazione B2B di Azure AD](tutorial-bulk-invite.md)
