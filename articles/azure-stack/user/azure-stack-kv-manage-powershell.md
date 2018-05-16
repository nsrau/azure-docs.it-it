---
title: Gestire l'insieme di credenziali chiave nello Stack di Azure usando PowerShell | Documenti Microsoft
description: Informazioni su come gestire l'insieme di credenziali chiave nello Stack di Azure tramite PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5e9de401f64a835c286c226bfac88caf5168b96e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Gestire l'insieme di credenziali chiave nello Stack di Azure tramite PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile gestire l'insieme di credenziali chiave nello Stack di Azure tramite PowerShell. Imparare a utilizzare i cmdlet di PowerShell nell'insieme di credenziali chiave per:

* Creare un insieme di credenziali delle chiavi.
* Archiviare e gestire i segreti e le chiavi di crittografia.
* Autorizzare gli utenti o applicazioni per richiamare le operazioni nell'insieme di credenziali.

>[!NOTE]
>Il descritti i cmdlet PowerShell dell'insieme di credenziali chiave in questo articolo vengono fornite in Azure PowerShell SDK.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio insieme credenziali chiavi Azure.
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Abilitare la sottoscrizione di tenant per le operazioni di insieme di credenziali chiave

Prima di poter eseguire qualsiasi operazione su un insieme di credenziali chiave, è necessario verificare che la sottoscrizione di tenant sia abilitata per le operazioni dell'insieme di credenziali. Per verificare che siano abilitate le operazioni dell'insieme di credenziali, eseguire il comando seguente:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Output**

Se la sottoscrizione è abilitata per le operazioni dell'insieme di credenziali, l'output mostra "RegistrationState" sia "registrata" per tutti i tipi di risorse di un insieme di credenziali delle chiavi.

![Stato della registrazione dell'insieme di credenziali chiave](media/azure-stack-kv-manage-powershell/image1.png)

Se non sono abilitate le operazioni dell'insieme di credenziali, è possibile richiamare il comando seguente per registrare il servizio insieme di credenziali chiave nella sottoscrizione:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Output**

Se la registrazione ha esito positivo, viene restituito il seguente output:

![Registrare](media/azure-stack-kv-manage-powershell/image2.png) quando si richiamano i comandi dell'insieme di credenziali chiave, si verifichi un errore, ad esempio "la sottoscrizione non è registrata per l'utilizzo dello spazio dei nomi 'Microsoft.KeyVault'." Se si verifica un errore, verificare di avere [abilitato il provider di risorse dell'insieme di credenziali chiave](#enable-your-tenant-subscription-for-vault-operations) seguendo le istruzioni indicate in precedenza.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Prima di creare un insieme di credenziali chiave, creare un gruppo di risorse in modo che tutte le risorse correlate all'insieme di credenziali chiave esiste in un gruppo di risorse. Utilizzare il comando seguente per creare un nuovo gruppo di risorse:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Output**

![nuovo gruppo di risorse](media/azure-stack-kv-manage-powershell/image3.png)

A questo punto, usare il **New AzureRMKeyVault** comando per creare un insieme di credenziali chiave nel gruppo di risorse creato in precedenza. Questo comando legge tre parametri obbligatori: nome del gruppo di risorse, il nome dell'insieme di credenziali chiave e posizione geografica.

Eseguire il comando seguente per creare un insieme di credenziali chiave:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```

**Output**

![Insieme di credenziali delle chiavi nuovo](media/azure-stack-kv-manage-powershell/image4.png)

L'output di questo comando Mostra le proprietà dell'insieme di credenziali chiave che è stato creato. Quando un'applicazione accede a questo insieme di credenziali, è necessario utilizzare il **URI dell'insieme di credenziali** proprietà, ovvero "https://vault01.vault.local.azurestack.external" in questo esempio.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Distribuzione di Active Directory Federation Services (ADFS)

In una distribuzione di ADFS, è possibile che venga visualizzato questo avviso: "criteri di accesso non sono impostato. Nessun utente o l'applicazione dispone di autorizzazioni di accesso per utilizzare questo insieme di credenziali." Per risolvere questo problema, impostare un criterio di accesso per l'insieme di credenziali usando il [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gestire le chiavi e segreti

Dopo aver creato un insieme di credenziali, utilizzare la procedura seguente per creare e gestire le chiavi e segreti nell'insieme di credenziali.

### <a name="create-a-key"></a>Creare una chiave

Utilizzare il **Add-AzureKeyVaultKey** comando per creare o importare una chiave protetta tramite software in un insieme di credenziali chiave.

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```

Il **destinazione** parametro viene utilizzato per specificare che la chiave sia protetto da software. Quando la chiave viene creata correttamente, il comando restituisce i dettagli della chiave creata.

**Output**

![Nuova chiave](media/azure-stack-kv-manage-powershell/image5.png)

È ora possibile fare riferimento la chiave creata utilizzando il relativo URI. Se si crea o si importa una chiave con stesso nome di una chiave esistente, la chiave originale viene aggiornata con i valori specificati in una nuova chiave. È possibile accedere alla versione precedente utilizzando l'URI specifico della versione della chiave. Ad esempio: 

* Utilizzare "https://vault10.vault.local.azurestack.external:443/keys/key01" ottenere sempre la versione corrente.
* Utilizzare "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" per ottenere la versione specifica.

### <a name="get-a-key"></a>Ottenere una chiave

Utilizzare il **Get-AzureKeyVaultKey** comando per la lettura di una chiave e i relativi dettagli.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Creare un segreto

Utilizzare il **Set AzureKeyVaultSecret** comando per creare o aggiornare una chiave privata in un insieme di credenziali. Se non esiste già, viene creata una chiave privata. Se esiste già, viene creata una nuova versione del segreto.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Output**

![Creare un segreto](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Ottenere una chiave privata

Utilizzare il **Get AzureKeyVaultSecret** comando per la lettura di un segreto in un insieme di credenziali chiave. Questo comando può restituire tutte le versioni specifiche di un segreto o.

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Dopo aver creato le chiavi e segreti, è possibile autorizzare le applicazioni esterne per utilizzarli.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizzare un'applicazione può utilizzare una chiave o un segreto

Utilizzare il **Set-AzureRmKeyVaultAccessPolicy** comando autorizzare un'applicazione di accedere a una chiave o un segreto nell'insieme di credenziali chiave.
Nell'esempio seguente, il nome dell'insieme di credenziali è *ContosoKeyVault* e l'applicazione che si desidera autorizzare ha un ID client *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Per autorizzare l'applicazione, eseguire il comando seguente. Facoltativamente, è possibile specificare il **PermissionsToKeys** per impostare le autorizzazioni per un utente, applicazione o un gruppo di sicurezza.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se si desidera autorizzare la stessa applicazione per leggere i segreti nell'insieme di credenziali, eseguire il cmdlet seguente:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una macchina virtuale con una password archiviate nell'insieme di credenziali chiave](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuire una macchina virtuale con un certificato archiviato nell'insieme di credenziali chiave](azure-stack-kv-push-secret-into-vm.md)
