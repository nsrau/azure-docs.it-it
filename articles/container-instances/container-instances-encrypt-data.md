---
title: Crittografare i dati della distribuzione
description: Informazioni sulla crittografia dei dati resi persistenti per le risorse dell'istanza del contenitore e su come crittografare i dati con una chiave gestita dal clienteLearn about encryption of data persisted for your container instance resources and how to encrypt the data with a customer-managed key
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080361"
---
# <a name="encrypt-deployment-data"></a>Crittografare i dati della distribuzione

Quando si eseguono risorse di istanze di contenitori di Azure (ACI) nel cloud, il servizio ACI raccoglie e rende persistenti i dati correlati ai contenitori. ACI crittografa automaticamente questi dati quando vengono resi persistenti nel cloud. Questa crittografia protegge i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. ACI offre anche la possibilità di crittografare questi dati con la propria chiave, offrendo un maggiore controllo sui dati relativi alle distribuzioni ACI.

## <a name="about-aci-data-encryption"></a>Informazioni sulla crittografia dei dati ACI 

I dati in ACI vengono crittografati e decrittografati utilizzando la crittografia AES a 256 bit. È abilitato per tutte le distribuzioni ACI e non è necessario modificare la distribuzione o i contenitori per sfruttare questa crittografia. Sono inclusi i metadati relativi alla distribuzione, le variabili di ambiente, le chiavi passate nei contenitori e i log salvati in modo permanente dopo l'arresto dei contenitori in modo da poterli visualizzare. La crittografia non influisce sulle prestazioni del gruppo di contenitori e non sono previsti costi aggiuntivi per la crittografia.

## <a name="encryption-key-management"></a>Gestione delle chiavi di crittografia

È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dei dati del contenitore oppure è possibile gestire la crittografia con chiavi personalizzate. Nella tabella seguente vengono confrontate queste opzioni: 

|    |    Chiavi gestite da Microsoft     |     Chiavi gestite dal cliente     |
|----|----|----|
|    Operazioni di crittografia/decrittografia    |    Azure    |    Azure    |
|    Memorizzazione delle chiavi    |    Archivio chiavi Microsoft    |    Insieme di credenziali chiave di Azure    |
|    Responsabilità di rotazione chiave    |    Microsoft    |    Customer    |
|    Accesso alle chiavi    |    Solo Microsoft    |    Microsoft, cliente    |

Il resto del documento illustra i passaggi necessari per crittografare i dati di distribuzione ACI con la chiave (chiave gestita dal cliente). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Crittografare i dati con una chiave gestita dal clienteEncrypt data with a customer-managed key

### <a name="create-service-principal-for-aci"></a>Creare un'entità servizio per ACICreate Service Principal for ACI

Il primo passaggio consiste nel verificare che al tenant di Azure sia assegnata un'entità servizio per concedere autorizzazioni al servizio Istanze del contenitore di Azure.The first step is to ensure that your [Azure tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) has a service principal assigned for granting permissions to the Azure Container Instances service. 

> [!IMPORTANT]
> Per eseguire il comando seguente e creare correttamente un'entità servizio, verificare di disporre delle autorizzazioni per creare entità servizio nel tenant.
>

Il comando dell'interfaccia della riga di comando seguente consente di configurare il sp ACI nell'ambiente Azure:The following CLI command will set up the ACI SP in your Azure environment:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

L'output dell'esecuzione di questo comando dovrebbe mostrare un'entità servizio che è stata impostata con "displayName": "Azure Container Instance Service".

Nel caso in cui non si riesca a creare correttamente l'entità servizio:In case you are unable to successfully create the service principal:
* verificare di disporre delle autorizzazioni necessarie nel tenant
* Verificare se un'entità servizio esiste già nel tenant per la distribuzione in ACI. È possibile farlo `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` eseguendo e usare tale entità servizio invece

### <a name="create-a-key-vault-resource"></a>Creare una risorsa Key Vault

Creare un insieme di credenziali delle chiavi di Azure usando il portale di [Azure,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/key-vault/quick-create-cli)o [PowerShell.](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) 

Per le proprietà dell'insieme di credenziali delle chiavi, utilizzare le linee guida seguenti: 
* Nome: è necessario un nome univoco. 
* Sottoscrizione: scegliere una sottoscrizione.
* In Gruppo di risorse scegliere un gruppo di risorse esistente oppure crearne uno nuovo e immettere un nome per il gruppo di risorse.
* Scegliere un percorso nel menu a discesa Percorso.
* È possibile lasciare le altre opzioni ai valori predefiniti o scegliere in base a requisiti aggiuntivi.

> [!IMPORTANT]
> Quando si utilizzano chiavi gestite dal cliente per crittografare un modello di distribuzione ACI, è consigliabile impostare le due proprietà seguenti nell'insieme di credenziali delle chiavi, Eliminazione temporanea e Non eliminare. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate usando PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

### <a name="generate-a-new-key"></a>Generare una nuova chiave 

Dopo aver creato l'insieme di credenziali delle chiavi, passare alla risorsa nel portale di Azure.Once your key vault is created, navigate to the resource in Azure portal. Nel menu di spostamento sinistro del pannello risorse, in Impostazioni, fare clic su **Chiavi**. Nella visualizzazione "Chiavi", fare clic su "Genera/Importa" per generare una nuova chiave. Utilizzare qualsiasi nome univoco per questa chiave e qualsiasi altra preferenza in base alle proprie esigenze. 

![Generare una nuova chiave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Impostare i criteri di accesso

Creare un nuovo criterio di accesso per consentire al servizio ACI di accedere alla chiave.

* Dopo aver generato la chiave, nel pannello delle risorse dell'insieme di credenziali delle chiavi, in Impostazioni, fare clic su Criteri di **accesso**.
* Nella pagina "Criteri di accesso" per l'insieme di credenziali delle chiavi fare clic su **Aggiungi criterio**di accesso .
* Impostare le *autorizzazioni chiave* per includere le autorizzazioni Per il blocco delle chiavi **Get** e **Unwrap Key** ![](./media/container-instances-encrypt-data/set-key-permissions.png)
* Per Seleziona entità , selezionare **Servizio istanza contenitore di AzureFor** *Select Principal*, select Azure Container Instance Service
* Fai clic su **Aggiungi** in basso 

I criteri di accesso dovrebbero ora essere visualizzati nei criteri di accesso dell'insieme di credenziali delle chiavi.

![Nuovo criterio di accesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSONModify your JSON deployment template

> [!IMPORTANT]
> La crittografia dei dati di distribuzione con una chiave gestita dal cliente è disponibile nell'ultima versione dell'API (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione. In caso di problemi con questo, contattare il supporto di Azure.If you have any issues with this, please reach out to Azure Support.

Dopo aver impostato la chiave dell'insieme di credenziali della chiave e i criteri di accesso, aggiungere le proprietà seguenti al modello di distribuzione ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere [Esercitazione: Distribuire un gruppo multicontenitore usando un modello](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)di Resource Manager. 
* In `resources`, `apiVersion` `2019-12-01`impostare su .
* Nella sezione delle proprietà del gruppo di `encryptionProperties`contenitori del modello di distribuzione aggiungere un oggetto , che contiene i valori seguenti:
  * `vaultBaseUrl`: il nome DNS dell'insieme di credenziali delle chiavi, disponibile nel pannello Panoramica della risorsa dell'insieme di credenziali delle chiavi nel portale
  * `keyName`: il nome della chiave generata in precedenza
  * `keyVersion`: la versione corrente della chiave. Questo può essere trovato facendo clic sulla chiave stessa (sotto "Chiavi" nella sezione Impostazioni della risorsa dell'insieme di credenziali delle chiavi)
* Nelle proprietà del gruppo `sku` di contenitori aggiungere una proprietà con valore `Standard`. La `sku` proprietà è obbligatoria nella versione API 2019-12-01.The property is required in API version 2019-12-01.

Il frammento di modello seguente mostra queste proprietà aggiuntive per crittografare i dati di distribuzione:The following template snippet shows these additional properties to encrypt deployment data:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Di seguito è riportato un modello completo, adattato dal modello in [Esercitazione: Distribuire un gruppo multicontenitore usando un modello di Resource Manager.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Distribuire le risorseDeploy your resources

Se il file modello è stato creato e modificato sul desktop, è possibile caricarlo nella directory di Cloud Shell trascinandoilo. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Al termine della distribuzione, tutti i dati ad essa correlati verranno salvati in modo permanente dal servizio ACI con la chiave fornita.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
