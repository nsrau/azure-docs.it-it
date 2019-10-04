---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)
description: Questo articolo illustra i prerequisiti per l'uso di Crittografia dischi di Azure per le macchine virtuali IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: be709fcbb45c95f092b24b53fd0c506187fcd8b3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828540"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)

**La nuova versione di Crittografia dischi di Azure elimina la necessità di specificare un parametro dell'applicazione Azure AD per abilitare la crittografia dei dischi per le macchine virtuali. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD. Per visualizzare le istruzioni per abilitare la crittografia del disco della macchina virtuale con la nuova versione, vedere [crittografia dischi di Azure](disk-encryption-overview.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.**

Crittografia dischi di Azure usa Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../../key-vault/key-vault-secure-your-key-vault.md). 

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'uso con crittografia dischi di Azure con Azure AD (versione precedente) prevede tre passaggi:

1. Creare un insieme di credenziali delle chiavi. 
2. Configurare un'applicazione Azure AD e un'entità servizio.
3. Configurare i criteri di accesso per l'insieme di credenziali delle chiavi per l'app Azure AD.
4. Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi.
 
È anche possibile generare o importare una chiave di crittografia della chiave (KEK).

Per informazioni su come [installare gli strumenti e connettersi ad Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure), vedere l'articolo principale sulla [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md) .

> [!Note]
> I passaggi descritti in questo articolo sono automatizzati nello script dell'interfaccia della riga di comando dei [prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started) e nello script di PowerShell per i [prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi 
La soluzione Crittografia dischi di Azure è integrata con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi e dei segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. È possibile creare un insieme di credenziali delle chiavi o usarne uno esistente per Crittografia dischi di Azure. Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../../key-vault/key-vault-secure-your-key-vault.md). Per creare un insieme di credenziali delle chiavi è possibile usare un modello di Resource Manager, Azure PowerShell o l'interfaccia della riga di comando di Azure. 


>[!WARNING]
>Per assicurarsi che i segreti di crittografia non superino i confini a livello di area, Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare. 


### <a name="bkmk_KVPSH"></a> Creare un insieme di credenziali delle chiavi con PowerShell

È possibile creare un insieme di credenziali delle chiavi con Azure PowerShell usando il cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Per ulteriori cmdlet per Key Vault, vedere [AZ.](/powershell/module/az.keyvault/)insieme di credenziali. 

1. Creare un nuovo gruppo di risorse, se necessario, con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Per elencare data center percorsi, usare [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Creare un nuovo insieme di credenziali delle chiavi con [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Prendere nota dei valori restituiti per **Nome dell'insieme di credenziali**, **Nome del gruppo di risorse**, **ID risorsa**, **URI dell'insieme di credenziali**e **ID oggetto** per l'uso successivo, quando si crittograferanno i dischi. 


### <a name="bkmk_KVCLI"></a> Creare un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure
È possibile gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure usando i comandi [az keyvault](/cli/azure/keyvault#commands). Per creare un insieme di credenziali delle chiavi, usare [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. Se necessario, creare un nuovo gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). Per un elenco delle posizioni, usare [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Creare un nuovo insieme di credenziali delle chiavi usando [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Prendere nota dei valori restituiti per **Nome dell'insieme di credenziali** (nome), **Nome del gruppo di risorse**, **ID risorsa**, (ID), **URI dell'insieme di credenziali** e **ID oggetto** per l'uso successivo. 

### <a name="bkmk_KVRM"></a> Creare un insieme di credenziali delle chiavi con un modello di Resource Manager

È possibile creare un insieme di credenziali delle chiavi usando il [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Nel modello di avvio rapido di Azure fare clic su **Distribuisci in Azure**.
2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, il nome dell'insieme di credenziali delle chiavi, l'ID oggetto, le note legali e il contratto, quindi fare clic su **Acquista**. 


## <a name="bkmk_ADapp"></a> Configurare un'app Azure AD e un'entità servizio 
Quando è necessario abilitare la crittografia in una macchina virtuale in esecuzione in Azure, Crittografia dischi di Azure genera e scrive le chiavi di crittografia nell'insieme di credenziali delle chiavi. La gestione delle chiavi di crittografia nell'insieme di credenziali delle chiavi richiede l'autenticazione di Azure AD. Creare un'applicazione Azure AD per questo scopo. Ai fini dell'autenticazione, è possibile usare l'autenticazione basata sul segreto client o l'[autenticazione di Azure AD basata sul certificato client](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Configurare un'app Azure AD e un'entità servizio con Azure PowerShell 
Per eseguire i comandi seguenti, recuperare e usare il [modulo di Azure AD per PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Usare il cmdlet di PowerShell [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) per creare un'applicazione Azure ad. MyApplicationHomePage e MyApplicationUri possono essere qualsiasi valore desiderato.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId è l'ID client di Azure AD e $aadClientSecret è il segreto client da usare in seguito per abilitare Crittografia dischi di Azure. Proteggere correttamente il segreto client di Azure AD. Eseguendo `$azureAdApplication.ApplicationId` verrà visualizzato l'ID applicazione.


### <a name="bkmk_ADappCLI"></a> Configurare un'app Azure AD e un'entità servizio con l'interfaccia della riga di comando di Azure

È possibile gestire le entità servizio con l'interfaccia della riga di comando di Azure usando i comandi [az ad sp](/cli/azure/ad/sp). Per altre informazioni, vedere [creare un'entità servizio di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Creare una nuova entità servizio.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Il valore appId restituito è l'ID client di Azure AD usato in altri comandi. È anche il nome SPN che verrà usato per az keyvault set-policy. La password è il segreto client da usare in seguito per abilitare Crittografia dischi di Azure. Proteggere correttamente il segreto client di Azure AD.
 
### <a name="bkmk_ADappRM"></a> Configurare un'app Azure AD e un'entità servizio tramite il portale di Azure
Seguire i passaggi illustrati nell'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md) per creare un'applicazione Azure AD. Ognuno dei passaggi elencati di seguito conduce direttamente alla sezione dell'articolo da completare. 

1. [Verificare le autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Creare un'applicazione Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Quando si crea l'applicazione è possibile usare qualsiasi nome e URL di accesso desiderato.
3. [Ottenere l'ID applicazione e la chiave di autenticazione](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - La chiave di autenticazione è il segreto client e viene usato come AadClientSecret per set-AzVMDiskEncryptionExtension. 
        - La chiave di autenticazione viene usata dall'applicazione come credenziale per accedere ad Azure AD. Nel portale di Azure il segreto è denominato chiave, ma non ha alcuna relazione con gli insiemi di credenziali delle chiavi. Proteggere il segreto in modo appropriato. 
     - L'ID applicazione verrà usato in un secondo momento come AadClientId per set-AzVMDiskEncryptionExtension e come ServicePrincipalName per set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Configurare i criteri di accesso per l'insieme di credenziali delle chiavi per l'app Azure AD
Per scrivere segreti di crittografia in un insieme di credenziali delle chiavi specificato, Crittografia dischi di Azure deve avere l'ID client e il Segreto client dell'applicazione Azure Active Directory che dispone delle autorizzazioni per scrivere segreti nell'insieme di credenziali delle chiavi. 

> [!NOTE]
> Crittografia dischi di Azure richiede la configurazione di criteri di accesso all'applicazione client di Azure AD, ovvero le autorizzazioni _WrapKey_ e _Set_.

### <a name="bkmk_KVAPPSH"></a> Configurare i criteri di accesso per l'insieme di credenziali delle chiavi per l'app Azure AD con Azure PowerShell
L'applicazione Azure AD deve avere i diritti di accesso alle chiavi o ai segreti nell'insieme di credenziali. Usare il cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per concedere le autorizzazioni all'applicazione, usando l'ID client, generato al momento della registrazione dell'applicazione, come valore del parametro _– servicePrincipalName_ . Per altre informazioni, vedere il post di blog [Azure Key Vault - Step by Step](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Procedura dettagliata per Azure Key Vault). 

1. Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione AD con PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'app Azure AD con l'interfaccia della riga di comando di Azure
Usare [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) per impostare i criteri di accesso. Per altre informazioni, vedere [Gestire Key Vault tramite l'interfaccia della riga di comando 2.0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Assegnare all'entità servizio creata tramite l'interfaccia della riga di comando di Azure l'accesso per ottenere i segreti ed eseguire il wrapping delle chiavi con il comando seguente:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'app Azure AD con il portale

1. Aprire il gruppo di risorse con l'insieme di credenziali delle chiavi.
2. Selezionare l'insieme di credenziali delle chiavi, passare a **Criteri di accesso**, quindi fare clic su **Aggiungi nuovo**.
3. In **Selezionare un'entità** cercare l'applicazione Azure AD creata e selezionarla. 
4. Per **Autorizzazioni chiave** selezionare **Esegui il wrapping della chiave** in **Operazioni crittografiche**.
5. Per **Autorizzazioni segrete** selezionare **Imposta** in **Operazioni di gestione dei segreti**.
6. Fare clic su **OK** per salvare il criterio di accesso. 

![Operazioni crittografiche di Azure Key Vault - Esegui il wrapping della chiave](./media/disk-encryption/keyvault-portal-fig3.png)

![Autorizzazioni segrete di Azure Key Vault - Imposta](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi
La piattaforma Azure deve avere accesso alle chiavi di crittografia o i segreti nell'insieme di credenziali delle chiavi per renderli disponibili alla macchina virtuale per l'avvio e la decrittografia dei volumi. Abilitare la crittografia del disco nell'insieme di credenziali delle chiavi o le distribuzioni avranno esito negativo.  

### <a name="bkmk_KVperPSH"></a> Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi con Azure PowerShell
 Usare il cmdlet PowerShell di Key Vault [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi.

  - **Abilitare Key Vault per la crittografia dischi:** EnabledForDiskEncryption è obbligatorio per Crittografia dischi di Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Abilitare Key Vault per la distribuzione, se necessario:** in questo modo si consente al provider di risorse Microsoft.Compute di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la creazione di risorse, ad esempio quando si crea una macchina virtuale.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Abilitare Key Vault per la distribuzione di modelli, se necessario:** in questo modo si consente a Azure Resource Manager di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la distribuzione di un modello.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure
Usare [az keyvault update](/cli/azure/keyvault#az-keyvault-update) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi. 

 - **Abilitare Key Vault per la crittografia dischi:** È richiesto Enabled-for-disk-encryption. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Abilitare Key Vault per la distribuzione, se necessario:** consente alle macchine virtuali di recuperare i certificati archiviati come segreti dall'insieme di credenziali.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Abilitare Key Vault per la distribuzione di modelli, se necessario:** consente a Resource Manager di recuperare i segreti dall'insieme di credenziali.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi tramite il portale di Azure

1. Selezionare l'insieme di credenziali delle chiavi, passare a **Criteri di accesso** e **Fare clic per visualizzare i criteri di accesso avanzati**.
2. Selezionare la casella **Abilita l'accesso a Crittografia dischi di Azure per la crittografia dei volumi**.
3. Selezionare **Abilita l'accesso alle macchine virtuali di Azure per la distribuzione** e/o **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**, se necessario. 
4. Fare clic su **Salva**.

![Criteri di accesso avanzati per l'insieme di credenziali delle chiavi di Azure](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurare una chiave di crittografia della chiave (facoltativo)
Se si vuole usare una chiave di crittografia della chiave (KEK) per un livello aggiuntivo di sicurezza per le chiavi di crittografia, aggiungere una KEK all'insieme di credenziali delle chiavi. Usare il cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) per creare una chiave di crittografia della chiave nell'insieme di credenziali delle chiavi. È anche possibile importare una chiave di crittografia della chiave dal modulo di protezione hardware di gestione delle chiavi locale. Per altre informazioni, vedere la [documentazione di Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. 

* Quando si generano chiavi, usare un tipo di chiave RSA. Crittografia dischi di Azure non supporta ancora l'uso di chiavi a curva ellittica.

* È necessario applicare il controllo delle versioni agli URL del segreto dell'insieme di credenziali delle chiavi e della chiave di crittografia della chiave. Azure applica questa restrizione relativa al controllo delle versioni. Per informazioni sugli URL del segreto e della chiave di crittografia della chiave validi, vedere gli esempi seguenti:

  * Esempio di URL del segreto valido:  *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Esempio di URL della chiave di crittografia della chiave valido:  *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Crittografia dischi di Azure non supporta la possibilità di specificare i numeri di porta come parte dei segreti dell'insieme di credenziali delle chiavi e degli URL della chiave di crittografia della chiave. Ecco alcuni esempi di URL di insiemi di credenziali delle chiavi non supportati e supportati:

  * URL dell'insieme di credenziali delle chiavi non accettabile:  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL dell'insieme di credenziali delle chiavi accettabile:  *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Configurare una chiave di crittografia della chiave con Azure PowerShell 
Prima di usare lo script di PowerShell, è necessario conoscere i prerequisiti di Crittografia dischi di Azure per comprendere i passaggi nello script. Lo script di esempio potrebbe richiedere modifiche per l'ambiente in uso. Questo script crea tutti i prerequisiti di Crittografia dischi di Azure e crittografa una macchina virtuale IaaS esistente, eseguendo il wrapping della chiave di crittografia del disco tramite una chiave di crittografia della chiave. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Autenticazione basata su certificati (facoltativo)
Se si vuole usare l'autenticazione basata su certificati, è possibile caricarne uno nell'insieme di credenziali delle chiavi e distribuirlo al client. Prima di usare lo script di PowerShell, è necessario conoscere i prerequisiti di Crittografia dischi di Azure per comprendere i passaggi nello script. Lo script di esempio potrebbe richiedere modifiche per l'ambiente in uso.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Autenticazione basata su certificati e KEK (facoltativo)

Se si vuole usare l'autenticazione basata su certificati ed eseguire il wrapping della chiave di crittografia con una KEK, si può usare lo script seguente come esempio. Prima di usare lo script di PowerShell, è necessario conoscere tutti i prerequisiti di Crittografia dischi di Azure precedenti per comprendere i passaggi nello script. Lo script di esempio potrebbe richiedere modifiche per l'ambiente in uso.

> [!IMPORTANT]
> L'autenticazione basata su certificato per Azure AD non è attualmente supportata nelle VM Linux.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Passaggi successivi

[Abilitare crittografia dischi di Azure con Azure AD nelle macchine virtuali Linux (versione precedente)](disk-encryption-linux-aad.md)
