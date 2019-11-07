---
title: Concedere alle applicazioni l'autorizzazione ad accedere a un insieme di credenziali delle chiavi di Azure - Azure Key Vault | Microsoft Docs
description: Informazioni su come concedere a molte applicazioni l'autorizzazione per accedere a Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c31c04137a8d36adfe41a18cbc276a45483b05b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467168"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Fornire un'autenticazione di Key Vault con un criterio di controllo di accesso

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il modo più semplice per autenticare un'applicazione basata sul cloud in Key Vault consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Usare un'identità gestita del Servizio app di Azure per accedere ad Azure Key Vault](managed-identity.md).  Se si crea un'applicazione locale, si sviluppa in locale o se non è possibile usare un'identità gestita per altri motivi, si può provare a registrare manualmente un'entità servizio e fornire l'accesso all'insieme di credenziali delle chiavi usando un criterio di controllo di accesso.  

L'insieme di credenziali delle chiavi supporta fino a 1024 voci di criteri di accesso, con ogni voce che concede un set di autorizzazioni distinte a un'"entità di sicurezza":   Ad esempio, questa è la modalità usata dall'app console in [Avvio rapido: Libreria client di Azure Key Vault per .NET](quick-create-net.md) per accedere all'insieme di credenziali delle chiavi.

Per i dettagli completi sul controllo di accesso di Key Vault, vedere [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](overview-security.md#identity-and-access-management). Per i dettagli completi su [Chiavi, segreti e certificati](about-keys-secrets-and-certificates.md) per il controllo di accesso, vedere: 

- [Controllo di accesso per le chiavi](about-keys-secrets-and-certificates.md#key-access-control)
- [Controllo di accesso per i segreti](about-keys-secrets-and-certificates.md#secret-access-control)
- [Controllo di accesso per i certificati](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un insieme di credenziali delle chiavi. È possibile usare un insieme di credenziali delle chiavi esistente o crearne uno nuovo seguendo le procedure illustrate in una di queste guide di avvio rapido:
   - [Creare un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
   - [Creare un insieme di credenziali delle chiavi con Azure PowerShell](quick-create-powershell.md)
   - [Creare un insieme di credenziali delle chiavi con il portale di Azure](quick-create-portal.md).
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/overview). In alternativa, è possibile usare il [portale di Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Ogni voce dei criteri di accesso per l'insieme di credenziali delle chiavi concede un set distinto di autorizzazioni a un'entità di sicurezza:

- **Un'applicazione** Se l'applicazione è basata sul cloud, è necessario [Usare un'identità gestita per accedere ad Azure Key Vault](managed-identity.md), se possibile
- **Un gruppo di Azure AD** Sebbene l'insieme di credenziali delle chiavi supporti fino a 1024 voci dei criteri di accesso, è possibile aggiungere più applicazioni e utenti a un singolo gruppo di Azure AD e quindi aggiungere il gruppo come singola voce ai criteri di controllo di accesso.
- **Un utente** È **sconsigliabile** concedere agli utenti l'accesso diretto a un insieme di credenziali delle chiavi. È opportuno aggiungere gli utenti a un gruppo di Azure AD, a cui viene a sua volta concesso l'accesso all'insieme di credenziali delle chiavi. Vedere [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Ottenere l'objectID

Per concedere l'accesso all'insieme di credenziali delle chiavi a un'applicazione, a un gruppo di Azure AD o a un utente, è prima necessario ottenere il relativo objectId.

#### <a name="applications"></a>APPLICAZIONI

L'objectId per un'applicazione corrisponde all'entità servizio ad essa associata. Per informazioni dettagliate sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Esistono due modi per ottenere un objectId per un'applicazione.  Il primo consiste nel registrare l'applicazione con Azure Active Directory. A tale scopo, seguire la procedura illustrata nella guida di avvio rapido [Registrare un'applicazione con Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Al termine della registrazione, l'objectId verrà mostrato come "ID applicazione (client)".

Il secondo modo prevede la creazione di un'entità servizio in una finestra del terminale. Con l'interfaccia della riga di comando di Azure, usare il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

L'objectId verrà mostrato nell'output come `clientID`.

Con Azure PowerShell, usare il cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0).


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

L'objectId verrà mostrato nell'output come `Id` (non `ApplicationId`).

#### <a name="azure-ad-groups"></a>Gruppi di Azure AD

È possibile aggiungere più applicazioni e utenti a un gruppo di Azure AD e quindi concedere al gruppo l'accesso all'insieme di credenziali delle chiavi.  Per informazioni dettagliate, vedere la sezione [Creazione e aggiunta di membri a un gruppo di Azure AD](#creating-and-adding-members-to-an-azure-ad-group) più avanti.

Per trovare l'objectId di un gruppo di Azure AD con l'interfaccia della riga di comando di Azure, usare il comando [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list). Poiché il numero di gruppi all'interno dell'organizzazione potrebbe essere elevato, specificare anche una stringa di ricerca nel parametro `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
L'objectId verrà restituito nel codice JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Per trovare l'objectId di un gruppo di Azure AD con Azure PowerShell, usare il cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0). Poiché il numero di gruppi all'interno dell'organizzazione potrebbe essere elevato, è consigliabile specificare anche una stringa di ricerca nel parametro `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Nell'output l'objectId sarà visualizzato come `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Utenti

È anche possibile aggiungere un singolo utente ai criteri di controllo di accesso dell'insieme di credenziali delle chiavi. **Questa procedura è sconsigliata**. È opportuno aggiungere gli utenti a un gruppo di Azure AD e quindi aggiungere il gruppo ai criteri.

Tuttavia, se si vuole trovare un utente con l'interfaccia della riga di comando di Azure, usare il comando [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show), passando l'indirizzo di posta elettronica dell'utente al parametro `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

L'objectId dell'utente verrà restituito nell'output:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Per trovare un utente con Azure PowerShell, usare il cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0), passando l'indirizzo di posta elettronica dell'utente al parametro `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

L'objectId dell'utente verrà restituito nell'output come `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Concedere all'entità di sicurezza l'accesso all'insieme di credenziali delle chiavi

Ora che si dispone dell'objectId dell'entità di sicurezza, è possibile creare un criterio di accesso per l'insieme di credenziali delle chiavi al fine di concedere all'entità le autorizzazioni di recupero, elenco, impostazione ed eliminazione per chiavi e segreti e le eventuali autorizzazioni aggiuntive desiderate.

Con l'interfaccia della riga di comando di Azure, passare l'objectId al comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Con Azure PowerShell, passare l'objectId al cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Creazione e aggiunta di membri a un gruppo di Azure AD

È possibile creare un gruppo di Azure AD, aggiungere applicazioni e utenti al gruppo e quindi concedere al gruppo l'accesso all'insieme di credenziali delle chiavi.  Questo consente di aggiungere a un insieme di credenziali delle chiavi più applicazioni come una singola voce di criterio di accesso eliminando così la necessità di concedere agli utenti l'accesso diretto all'insieme di credenziali delle chiavi (procedura sconsigliata). Per informazioni dettagliate, vedere [Gestire l'accesso ad app e risorse tramite i gruppi di Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Prerequisiti aggiuntivi

Oltre ai [prerequisiti precedenti](#prerequisites), saranno necessarie le autorizzazioni per la creazione/modifica dei gruppi nel tenant di Azure Active Directory. Se non si dispone delle autorizzazioni, può essere necessario contattare l'amministratore di Azure Active Directory.

Se si intende usare PowerShell, sarà inoltre necessario il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Creare un gruppo di Azure Active Directory

Creare un nuovo gruppo di Azure Active Directory usando il comando [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) dell'interfaccia della riga di comando di Azure o il cmdlet [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) di Azure PowerShell.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

In entrambi i casi, prendere nota del GroupId dei gruppi appena creati, in quanto sarà necessario per le procedure successive.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Individuare gli objectID di applicazioni e utenti

È possibile trovare gli objectID delle applicazioni usando l'interfaccia della riga di comando di Azure con il comando [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) e il parametro `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

È anche possibile trovare gli objectID delle applicazioni usando Azure PowerShell con il cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0), passando una stringa di ricerca al parametro `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Per trovare gli objectID degli utenti, seguire la procedura illustrata nella sezione [Utenti](#users) precedente.

### <a name="add-your-applications-and-users-to-the-group"></a>Aggiungere le applicazioni e gli utenti al gruppo

A questo punto, aggiungere gli objectID al gruppo di Azure AD appena creato.

Con l'interfaccia della riga di comando di Azure, usare il comando [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), passando l'elemento objectId al parametro `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Con Azure PowerShell, usare il cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0), passando l'elemento objectId al parametro `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Concedere al gruppo di Azure AD l'accesso all'insieme di credenziali delle chiavi

Infine, concedere al gruppo di Azure AD le autorizzazioni per l'insieme di credenziali delle chiavi usando il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure o il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) di Azure PowerShell. Per gli esempi, vedere la sezione [Concedere all'applicazione, al gruppo di Azure AD o all'utente l'accesso all'insieme di credenziali delle chiavi](#give-the-principal-access-to-your-key-vault) precedente.


## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](overview-security.md#identity-and-access-management)
- [Fornire l'autenticazione di Azure Key Vault con identità gestita dal Servizio app](managed-identity.md)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md).
- [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)
