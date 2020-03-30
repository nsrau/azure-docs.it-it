---
title: Configurare la propria chiave per la crittografia dei dati del bus di servizio di Azure inattiviConfigure your own key for encrypting Azure Service Bus data at rest
description: Questo articolo fornisce informazioni su come configurare una chiave personalizzata per crittografare il rest dei dati del bus di servizio di Azure.This article provides information on how to configure your own key for encrypting Azure Service Bus data rest.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624082"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi tramite il portale di AzureConfigure customer-managed keys for encrypting Azure Service Bus data at rest by using the Azure portal
Azure Service Bus Premium offre la crittografia dei dati inattivi con Azure Storage Service Encryption (Azure SSE). Service Bus Premium si basa su Archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft.Service Bus Premium relies on Azure Storage to store the data and by default, all the data that is stored with Azure Storage is encrypted using Microsoft-managed keys. 

## <a name="overview"></a>Panoramica
Il bus di servizio di Azure supporta ora l'opzione di crittografare i dati inattivi con chiavi gestite da Microsoft o con chiavi gestite dal cliente (Bring Your Own Key - BYOK). Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare il bus di servizio di Azure inattivi.

L'abilitazione della funzionalità BYOK è un processo di configurazione una tantera nello spazio dei nomi.

> [!NOTE]
> Sono presenti alcuni avvertimenti per la chiave gestita del cliente per la crittografia lato servizio. 
>   * Questa funzionalità è supportata dal livello Premium del bus di servizio di [Azure.This feature](service-bus-premium-messaging.md) is supported by Azure Service Bus Premium tier. Non può essere abilitato per gli spazi dei nomi del bus di servizio di livello standard.
>   * La crittografia può essere abilitata solo per spazi dei nomi nuovi o vuoti. Se lo spazio dei nomi contiene dati, l'operazione di crittografia avrà esito negativo.

È possibile usare l'insieme di credenziali delle chiavi di Azure per gestire le chiavi e controllare l'utilizzo delle chiavi. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../key-vault/key-vault-overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente tramite il portale di Azure.This article shows how to configure a key vault with customer-managed keys by using the Azure portal. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Un](../key-vault/quick-create-portal.md)insieme di credenziali delle chiavi di Azure usando il portale di Azure.To learn how to create a key vault using the Azure portal, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

> [!IMPORTANT]
> L'uso di chiavi gestite dal cliente con il bus di servizio di Azure richiede che nell'insieme di credenziali delle chiavi siano configurate due proprietà obbligatorie. Essi sono: **Eliminazione soft** e **non eliminare**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure.These properties are enabled by default when you create a new key vault in the Azure portal. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.However, if you need to enable these properties on an existing key vault, you must use either PowerShell or Azure CLI.

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys
Per abilitare le chiavi gestite dal cliente nel portale di Azure, eseguire la procedura seguente:To enable customer-managed keys in the Azure portal, follow these steps:

1. Passare allo spazio dei nomi Service Bus Premium.Navigate to your Service Bus Premium namespace.
2. Nella pagina **Impostazioni** dello spazio dei nomi del bus di servizio selezionare **Crittografia**.
3. Selezionare la crittografia della **chiave gestita dal cliente inattivi** come illustrato nell'immagine seguente.

    ![Abilita chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con chiaviSet up a key vault with keys

Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita del cliente allo spazio dei nomi del bus di servizio di Azure.After you enable customer-managed keys, you need to associate the customer managed key with your Azure Service Bus namespace. Il bus di servizio supporta solo l'insieme di credenziali delle chiavi di Azure.Service Bus supports only Azure Key Vault. Se si abilita l'opzione Crittografia con chiave gestita dal cliente nella sezione precedente, è necessario che la chiave importi nell'insieme di credenziali delle chiavi di Azure.If you enable the **Encryption with customer-managed key option** in the previous section, you need to have the key imported into Azure Key Vault. Inoltre, le chiavi devono avere **Soft Delete** e Do **Not Purge** configurato per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) o [l'interfaccia della riga di comando.](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la [guida introduttiva](../key-vault/key-vault-overview.md)dell'insieme di credenziali delle chiavi di Azure . Per ulteriori informazioni sull'importazione di chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).
1. Per attivare la protezione di eliminazione temporanea ed eliminazione durante la creazione di un insieme di credenziali, utilizzare il comando [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione dall'eliminazione a un vault esistente (che ha già attivato l'eliminazione temporanea), utilizzare il comando [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare le chiavi attenendosi alla seguente procedura:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Selezionare il pulsante Genera/Importa](./media/configure-customer-managed-key/select-generate-import.png)

    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 

    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi del bus di servizio per la crittografia dall'elenco a discesa. 

        ![Seleziona chiave dall'insieme di credenziali delle chiavi](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Per la ridondanza, è possibile aggiungere fino a 3 chiavi. Nel caso in cui una delle chiavi sia scaduta o non sia accessibile, le altre chiavi verranno utilizzate per la crittografia.
        
    1. Immettere i dettagli per la chiave e fare clic su **Seleziona**. Ciò consentirà la crittografia dei dati inattivi nello spazio dei nomi con una chiave gestita dal cliente. 


    > [!IMPORTANT]
    > Se si desidera utilizzare la chiave gestita dal cliente insieme al ripristino di emergenza di Geo, 
    >
    > Per abilitare la crittografia inlineata con la chiave gestita dal cliente, vengono impostati criteri di [accesso](../key-vault/key-vault-secure-your-key-vault.md) per l'identità gestita del bus di servizio nel KeyVault di Azure specificato. Ciò garantisce l'accesso controllato a Azure KeyVault dallo spazio dei nomi del bus di servizio di Azure.This ensures controlled access to the Azure KeyVault from the Azure Service Bus namespace.
    >
    > A causa di questo:
    > 
    >   * Se il ripristino di [emergenza geografico](service-bus-geo-dr.md) è già abilitato per lo spazio dei nomi del bus di servizio e si desidera abilitare la chiave gestita dal cliente, 
    >     * Interrompere l'associazione
    >     * [Impostare i criteri](../key-vault/managed-identity.md) di accesso per l'identità gestita per gli spazi dei nomi primario e secondario nell'insieme di credenziali delle chiavi.
    >     * Configurare la crittografia nello spazio dei nomi primario.
    >     * Riassociare gli spazi dei nomi primario e secondario.
    > 
    >   * Se si desidera abilitare Geo-DR in uno spazio dei nomi del bus di servizio in cui la chiave gestita dal cliente è già impostata,
    >     * [Impostare i criteri](../key-vault/managed-identity.md) di accesso per l'identità gestita per lo spazio dei nomi secondario nell'insieme di credenziali delle chiavi.
    >     * Associare gli spazi dei nomi primario e secondario.


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia

È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione degli insiemi di credenziali delle chiavi di Azure.You can rotate your key in the key vault by using the Azure Key Vaults rotation mechanism. Per ulteriori informazioni, consultate [Impostare la rotazione e](../key-vault/key-vault-key-rotation-log-monitoring.md)il controllo delle chiavi. Le date di attivazione e scadenza possono anche essere impostate per automatizzare la rotazione delle chiavi. Il servizio Bus di servizio rileverà le nuove versioni chiave e inizierà a utilizzarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revoca dell'accesso alle chiavi

La revoca dell'accesso alle chiavi di crittografia non eliminerà i dati dal bus di servizio. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi del bus di servizio. È possibile revocare la chiave di crittografia tramite i criteri di accesso o eliminando la chiave. Ulteriori informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi da [Accesso sicuro a un insieme di credenziali delle chiavi](../key-vault/key-vault-secure-your-key-vault.md).

Una volta revocata la chiave di crittografia, il servizio bus di servizio nello spazio dei nomi crittografato diventerà inutilizzabile. Se l'accesso alla chiave è abilitato o la chiave eliminata viene ripristinata, il servizio bus di servizio selezionerà la chiave in modo da poter accedere ai dati dallo spazio dei nomi del bus di servizio crittografato.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usare il modello di Resource Manager per abilitare la crittografiaUse Resource Manager template to enable encryption
In questa sezione viene illustrato come eseguire le attività seguenti usando i modelli di **Azure Resource Manager.** 

1. Creare uno spazio dei nomi del bus di servizio **premium** con **un'identità**del servizio gestito.
2. Creare un **insieme di credenziali** delle chiavi e concedere all'identità del servizio l'accesso all'insieme di credenziali delle chiavi. 
3. Aggiornare lo spazio dei nomi del bus di servizio con le informazioni sull'insieme di credenziali delle chiavi (chiave/valore). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Creare uno spazio dei nomi del bus di servizio premium con identità del servizio gestitoCreate a premium Service Bus namespace with managed service identity
Questa sezione illustra come creare uno spazio dei nomi del bus di servizio di Azure con identità del servizio gestito usando un modello di Azure Resource Manager e PowerShell.This section shows you how to create an Azure Service Bus namespace with managed service identity by using an Azure Resource Manager template and PowerShell. 

1. Creare un modello di Azure Resource Manager per creare uno spazio dei nomi del livello Premium del bus di servizio con un'identità del servizio gestito. Denominare il file: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Creare un file di parametri di modello denominato: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<ServiceBusNamespaceName>`- Nome dello spazio dei nomi del bus di servizio- Name of your Service Bus namespace
    > - `<Location>`- Percorso dello spazio dei nomi del bus di servizio- Location of your Service Bus namespace

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Eseguire il comando di PowerShell seguente per distribuire il modello per creare uno spazio dei nomi del bus di servizio premium. Quindi, recuperare l'ID dello spazio dei nomi del bus di servizio per utilizzarlo in un secondo momento. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Concedere l'accesso all'identità dello spazio dei nomi del bus di servizio all'insieme di credenziali delle chiaviGrant Service Bus namespace identity identity

1. Eseguire il comando seguente per creare un insieme di credenziali delle chiavi con **la protezione di eliminazione** e **l'eliminazione temporanea** abilitata. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    OPPURE
    
    Eseguire il comando riportato di seguito per aggiornare un **insieme di credenziali delle chiavi esistente.** Specificare i valori per i nomi dei gruppi di risorse e dell'insieme di credenziali delle chiavi prima di eseguire il comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Impostare i criteri di accesso dell'insieme di credenziali delle chiavi in modo che l'identità gestita dello spazio dei nomi del bus di servizio possa accedere al valore della chiave nell'insieme di credenziali delle chiavi. Utilizzare l'ID dello spazio dei nomi del bus di servizio della sezione precedente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Crittografare i dati nello spazio dei nomi del bus di servizio con la chiave gestita dal cliente dall'insieme di credenziali delle chiaviEncrypt data in Service Bus namespace with customer-managed key from key vault
Finora sono stati effettuati i seguenti passaggi: 

1. Creato uno spazio dei nomi premium con un'identità gestita.
2. Creare un insieme di credenziali delle chiavi e concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi. 

In questo passaggio verrà aggiornato lo spazio dei nomi del bus di servizio con le informazioni sull'insieme di credenziali delle chiavi. 

1. Creare un file JSON denominato UpdateServiceBusNamespaceWithEncryption.json con il contenuto seguente:Create a JSON file named **UpdateServiceBusNamespaceWithEncryption.json** with the following content: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Creare un file di parametri di modello: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<ServiceBusNamespaceName>`- Nome dello spazio dei nomi del bus di servizio- Name of your Service Bus namespace
    > - `<Location>`- Percorso dello spazio dei nomi del bus di servizio- Location of your Service Bus namespace
    > - `<KeyVaultName>`- Nome dell'insieme di credenziali delle chiavi
    > - `<KeyName>`- Nome della chiave nell'insieme di credenziali delle chiavi- Name of the key in the key vault  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Eseguire il comando PowerShell seguente per distribuire il modello di Resource Manager.Run the following PowerShell command to deploy the Resource Manager template. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica del bus di servizio](service-bus-messaging-overview.md)
- [Panoramica dell'Insieme di chiaviKey Vault overview](../key-vault/key-vault-overview.md)


