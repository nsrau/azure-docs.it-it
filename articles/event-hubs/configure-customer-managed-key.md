---
title: Configurare la propria chiave per la crittografia dei dati inattivi di hub eventi di Azure
description: Questo articolo fornisce informazioni su come configurare la propria chiave per la crittografia dei dati REST di hub eventi di Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459135"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure usando il portale di Azure
Hub eventi di Azure fornisce la crittografia dei dati inattivi con crittografia del servizio di archiviazione di Azure (SSE di Azure). Hub eventi si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati usando le chiavi gestite da Microsoft. 

## <a name="overview"></a>Panoramica
Hub eventi di Azure ora supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente (Bring Your Own Key – BYOK). Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia dei dati inattivi di hub eventi di Azure.

L'abilitazione della funzionalità BYOK è un processo di configurazione una volta nello spazio dei nomi.

> [!NOTE]
> La funzionalità BYOK è supportata dai cluster a [tenant singolo dedicati di hub eventi](event-hubs-dedicated-overview.md) . Non può essere abilitata per gli spazi dei nomi standard di hub eventi.

È possibile usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo della chiave. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../key-vault/general/overview.md)

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente usando il portale di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> Per usare chiavi gestite dal cliente con hub eventi di Azure, è necessario che nell'insieme di credenziali delle chiavi siano configurate due proprietà obbligatorie. Sono: **eliminazione** temporanea e **non ripulitura**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente
Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare al cluster Hub eventi Dedicato.
1. Selezionare lo spazio dei nomi in cui si vuole abilitare BYOK.
1. Nella pagina **Impostazioni** dello spazio dei nomi di hub eventi selezionare **crittografia**. 
1. Selezionare la **crittografia della chiave gestita dal cliente** , come illustrato nella figura seguente. 

    ![Abilita chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con chiavi
Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita dal cliente allo spazio dei nomi di hub eventi di Azure. Hub eventi supporta solo Azure Key Vault. Se si Abilita l'opzione **crittografia con chiave gestita dal cliente** nella sezione precedente, è necessario importare la chiave in Azure Key Vault. Inoltre, le chiavi devono essere **eliminate temporaneamente** e **non vengono rieliminate** configurate per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell](../key-vault/general/soft-delete-powershell.md) o l' [interfaccia](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)della riga di comando.

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la [Guida introduttiva](../key-vault/general/overview.md)Azure Key Vault. Per ulteriori informazioni sull'importazione di chiavi esistenti, vedere [informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).
1. Per attivare l'eliminazione temporanea e ripulire la protezione durante la creazione di un insieme di credenziali, usare il comando [AZ per Vault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione di ripulitura a un insieme di credenziali esistente (in cui è già abilitata l'eliminazione temporanea), usare il comando [AZ di Vault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare le chiavi attenendosi alla procedura seguente:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Pulsante Seleziona genera/importa](./media/configure-customer-managed-key/select-generate-import.png)
    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 
    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi di hub eventi per la crittografia dall'elenco a discesa. 

        ![Selezionare la chiave da Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Inserire i dettagli per la chiave e fare clic su **Seleziona**. In questo modo verrà abilitata la crittografia dei dati inattivi nello spazio dei nomi con una chiave gestita dal cliente. 


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia
È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione di Azure Key Vault. Per altre informazioni, vedere [configurare la rotazione e il controllo delle chiavi](../key-vault/secrets/key-rotation-log-monitoring.md). È anche possibile impostare le date di attivazione e di scadenza per automatizzare la rotazione delle chiavi. Il servizio Hub eventi rileverà nuove versioni chiave e inizierà a utilizzarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revoca l'accesso alle chiavi
La revoca dell'accesso alle chiavi di crittografia non eliminerà i dati da Hub eventi. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi di hub eventi. Per revocare la chiave di crittografia, è possibile usare i criteri di accesso oppure eliminare la chiave. Altre informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi dall' [accesso sicuro a un insieme di](../key-vault/general/secure-your-key-vault.md)credenziali delle chiavi.

Una volta revocata la chiave di crittografia, il servizio Hub eventi nello spazio dei nomi crittografato diventerà inutilizzabile. Se l'accesso alla chiave è abilitato o il tasto CANC viene ripristinato, il servizio Hub eventi selezionerà la chiave in modo che sia possibile accedere ai dati dallo spazio dei nomi di hub eventi crittografati.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica 
L'impostazione di log di diagnostica per gli spazi dei nomi abilitati per BYOK fornisce le informazioni necessarie sulle operazioni quando uno spazio dei nomi è crittografato con chiavi gestite dal cliente. Questi log possono essere abilitati e successivamente trasmessi a un hub eventi o analizzati tramite log Analytics o trasmessi all'archiviazione per eseguire analisi personalizzate. Per altre informazioni sui log di diagnostica, vedere [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Abilitare i log utente
Seguire questa procedura per abilitare i log per le chiavi gestite dal cliente.

1. Nella portale di Azure passare allo spazio dei nomi in cui è abilitato BYOK.
1. Selezionare **impostazioni di diagnostica** in **monitoraggio**.

    ![Selezionare le impostazioni di diagnostica](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selezionare **+ Aggiungi impostazioni di diagnostica**. 

    ![Selezionare Aggiungi impostazioni di diagnostica](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Specificare un **nome** e selezionare la posizione in cui si desidera trasmettere i log.
1. Selezionare **CustomerManagedKeyUserLogs** e **Salva**. Questa azione Abilita i registri per BYOK nello spazio dei nomi.

    ![Selezionare l'opzione log utente chiave gestita dal cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schema del log 
Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce contiene campi stringa che usano il formato descritto nella tabella seguente. 

| Name | Descrizione |
| ---- | ----------- | 
| TaskName | Descrizione dell'attività non riuscita. |
| ActivityId | ID interno usato per il rilevamento. |
| category | Definisce la classificazione dell'attività. Se, ad esempio, la chiave dell'insieme di credenziali delle chiavi è disabilitata, sarà una categoria di informazioni o se non è possibile decrittografare una chiave, potrebbe rientrare in errore. |
| resourceId | ID della risorsa Azure Resource Manager |
| keyVault | Nome completo dell'insieme di credenziali delle chiavi. |
| Key | Nome della chiave usato per crittografare lo spazio dei nomi di hub eventi. |
| Versione | Versione della chiave usata. |
| operazione | Operazione eseguita sulla chiave nell'insieme di credenziali delle chiavi. Ad esempio, disabilitare/abilitare la chiave, eseguire il wrapping o annullare il wrapping |
| code | Codice associato all'operazione. Esempio: codice errore 404 indica che la chiave non è stata trovata. |
| message | Qualsiasi messaggio di errore associato all'operazione |

Di seguito è riportato un esempio di log per una chiave gestita dal cliente:

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usare Gestione risorse modello per abilitare la crittografia
In questa sezione viene illustrato come eseguire le attività seguenti utilizzando i **modelli di Azure Resource Manager**. 

1. Creare uno **spazio dei nomi di hub eventi** con un'identità del servizio gestito.
2. Creare un insieme di credenziali delle **chiavi** e concedere all'identità del servizio l'accesso all'insieme di credenziali delle chiavi. 
3. Aggiornare lo spazio dei nomi di hub eventi con le informazioni sull'insieme di credenziali delle chiavi (chiave/valore). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Creare un cluster di hub eventi e uno spazio dei nomi con identità del servizio gestito
Questa sezione illustra come creare uno spazio dei nomi di hub eventi di Azure con l'identità del servizio gestito usando un modello di Azure Resource Manager e PowerShell. 

1. Creare un modello di Azure Resource Manager per creare uno spazio dei nomi di hub eventi con un'identità del servizio gestito. Denominare il file: **CreateEventHubClusterAndNamespace. JSON**: 

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
2. Creare un file di parametri di modello denominato: **CreateEventHubClusterAndNamespaceParams. JSON**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>`: Nome del cluster di hub eventi    
    > - `<EventHubsNamespaceName>`: Nome dello spazio dei nomi di hub eventi
    > - `<Location>`-Percorso dello spazio dei nomi di hub eventi

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
3. Eseguire il comando di PowerShell seguente per distribuire il modello per creare uno spazio dei nomi di hub eventi. Recuperare quindi l'ID dello spazio dei nomi di hub eventi per usarlo in un secondo momento. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Concessione dell'accesso all'identità dello spazio dei nomi di hub eventi a Key Vault

1. Eseguire il comando seguente per creare un insieme di credenziali delle chiavi con la **protezione di ripulitura** e l' **eliminazione** temporanea abilitata. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    OPPURE    
    
    Eseguire il comando seguente per aggiornare un insieme di credenziali delle **chiavi esistente**. Specificare i valori per i nomi dei gruppi di risorse e delle chiavi prima di eseguire il comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Impostare i criteri di accesso di Key Vault in modo che l'identità gestita dello spazio dei nomi di hub eventi possa accedere al valore della chiave nell'insieme di credenziali delle chiavi. Usare l'ID dello spazio dei nomi di hub eventi della sezione precedente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Crittografare i dati nello spazio dei nomi di hub eventi con chiave gestita dal cliente da Key Vault
Fino a questo punto sono stati eseguiti i passaggi seguenti: 

1. È stato creato uno spazio dei nomi Premium con un'identità gestita.
2. Creare un insieme di credenziali delle chiavi e concedere all'identità gestita l'accesso a Key Vault. 

In questo passaggio verrà aggiornato lo spazio dei nomi di hub eventi con le informazioni sull'insieme di credenziali delle chiavi. 

1. Creare un file JSON denominato **CreateEventHubClusterAndNamespace. JSON** con il contenuto seguente: 

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

2. Creare un file di parametri di modello: **UpdateEventHubClusterAndNamespaceParams. JSON**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>`: Nome del cluster di hub eventi.        
    > - `<EventHubsNamespaceName>`: Nome dello spazio dei nomi di hub eventi
    > - `<Location>`-Percorso dello spazio dei nomi di hub eventi
    > - `<KeyVaultName>`: Nome dell'insieme di credenziali delle chiavi
    > - `<KeyName>`: Nome della chiave nell'insieme di credenziali delle chiavi

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
3. Eseguire il comando di PowerShell seguente per distribuire il modello di Gestione risorse. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Risolvere problemi
Come procedura consigliata, abilitare sempre i log, come illustrato nella sezione precedente. Consente di tenere traccia delle attività quando è abilitata la crittografia BYOK. Consente inoltre di rientrare nell'ambito dei problemi.

Di seguito sono riportati i codici di errore comuni da cercare quando è abilitata la crittografia BYOK.

| Action | Codice errore | Stato risultante dei dati |
| ------ | ---------- | ----------------------- | 
| Rimuovere l'autorizzazione wrap/unwrap da un insieme di credenziali delle chiavi | 403 |    Inaccessible |
| Rimuovere l'appartenenza al ruolo AAD da un'entità AAD che ha concesso l'autorizzazione wrap/unwrap | 403 |  Inaccessible |
| Eliminare una chiave di crittografia dall'insieme di credenziali delle chiavi | 404 | Inaccessible |
| Eliminare l'insieme di credenziali delle chiavi | 404 | Inaccessibile (si presuppone che l'eliminazione temporanea sia abilitata, che è un'impostazione obbligatoria). |
| Modificare il periodo di scadenza della chiave di crittografia in modo che sia già scaduto | 403 |   Inaccessible  |
| Modifica del NBF (non prima) in modo tale che la chiave di crittografia della chiave non sia attiva | 403 | Inaccessible  |
| Selezionare l'opzione **Consenti servizi MSFT** per il firewall dell'insieme di credenziali delle chiavi o bloccare l'accesso di rete all'insieme di credenziali delle chiavi con la chiave di crittografia | 403 | Inaccessible |
| Trasferimento dell'insieme di credenziali delle chiavi in un tenant diverso | 404 | Inaccessible |  
| Problemi di rete intermittenti o interruzione di DNS/AAD/MSI |  | Accessibile tramite la chiave di crittografia dei dati memorizzata nella cache |

> [!IMPORTANT]
> Per abilitare il ripristino di emergenza geografico in uno spazio dei nomi che usa la crittografia BYOK, lo spazio dei nomi secondario per l'associazione deve trovarsi in un cluster dedicato ed è necessario che sia abilitata un'identità gestita assegnata dal sistema. Per altre informazioni, vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica di Hub eventi](event-hubs-about.md)
- [Panoramica di Key Vault](../key-vault/general/overview.md)




