---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere all'API Graph di Azure AD
description: Esercitazione che descrive in modo dettagliato l'uso di un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere all'API Graph di Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b0d9e4fad61195118c92c047340f2cd4d9e20f
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480686"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere all'API Graph di Azure AD

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Questa esercitazione descrive come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere all'API Graph di Microsoft in modo da recuperarne le appartenenze ai gruppi. Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice.  Per questa esercitazione si eseguiranno query sull'appartenenza dell'identità della macchina virtuale in gruppi di Azure AD. Le informazioni sui gruppi vengono spesso usate, ad esempio, nelle decisioni per l'autorizzazione. In background, l'identità gestita della macchina virtuale è rappresentata da un'**entità servizio** in Azure AD. Prima di eseguire la query di gruppo, aggiungere l'entità servizio che rappresenta l'identità della macchina virtuale a un gruppo in Azure AD. A questo scopo, è possibile usare Azure PowerShell, Azure AD PowerShell o l'interfaccia della riga di comando di Azure.

> [!div class="checklist"]
> * Connessione ad Azure AD
> * Aggiungere l'identità della macchina virtuale a un gruppo in Azure AD 
> * Concedere all'identità della macchina virtuale l'accesso ad Azure AD Graph 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure AD Graph

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Per concedere a un'identità della macchina virtuale l'accesso ad Azure AD Graph, all'account deve essere assegnato il ruolo di **amministratore globale** in Azure AD.
- Installare la [versione più recente di Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), se non è già installata. 

## <a name="connect-to-azure-ad"></a>Connessione ad Azure AD

È necessario connettersi ad Azure AD per assegnare la macchina virtuale a un gruppo, nonché concedere alla macchina virtuale l'autorizzazione per recuperare le proprie appartenenze ai gruppi. È possibile usare il cmdlet Connect-AzureAD direttamente o con il parametro TenantId se sono presenti più tenant.

```powershell
Connect-AzureAD
```
Oppure
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Aggiungere l'identità della macchina virtuale a un gruppo in Azure AD

Quando è stata abilitata l'identità gestita assegnata dal sistema nella macchina virtuale Windows, è stata creata un'entità servizio in Azure AD.  È ora necessario aggiungere la macchina virtuale a un gruppo.  L'esempio seguente crea un nuovo gruppo in Azure AD e aggiunge l'entità servizio della macchina virtuale al gruppo:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Concedere alla macchina virtuale l'accesso all'API Graph di Azure AD

Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD. L'API Graph di Microsoft Azure AD supporta l'autenticazione di Azure AD. In questo passaggio si concederà all'entità servizio dell'identità della macchina virtuale l'accesso ad Azure AD Graph in modo da eseguire query sulle appartenenze ai gruppi. Alle entità servizio viene concesso l'accesso a Microsoft Graph o ad Azure AD Graph tramite **autorizzazioni dell'applicazione**. Il tipo di autorizzazione dell'applicazione che è necessario concedere dipende dall'entità cui si vuole accedere in Microsoft Graph o Azure AD Graph.

Per questa esercitazione, si concederà all'identità della macchina virtuale la possibilità di eseguire query sull'appartenenza ai gruppi usando l'autorizzazione dell'applicazione ```Directory.Read.All```. Per concedere questa autorizzazione, è necessario un account utente cui sia assegnato il ruolo di amministratore globale di Azure AD. In genere si concede un'autorizzazione dell'applicazione visitando la registrazione dell'applicazione nel portale di Azure e aggiungendo l'autorizzazione da qui. Tuttavia, le identità gestite per le risorse di Azure non registrano oggetti dell'applicazione in Azure AD, ma solo entità servizio. Per registrare l'autorizzazione dell'applicazione, è necessario usare lo strumento della riga di comando Azure AD PowerShell. 

Azure AD Graph:
- ID app dell'entità servizio (usato quando si concede l'autorizzazione dell'app): 00000002-0000-0000-c000-000000000000
- ID risorsa (usato quando si richiede il token di accesso da identità gestite per risorse di Azure): https://graph.windows.net
- Riferimento all'ambito di autorizzazione: [riferimento alle autorizzazioni di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Concedere autorizzazioni dell'applicazione tramite Azure AD PowerShell

Per eseguire questa procedura, è necessario Azure AD PowerShell. Se non è già stato installato, [scaricare la versione più recente](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) prima di continuare.

1. Aprire una finestra di PowerShell e connettersi ad Azure AD:

   ```powershell
   Connect-AzureAD
   ```
   Per connettersi a un ambiente Azure Active Directory specifico, usare il parametro _TenantId_ come illustrato di seguito:

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Eseguire i comandi di PowerShell seguenti per assegnare l'autorizzazione dell'applicazione ``Directory.Read.All`` all'entità servizio che rappresenta l'identità della macchina virtuale.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   L'output del comando finale dovrebbe essere simile al seguente, restituendo l'ID dell'assegnazione:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Se la chiamata a `New-AzureAdServiceAppRoleAssignment` non riesce con l'errore `bad request, one or more properties are invalid`, l'autorizzazione dell'app potrebbe essere già assegnata all'entità servizio dell'identità della macchina virtuale. È possibile usare i comandi di PowerShell seguenti per verificare se l'autorizzazione dell'applicazione esiste già tra l'identità della macchina virtuale e Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   È possibile usare i comandi di PowerShell seguenti per elencare tutte le autorizzazioni dell'app concesse per Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   È possibile usare i comandi di PowerShell seguenti per rimuovere le autorizzazioni dell'app concesse all'identità della macchina virtuale per Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Ottenere un token di accesso usando l'identità della macchina virtuale per chiamare Azure AD Graph 

Per usare l'identità gestita assegnata dal sistema della macchina virtuale per l'autenticazione in Azure AD Graph, è necessario effettuare richieste dalla macchina virtuale.

1. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e nel pannello **Panoramica** fare clic su **Connetti**.  
2. Nei campi **Nome utente** e **Password** immettere i valori usati al momento della creazione della macchina virtuale Windows.
3. Ora che è stata creata una connessione Desktop remoto alla macchina virtuale, aprire PowerShell nella sessione remota.  
4. Usando Invoke-WebRequest di PowerShell, effettuare una richiesta all'identità gestita locale per l'endpoint delle risorse di Azure per ottenere un token di accesso per Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Convertire la risposta da un oggetto JSON a un oggetto di PowerShell.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Estrarre il token di accesso dalla risposta.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando l'ID oggetto dell'entità servizio della macchina virtuale (recuperare questo valore dalla variabile dichiarata nei passaggi precedenti: ``$ManagedIdentitiesServicePrincipal.ObjectId``), è possibile eseguire query sull'API Graph di Azure AD per recuperare le appartenenze ai gruppi. Sostituire <OBJECT ID> con l'ID oggetto dal passaggio precedente e <ACCESS-TOKEN> con il token di accesso ottenuto prima:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   La risposta contiene il valore di `Object ID` del gruppo cui è stata aggiunta l'entità servizio della macchina virtuale nei passaggi precedenti.

   Risposta:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure AD Graph.  Per altre informazioni su Azure AD Graph, vedere:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
