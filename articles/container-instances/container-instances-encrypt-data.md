---
title: Crittografa dati di distribuzione
description: Informazioni sulla crittografia dei dati salvati in modo permanente per le risorse dell'istanza di contenitore e su come crittografare i dati con una chiave gestita dal cliente
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904211"
---
# <a name="encrypt-deployment-data"></a>Crittografa dati di distribuzione

Quando si eseguono risorse di istanze di contenitore di Azure (ACI) nel cloud, il servizio ACI raccoglie e Salva in modo permanente i dati relativi ai contenitori. ACI crittografa automaticamente questi dati quando vengono salvati in modo permanente nel cloud. Questa crittografia consente di proteggere i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. ACI offre inoltre la possibilità di crittografare i dati con la propria chiave, garantendo un maggiore controllo sui dati relativi alle distribuzioni di ACI.

## <a name="about-aci-data-encryption"></a>Informazioni sulla crittografia dei dati ACI 

I dati in ACI vengono crittografati e decrittografati usando la crittografia AES a 256 bit. È abilitata per tutte le distribuzioni di ACI e non è necessario modificare la distribuzione o i contenitori per sfruttare questa crittografia. Sono inclusi i metadati relativi alla distribuzione, le variabili di ambiente, le chiavi passate nei contenitori e i log resi persistenti dopo l'arresto dei contenitori, in modo che sia ancora possibile visualizzarli. La crittografia non influisce sulle prestazioni del gruppo di contenitori e non sono previsti costi aggiuntivi per la crittografia.

## <a name="encryption-key-management"></a>Gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite da Microsoft per la crittografia dei dati del contenitore oppure è possibile gestire la crittografia con le proprie chiavi. Nella tabella seguente vengono confrontate le opzioni seguenti: 

|    |    Chiavi gestite da Microsoft     |     Chiavi gestite dal cliente     |
|----|----|----|
|    Operazioni di crittografia/decrittografia    |    Azure    |    Azure    |
|    Archiviazione chiavi    |    Archivio chiavi Microsoft    |    Azure Key Vault    |
|    Responsabilità della rotazione delle chiavi    |    Microsoft    |    Customer    |
|    Accesso alle chiavi    |    Solo Microsoft    |    Microsoft, cliente    |

Il resto del documento illustra i passaggi necessari per crittografare i dati di distribuzione ACI con la chiave (chiave gestita dal cliente). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Crittografare i dati con una chiave gestita dal cliente

### <a name="create-service-principal-for-aci"></a>Creare un'entità servizio per ACI

Il primo passaggio consiste nel verificare che il [tenant di Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) disponga di un'entità servizio assegnata per concedere le autorizzazioni al servizio istanze di contenitore di Azure. 

Il seguente comando dell'interfaccia della riga di comando configurerà l'ambiente ACI SP nell'ambiente Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

L'output dell'esecuzione di questo comando dovrebbe mostrare un'entità servizio configurata con "displayName": "servizio istanza di contenitore di Azure".

### <a name="create-a-key-vault-resource"></a>Creare una risorsa Key Vault

Creare una Azure Key Vault usando [portale di Azure](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), l' [interfaccia](https://docs.microsoft.com/azure/key-vault/quick-create-cli)della riga di comando o [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Per le proprietà dell'insieme di credenziali delle chiavi, usare le linee guida seguenti: 
* Nome: è necessario un nome univoco. 
* Sottoscrizione: scegliere una sottoscrizione.
* In gruppo di risorse scegliere un gruppo di risorse esistente o crearne uno nuovo e immettere un nome per il gruppo di risorse.
* Scegliere un percorso nel menu a discesa Percorso.
* È possibile lasciare le altre opzioni predefinite o selezionare in base a requisiti aggiuntivi.

> [!IMPORTANT]
> Quando si usano chiavi gestite dal cliente per crittografare un modello di distribuzione ACI, è consigliabile impostare le due proprietà seguenti nell'insieme di credenziali delle chiavi, eliminare temporaneamente e non ripulire. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

### <a name="generate-a-new-key"></a>Genera una nuova chiave 

Dopo aver creato l'insieme di credenziali delle chiavi, passare alla risorsa in portale di Azure. Nel menu di spostamento a sinistra del pannello della risorsa, in impostazioni, fare clic su **chiavi**. Nella vista "chiavi", fare clic su "genera/importa" per generare una nuova chiave. Usare un nome univoco per questa chiave e qualsiasi altra preferenza in base ai requisiti. 

![Genera una nuova chiave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Impostare i criteri di accesso

Creare nuovi criteri di accesso per consentire al servizio ACI di accedere alla chiave.

* Dopo che la chiave è stata generata, tornare al pannello delle risorse dell'insieme di credenziali delle chiavi, in impostazioni, fare clic su **criteri di accesso**.
* Nella pagina "criteri di accesso" dell'insieme di credenziali delle chiavi fare clic su **Aggiungi criteri di accesso**.
* Impostare le *autorizzazioni chiave* per includere le autorizzazioni **Get** e **Unwrap** Key ![set Key](./media/container-instances-encrypt-data/set-key-permissions.png)
* Per *Select Principal*selezionare il **servizio istanza di contenitore di Azure**
* Fare clic su **Aggiungi** nella parte inferiore 

I criteri di accesso dovrebbero ora essere visualizzati nei criteri di accesso dell'insieme di credenziali delle chiavi.

![Nuovo criterio di accesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSON

> [!IMPORTANT]
> La crittografia dei dati di distribuzione con una chiave gestita dal cliente è disponibile nella versione più recente dell'API (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione. In caso di problemi, rivolgersi al supporto tecnico di Azure.

Una volta configurati i criteri di accesso e la chiave dell'insieme di credenziali delle chiavi, aggiungere la proprietà seguente al modello di distribuzione ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere l' [esercitazione: distribuire un gruppo multicontenitore usando un modello di gestione risorse](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

In particolare, nella sezione Proprietà gruppo di contenitori del modello di distribuzione, aggiungere un "encryptionProperties", che contiene i valori seguenti:
* vaultBaseUrl: il nome DNS dell'insieme di credenziali delle chiavi. si trova nel pannello panoramica della risorsa key Vault nel portale
* Nome chiave: il nome della chiave generata in precedenza
* chiave Version: versione corrente della chiave. Questo può essere trovato facendo clic sulla chiave stessa (in "chiavi" nella sezione impostazioni della risorsa key Vault)


```json
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
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Distribuire le risorse

Se il file modello è stato creato e modificato sul desktop, è possibile caricarlo nella directory Cloud Shell trascinandolo. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Al termine della distribuzione, tutti i dati relativi a tale servizio verranno crittografati con la chiave specificata.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create