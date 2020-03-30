---
title: Configurare la propria chiave per la crittografia dei dati di Hub eventi di Azure inattiviConfigure your own key for encrypting Azure Event Hubs data at rest
description: Questo articolo fornisce informazioni su come configurare una chiave personalizzata per crittografare i dati di Hub eventi di Azure.This article provides information on how to configure your own key for encrypting Azure Event Hubs data rest.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621998"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per la crittografia dei dati di Hub eventi di Azure inattivi tramite il portale di AzureConfigure customer-managed keys for encrypting Azure Event Hubs data at rest by using the Azure portal
Hub eventi di Azure offre la crittografia dei dati inattivi con Azure Storage Service Encryption (Azure SSE). Gli hub eventi si basano su Archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft.Event Hubs relies on Azure Storage to store the data and by default, all the data that is stored with Azure Storage is encrypted using Microsoft-managed keys. 

## <a name="overview"></a>Panoramica
Hub eventi di Azure ora supporta l'opzione di crittografare i dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente (Bring Your Own Key – BYOK). Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare i dati di Hub eventi di Azure inattivi.

L'abilitazione della funzionalità BYOK è un processo di configurazione una tantera nello spazio dei nomi.

> [!NOTE]
> La funzionalità BYOK è supportata dai cluster [dedicati a tenant di Hub eventi.](event-hubs-dedicated-overview.md) Non può essere abilitato per gli spazi dei nomi Hub di eventi standard.

È possibile usare l'insieme di credenziali delle chiavi di Azure per gestire le chiavi e controllare l'utilizzo delle chiavi. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../key-vault/key-vault-overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente tramite il portale di Azure.This article shows how to configure a key vault with customer-managed keys by using the Azure portal. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Un](../key-vault/quick-create-portal.md)insieme di credenziali delle chiavi di Azure usando il portale di Azure.To learn how to create a key vault using the Azure portal, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

> [!IMPORTANT]
> L'uso di chiavi gestite dal cliente con Hub eventi di Azure richiede che nell'insieme di credenziali delle chiavi siano configurate due proprietà obbligatorie. Essi sono: **Eliminazione soft** e **non eliminare**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure.These properties are enabled by default when you create a new key vault in the Azure portal. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.However, if you need to enable these properties on an existing key vault, you must use either PowerShell or Azure CLI.

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys
Per abilitare le chiavi gestite dal cliente nel portale di Azure, eseguire la procedura seguente:To enable customer-managed keys in the Azure portal, follow these steps:

1. Passare al cluster dedicato agli hub eventi.
1. Selezionare lo spazio dei nomi in cui si desidera abilitare BYOK.
1. Nella pagina **Impostazioni** dello spazio dei nomi Hub eventi selezionare **Crittografia**. 
1. Selezionare la crittografia della **chiave gestita dal cliente inattivi** come illustrato nell'immagine seguente. 

    ![Abilita chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con chiaviSet up a key vault with keys
Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita del cliente allo spazio dei nomi Hub eventi di Azure.After you enable customer-managed keys, you need to associate the customer managed key with your Azure Event Hubs namespace. Gli hub eventi supportano solo L'insieme di chiavi di Azure.Event Hubs supports only Azure Key Vault. Se si abilita l'opzione Crittografia con chiave gestita dal cliente nella sezione precedente, è necessario che la chiave importi nell'insieme di credenziali delle chiavi di Azure.If you enable the **Encryption with customer-managed key option** in the previous section, you need to have the key imported into Azure Key Vault. Inoltre, le chiavi devono avere **Soft Delete** e Do **Not Purge** configurato per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) o [l'interfaccia della riga di comando.](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la [guida introduttiva](../key-vault/key-vault-overview.md)dell'insieme di credenziali delle chiavi di Azure . Per ulteriori informazioni sull'importazione di chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).
1. Per attivare la protezione di eliminazione temporanea ed eliminazione durante la creazione di un insieme di credenziali, utilizzare il comando [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione dall'eliminazione a un vault esistente (che ha già attivato l'eliminazione temporanea), utilizzare il comando [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare le chiavi attenendosi alla seguente procedura:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Selezionare il pulsante Genera/Importa](./media/configure-customer-managed-key/select-generate-import.png)
    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 
    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi Hub eventi per la crittografia dall'elenco a discesa. 

        ![Seleziona chiave dall'insieme di credenziali delle chiavi](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Immettere i dettagli per la chiave e fare clic su **Seleziona**. Ciò consentirà la crittografia dei dati inattivi nello spazio dei nomi con una chiave gestita dal cliente. 


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia
È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione degli insiemi di credenziali delle chiavi di Azure.You can rotate your key in the key vault by using the Azure Key Vaults rotation mechanism. Per ulteriori informazioni, consultate [Impostare la rotazione e](../key-vault/key-vault-key-rotation-log-monitoring.md)il controllo delle chiavi. Le date di attivazione e scadenza possono anche essere impostate per automatizzare la rotazione delle chiavi. Il servizio Hub eventi rileverà le nuove versioni chiave e inizierà a usarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revoca dell'accesso alle chiavi
La revoca dell'accesso alle chiavi di crittografia non eliminerà i dati dagli hub eventi. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi Hub eventi. È possibile revocare la chiave di crittografia tramite i criteri di accesso o eliminando la chiave. Ulteriori informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi da [Accesso sicuro a un insieme di credenziali delle chiavi](../key-vault/key-vault-secure-your-key-vault.md).

Una volta revocata la chiave di crittografia, il servizio Hub eventi nello spazio dei nomi crittografato diventerà inutilizzabile. Se l'accesso alla chiave è abilitato o viene ripristinata la chiave di eliminazione, il servizio Hub eventi selezionerà la chiave in modo da poter accedere ai dati dallo spazio dei nomi Hub eventi crittografato.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica 
L'impostazione dei log di diagnostica per gli spazi dei nomi abilitati per BYOK fornisce le informazioni necessarie sulle operazioni quando uno spazio dei nomi viene crittografato con chiavi gestite dal cliente. Questi log possono essere abilitati e successivamente in streaming a un hub eventi o analizzati tramite l'analisi dei log o trasmessi nell'archiviazione per eseguire analisi personalizzate. Per altre informazioni sui log di diagnostica, vedere [Panoramica dei log di diagnostica di Azure.To](../azure-monitor/platform/platform-logs-overview.md)learn more about diagnostic logs, see Overview of Azure Diagnostic logs.

## <a name="enable-user-logs"></a>Abilitare i registri utente
Seguire questi passaggi per abilitare i log per le chiavi gestite dal cliente.

1. Nel portale di Azure passare allo spazio dei nomi con BYOK abilitato.
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.

    ![Selezionare le impostazioni di diagnostica](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selezionare **: Aggiungi impostazione diagnostica**. 

    ![Selezionare Aggiungi impostazione diagnostica](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Specificare un **nome** e selezionare la posizione in cui si desidera trasmettere i log.
1. Selezionare **CustomerManagedKeyUserLogs** e **Salva**. Questa azione abilita i log per BYOK nello spazio dei nomi.

    ![Selezionare l'opzione di log utente chiave gestita dal cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schema del log 
Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce dispone di campi stringa che utilizzano il formato descritto nella tabella seguente. 

| Nome | Descrizione |
| ---- | ----------- | 
| TaskName | Descrizione dell'attività non riuscita. |
| ActivityId | ID interno utilizzato per il monitoraggio. |
| category | Definisce la classificazione dell'attività. Ad esempio, se la chiave dell'insieme di credenziali delle chiavi viene disabilitata, si tratterebbe di una categoria di informazioni o se non è possibile annullare il wrapping di una chiave, potrebbe essere in errore. |
| resourceId | ID della risorsa Azure Resource Manager |
| keyVault (chiaveVault) | Nome completo dell'insieme di credenziali delle chiavi. |
| Key | Nome della chiave utilizzato per crittografare lo spazio dei nomi Hub eventi. |
| version | Versione della chiave in uso. |
| operazione | Operazione eseguita sulla chiave nell'insieme di credenziali delle chiavi. Ad esempio, disabilitare/abilitare la chiave, avvolgere o annullare il wrapping |
| codice | Codice associato all'operazione. Esempio: codice di errore, 404 indica che la chiave non è stata trovata. |
| message | Qualsiasi messaggio di errore associato all'operazione |

Ecco un esempio del log per una chiave gestita dal cliente:Here's an example of the log for a customer managed key:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usare il modello di Resource Manager per abilitare la crittografiaUse Resource Manager template to enable encryption
In questa sezione viene illustrato come eseguire le attività seguenti usando i modelli di **Azure Resource Manager.** 

1. Creare **uno spazio dei nomi Hub eventi** con un'identità del servizio gestito.
2. Creare un **insieme di credenziali** delle chiavi e concedere all'identità del servizio l'accesso all'insieme di credenziali delle chiavi. 
3. Aggiornare lo spazio dei nomi Hub eventi con le informazioni sull'insieme di credenziali delle chiavi (chiave/valore). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Creare un cluster e uno spazio dei nomi di Hub eventi con identità del servizio gestitoCreate an Event Hubs cluster and namespace with managed service identity
Questa sezione illustra come creare uno spazio dei nomi Hub eventi di Azure con identità del servizio gestito usando un modello di Azure Resource Manager e PowerShell.This section shows you how to create an Azure Event Hubs namespace with managed service identity by using an Azure Resource Manager template and PowerShell. 

1. Creare un modello di Azure Resource Manager per creare uno spazio dei nomi Hub eventi con un'identità del servizio gestito. Denominare il file: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Creare un file di parametri di modello denominato: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>`- Nome del cluster Hub eventi    
    > - `<EventHubsNamespaceName>`- Nome dello spazio dei nomi di Hub eventi
    > - `<Location>`- Percorso dello spazio dei nomi Hub eventi

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Eseguire il comando PowerShell seguente per distribuire il modello per creare uno spazio dei nomi Hub eventi. Quindi, recuperare l'ID dello spazio dei nomi Hub eventi per utilizzarlo in un secondo momento. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Concedere all'insieme di credenziali l'identità dello spazio dei nomi Hub eventi

1. Eseguire il comando seguente per creare un insieme di credenziali delle chiavi con **la protezione di eliminazione** e **l'eliminazione temporanea** abilitata. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    OPPURE    
    
    Eseguire il comando riportato di seguito per aggiornare un **insieme di credenziali delle chiavi esistente.** Specificare i valori per i nomi dei gruppi di risorse e dell'insieme di credenziali delle chiavi prima di eseguire il comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Impostare i criteri di accesso dell'insieme di credenziali delle chiavi in modo che l'identità gestita dello spazio dei nomi Hub eventi possa accedere al valore della chiave nell'insieme di credenziali delle chiavi. Usare l'ID dello spazio dei nomi Hub eventi della sezione precedente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Crittografare i dati nello spazio dei nomi Hub eventi con la chiave gestita dal cliente dall'insieme di credenziali delle chiaviEncrypt data in Event Hubs namespace with customer-managed key from key vault
Finora sono stati effettuati i seguenti passaggi: 

1. Creato uno spazio dei nomi premium con un'identità gestita.
2. Creare un insieme di credenziali delle chiavi e concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi. 

In questo passaggio verrà aggiornato lo spazio dei nomi Hub eventi con le informazioni sull'insieme di credenziali delle chiavi. 

1. Creare un file JSON denominato CreateEventHubClusterAndNamespace.json con il contenuto seguente:Create a JSON file named **CreateEventHubHubClusterAndNamespace.json** with the following content: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Creare un file di parametri di modello: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>`- Nome del cluster Hub eventi.        
    > - `<EventHubsNamespaceName>`- Nome dello spazio dei nomi di Hub eventi
    > - `<Location>`- Percorso dello spazio dei nomi Hub eventi
    > - `<KeyVaultName>`- Nome dell'insieme di credenziali delle chiavi
    > - `<KeyName>`- Nome della chiave nell'insieme di credenziali delle chiavi- Name of the key in the key vault

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Risolvere i problemi
Come procedura consigliata, abilitare sempre i log come illustrato nella sezione precedente. Aiuta a tenere traccia delle attività quando la crittografia BYOK è abilitata. Aiuta anche a rintracciare i problemi.

Di seguito sono riportati i codici di errore comuni da cercare quando è abilitata la crittografia BYOK.

| Azione | Codice di errore | Stato dei dati risultante |
| ------ | ---------- | ----------------------- | 
| Rimuovere l'autorizzazione di wrapping/unwrap da un insieme di credenziali delle chiaviRemove wrap/unwrap permission from a key vault | 403 |    Inaccessible |
| Rimuovere l'appartenenza al ruolo AAD da un'entità AAD che ha concesso l'autorizzazione di wrapping/unwrapRemove AAD role membership from an AAD principal that granted the wrap/unwrap permission | 403 |  Inaccessible |
| Eliminare una chiave di crittografia dall'insieme di credenziali delle chiaviDelete an encryption key from the key vault | 404 | Inaccessible |
| Eliminare l'insieme di credenziali delle chiavi | 404 | Inaccessibile (presuppone che l'eliminazione temporanea sia abilitata, che è un'impostazione obbligatoria). |
| Modifica del periodo di scadenza della chiave di crittografia in modo che sia già scaduta | 403 |   Inaccessible  |
| Modifica dell'FB NBF (non prima) in modo che la chiave di crittografia della chiave non sia attiva | 403 | Inaccessible  |
| Selezione dell'opzione **Consenti servizi MSFT** per il firewall del vault della chiave o blocco del sistema di credenziali di rete in altro modo per l'insieme di credenziali delle chiavi con la chiave di crittografia | 403 | Inaccessible |
| Spostamento dell'insieme di credenziali delle chiavi in un tenant diverso | 404 | Inaccessible |  
| Problema di rete intermittente o interruzione DNS/AAD/MSI |  | Accessibile tramite chiave di crittografia dei dati memorizzata nella cache |

> [!IMPORTANT]
> Per abilitare il Geo-DR in uno spazio dei nomi che utilizza la crittografia BYOK, lo spazio dei nomi secondario per l'associazione deve trovarsi in un cluster dedicato e deve disporre di un'identità gestita assegnata al sistema. Per altre informazioni, vedere Identità gestite per le risorse di Azure.To learn more, see [Managed Identities for Azure Resources.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica di Hub eventi](event-hubs-about.md)
- [Panoramica dell'Insieme di chiaviKey Vault overview](../key-vault/key-vault-overview.md)




