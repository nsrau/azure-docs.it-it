---
title: Esercitazione per invitare in blocco utenti di Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: In questa esercitazione si apprenderà come usare PowerShell e un file CSV per inviare inviti in blocco a utenti esterni di Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 897fa353061d5b3e59f5613ea46e47afba0e3efa
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337800"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Esercitazione: Invitare in blocco utenti di Collaborazione B2B di Azure AD

Se si usa Collaborazione B2B di Azure Active Directory (Azure AD) per collaborare con partner esterni, è possibile invitare nell'organizzazione più utenti guest contemporaneamente. In questa esercitazione si apprenderà come usare PowerShell per inviare inviti in blocco a utenti esterni. In particolare, si eseguiranno le operazioni seguenti:

> [!div class="checklist"]
> * Preparare un file con valori delimitati da virgole (CSV) con le informazioni utente
> * Eseguire uno script PowerShell per inviare gli inviti
> * Verificare che gli utenti siano stati aggiunti alla directory

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-the-latest-azureadpreview-module"></a>Installare il modulo AzureADPreview più recente
Verificare che sia installata la versione più recente del modulo di Azure AD PowerShell per Graph (AzureADPreview). 

Controllare innanzitutto quali moduli sono installati. Aprire Windows PowerShell come utente con privilegi elevati (Esegui come amministratore) ed eseguire il comando seguente:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Eseguire una delle operazioni seguenti in base all'output:

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

### <a name="get-test-email-accounts"></a>Ottenere account di posta elettronica di test

Sono necessari almeno due account di posta elettronica di test a cui inviare gli inviti. Gli account devono essere esterni all'organizzazione. È possibile usare qualsiasi tipo di account, inclusi gli account di social networking, come gli indirizzi gmail.com o outlook.com.

## <a name="prepare-the-csv-file"></a>Preparare il file CSV

In Microsoft Excel creare un file CSV con l'elenco dei nomi utente e degli indirizzi di posta elettronica degli invitati. Assicurarsi di includere le intestazioni di colonna **Name** e **InvitedUserEmailAddress**. 

Ad esempio, creare un foglio di lavoro nel formato seguente:


![Output di PowerShell che indica l'accettazione utente in sospeso](media/tutorial-bulk-invite/AddUsersExcel.png)

Salvare il file come **C:\BulkInvite\Invitations.csv**. 

Se non è disponibile Excel, è possibile creare un file CSV con qualsiasi editor di testo, come ad esempio il Blocco note. Separare ogni valore con una virgola e ogni riga con una nuova riga. 

## <a name="sign-in-to-your-tenant"></a>Accedere al tenant

Eseguire il comando seguente per connettersi al dominio del tenant:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Ad esempio: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Immettere le credenziali quando richiesto.

## <a name="send-bulk-invitations"></a>Inviare inviti in blocco

Per inviare gli inviti, eseguire lo script PowerShell seguente (in cui **c:\bulkinvite\invitations.csv** è il percorso del file CSV): 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.azure.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
Lo script invia un invito agli indirizzi di posta elettronica presenti nel file Invitations.csv. L'output dovrebbe essere simile all'esempio seguente per ogni utente:

![Output di PowerShell che indica l'accettazione utente in sospeso](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Verificare che gli utenti esistano nella directory

Per verificare che gli utenti invitati siano stati aggiunti ad Azure AD, eseguire il comando seguente:
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
Dovrebbero essere elencati gli utenti invitati, con il nome dell'entità utente nel formato *indirizzopostaelettronica*#EXT#\@*dominio*. Ad esempio *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, in cui contoso.onmicrosoft.com è l'organizzazione da cui sono stati inviati gli inviti.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare gli account utente di test dalla directory. Eseguire il comando seguente per eliminare un account utente:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Ad esempio: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati inviati inviti in blocco ad utenti guest all'esterno dell'organizzazione. Successivamente si apprenderà come funziona il processo di riscatto dell'invito.

> [!div class="nextstepaction"]
> [Informazioni sul processo di riscatto dell'invito di Collaborazione B2B di Azure AD](redemption-experience.md)

