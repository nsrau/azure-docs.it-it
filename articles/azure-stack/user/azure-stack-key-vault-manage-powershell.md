---
title: Gestire Key Vault in Azure Stack usando PowerShell | Microsoft Docs
description: Informazioni su come gestire Key Vault in Azure Stack tramite PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 4ec662cd60e2ca8e5db76a3fda4c68df6f3164e4
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893682"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Gestire Key Vault in Azure Stack tramite PowerShell

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile gestire Key Vault in Azure Stack tramite PowerShell. Informazioni su come usare i cmdlet di PowerShell di insieme di credenziali delle chiavi per:

* Creare un insieme di credenziali delle chiavi.
* Store e gestire chiavi crittografiche e segreti.
* Autorizzare gli utenti o applicazioni per richiamare le operazioni nell'insieme di credenziali.

>[!NOTE]
>Le descritte i cmdlet di PowerShell di insieme di credenziali delle chiavi in questo articolo vengono forniti in Azure PowerShell SDK.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio Azure Key Vault.
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
* [Configurare l'ambiente PowerShell dell'utente di Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Abilitare la sottoscrizione di tenant per le operazioni di Key Vault

Prima di poter eseguire qualsiasi operazione su un insieme di credenziali delle chiavi, è necessario assicurarsi che la sottoscrizione di tenant è abilitata per le operazioni dell'insieme di credenziali. Per verificare che siano abilitate le operazioni dell'insieme di credenziali, eseguire il comando seguente:

```PowerShell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Output**

Se la sottoscrizione è abilitata per le operazioni dell'insieme di credenziali, l'output mostra "RegistrationState" sia "registrato" per tutti i tipi di risorse di un insieme di credenziali delle chiavi.

![Stato della registrazione dell'insieme di credenziali chiave](media/azure-stack-key-vault-manage-powershell/image1.png)

Se le operazioni dell'insieme di credenziali non sono abilitate, richiamare il comando seguente per registrare il servizio Key Vault nella sottoscrizione:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Output**

Se la registrazione ha esito positivo, viene restituito l'output seguente:

![Registrare](media/azure-stack-key-vault-manage-powershell/image2.png) quando si richiamano i comandi di insieme di credenziali delle chiavi, è possibile ottenere un errore, ad esempio "la sottoscrizione non è registrata per l'utilizzo dello spazio dei nomi 'Microsoft. keyvault'." Se si verifica un errore, verificare che sia stato abilitato il provider di risorse di Key Vault, seguire le istruzioni indicate in precedenza.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Prima di creare un insieme di credenziali delle chiavi, creare un gruppo di risorse in modo che tutte le risorse correlate per l'insieme di credenziali delle chiavi esiste in un gruppo di risorse. Usare il comando seguente per creare un nuovo gruppo di risorse:

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force

```

**Output**

![Nuovo gruppo di risorse](media/azure-stack-key-vault-manage-powershell/image3.png)

A questo punto, usare il **New-AzureRMKeyVault** comando per creare un insieme di credenziali delle chiavi nel gruppo di risorse creato in precedenza. Questo comando legge i tre parametri obbligatori: nome del gruppo di risorse, nome del key vault e area geografica.

Eseguire il comando seguente per creare un insieme di credenziali delle chiavi:

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Output**

![Insieme di credenziali delle chiavi nuovo](media/azure-stack-key-vault-manage-powershell/image4.png)

L'output di questo comando Mostra le proprietà dell'insieme di credenziali chiave che è stato creato. Quando un'applicazione accede a questo insieme di credenziali, è necessario usare il **URI dell'insieme di credenziali** proprietà, ovvero "https://vault01.vault.local.azurestack.external" in questo esempio.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Distribuzione di Active Directory Federation Services (ADFS)

In una distribuzione di AD FS, è possibile ottenere questo avviso: "I criteri di accesso non sono impostato. Nessun utente o l'applicazione ha l'autorizzazione di accesso per usare questo insieme di credenziali". Per risolvere questo problema, impostare criteri di accesso per l'insieme di credenziali usando il [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gestire chiavi e segreti

Dopo aver creato un insieme di credenziali, usare la procedura seguente per creare e gestire chiavi e segreti nell'insieme di credenziali.

### <a name="create-a-key"></a>Creare una chiave

Usare la **Add-AzureKeyVaultKey** comando per creare o importare una chiave protetta tramite software in un insieme di credenziali delle chiavi.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Il **destinazione** parametro viene utilizzato per specificare che la chiave software protetto. Quando la chiave viene creata correttamente, il comando restituisce i dettagli della chiave creata.

**Output**

![Nuova chiave](media/azure-stack-key-vault-manage-powershell/image5.png)

È ora possibile fare riferimento la chiave creata utilizzando il relativo URI. Se si crea o si importa una chiave con stesso nome di una chiave esistente, la chiave originale viene aggiornata con i valori specificati nella chiave del nuovo. È possibile accedere alla versione precedente usando l'URI specifico della versione della chiave. Ad esempio: 

* Usare "https://vault10.vault.local.azurestack.external:443/keys/key01" per scaricare sempre la versione corrente.
* Usare "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" per ottenere questa versione specifica.

### <a name="get-a-key"></a>Ottenere una chiave

Usare la **Get-AzureKeyVaultKey** comandi per leggere una chiave e i relativi dettagli.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Creare un segreto

Usare la **Set-AzureKeyVaultSecret** comando per creare o aggiornare un segreto in un insieme di credenziali. Se non ne esiste già, viene creata una chiave privata. Se esiste già, viene creata una nuova versione del segreto.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Output**

![Creare un segreto](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Ottenere una chiave privata

Usare la **Get-AzureKeyVaultSecret** comando per leggere un segreto in un insieme di credenziali delle chiavi. Questo comando può restituire tutte le versioni specifiche di un segreto o.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Dopo aver creato le chiavi e segreti, è possibile autorizzare applicazioni esterne per usarli.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizzare un'applicazione a usare una chiave o segreto

Usare la **Set-AzureRmKeyVaultAccessPolicy** comando autorizzare un'applicazione di accedere a una chiave o segreto nell'insieme di credenziali chiave.
Nell'esempio seguente, è il nome dell'insieme di credenziali *ContosoKeyVault* e l'applicazione che si desidera autorizzare ha un ID client *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Per autorizzare l'applicazione, eseguire il comando seguente. Facoltativamente, è possibile specificare il **PermissionsToKeys** per impostare le autorizzazioni per un utente, applicazione o un gruppo di sicurezza.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se si desidera autorizzare la stessa applicazione di leggere i segreti nell'insieme di credenziali, eseguire il cmdlet seguente:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una macchina virtuale con una password archiviata in Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuire una macchina virtuale con un certificato archiviato in Key Vault](azure-stack-kv-push-secret-into-vm.md)
