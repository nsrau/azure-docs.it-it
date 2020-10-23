---
title: Proteggere un'area di lavoro Azure Machine Learning con reti virtuali
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata per proteggere l'area di lavoro Azure Machine Learning e le risorse associate.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python, contperfq1
ms.openlocfilehash: ef1e3c886cfd30db4c6a550d0ecabe7d41fab55c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424991"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Proteggere un'area di lavoro Azure Machine Learning con reti virtuali

Questo articolo illustra come proteggere un'area di lavoro Azure Machine Learning e le risorse associate in una rete virtuale.


Questo articolo è la seconda parte di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. È consigliabile leggere prima di tutto la [parte 1: Panoramica di VNet](how-to-network-security-overview.md) per comprendere prima l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica di VNet](how-to-network-security-overview.md)  >  **2. Proteggere l'area di lavoro**  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)  >  [5. Abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)

In questo articolo viene illustrato come abilitare le risorse seguenti per le aree di lavoro in una rete virtuale:
> [!div class="checklist"]
> - Area di lavoro di Azure Machine Learning
> - Account di archiviazione di Azure
> - Azure Machine Learning datastores e DataSets
> - Azure Key Vault
> - Registro Azure Container

## <a name="prerequisites"></a>Prerequisiti

+ Vedere l'articolo [Panoramica della sicurezza di rete](how-to-network-security-overview.md) per comprendere gli scenari di rete virtuale comuni e l'architettura complessiva della rete virtuale.

+ Una rete virtuale e una subnet esistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve avere le autorizzazioni per le azioni seguenti nel controllo degli accessi in base al ruolo di Azure (RBAC di Azure):

    - "Microsoft. Network/virtualNetworks/join/Action" sulla risorsa di rete virtuale.
    - "Microsoft. Network/virtualNetworks/subnet/join/Action" sulla risorsa della subnet.

    Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con la rete, vedere [ruoli predefiniti di rete](/azure/role-based-access-control/built-in-roles#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Proteggere l'area di lavoro con un endpoint privato

Il collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro solo per gli indirizzi IP privati. Il collegamento privato consente di ridurre il rischio di exfiltration dei dati.

Per ulteriori informazioni sulla configurazione di un'area di lavoro di collegamento privato, vedere [How to configure private link](how-to-configure-private-link.md).

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Proteggere gli account di archiviazione di Azure con gli endpoint di servizio

Azure Machine Learning supporta gli account di archiviazione configurati per l'uso di endpoint di servizio o di endpoint privati. Questa sezione illustra come proteggere un account di archiviazione di Azure usando gli endpoint di servizio. Per gli endpoint privati, vedere la sezione successiva.

> [!IMPORTANT]
> È possibile inserire sia l'_account di archiviazione predefinito_ per Azure Machine Learning, sia _gli account di archiviazione non predefiniti_ in una rete virtuale.
>
> Quando si crea un'area di lavoro, viene eseguito automaticamente il provisioning dell'account di archiviazione predefinito.
>
> Per gli account di archiviazione non predefiniti, il parametro `storage_account` nella funzione [`Workspace.create()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-&preserve-view=true)consente di specificare un account di archiviazione personalizzato in base all'ID risorsa di Azure.

Per usare un account di archiviazione di Azure per l'area di lavoro in una rete virtuale, seguire questa procedura:

1. Nella portale di Azure andare al servizio di archiviazione che si vuole usare nell'area di lavoro.

   [![Archiviazione collegata all'area di lavoro di Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Nella pagina account del servizio di archiviazione selezionare __firewall e reti virtuali__.

   ![L'area "Firewall e reti virtuali" della pagina Archiviazione di Azure nel portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Nella pagina __Firewall e reti virtuali__ eseguire le operazioni seguenti:
    1. Selezionare __Reti selezionate__.
    1. In __Reti virtuali__ selezionare il collegamento __Aggiungi rete virtuale esistente__. Questa azione aggiunge la rete virtuale in cui risiede il calcolo (vedere il passaggio 1).

        > [!IMPORTANT]
        > L'account di archiviazione deve trovarsi nella stessa rete virtuale e nella stessa subnet delle istanze di calcolo o dei cluster di elaborazione usati per il training o l'inferenza.

    1. Selezionare la casella di controllo __Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione__.

    > [!IMPORTANT]
    > Quando si usa Azure Machine Learning SDK, l'ambiente di sviluppo deve essere in grado di connettersi all'account di archiviazione di Azure. Se l'account di archiviazione si trova all'interno di una rete virtuale, il firewall deve consentire l'accesso dall'indirizzo IP dell'ambiente di sviluppo.
    >
    > Per abilitare l'accesso all'account di archiviazione, vedere i __firewall e le reti virtuali__ per l'account di archiviazione *da un Web browser nel client di sviluppo*. Usare quindi la casella di controllo __Aggiungere l'indirizzo IP client__ per aggiungere l'indirizzo IP del client all'__intervallo di indirizzi__. È anche possibile usare il campo __INTERVALLO DI INDIRIZZI__ per immettere manualmente l'indirizzo IP dell'ambiente di sviluppo. Dopo aver aggiunto l'indirizzo IP per il client, è possibile accedere all'account di archiviazione usando l'SDK.

   [![Il riquadro "Firewall e reti virtuali" nel portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Proteggere gli account di archiviazione di Azure con endpoint privati

Azure Machine Learning supporta gli account di archiviazione configurati per l'uso di endpoint di servizio o di endpoint privati. Se l'account di archiviazione Usa endpoint privati, è necessario configurare due endpoint privati per l'account di archiviazione predefinito:
1. Un endpoint privato con una risorsa secondaria di destinazione **BLOB** .
1. Un endpoint privato con una risorsa secondaria di destinazione **file** (FileShare).

![Screenshot che mostra la pagina di configurazione degli endpoint privati con le opzioni BLOB e file](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Per configurare un endpoint privato per un account di archiviazione **diverso** da quello predefinito, selezionare il tipo di **risorsa secondaria di destinazione** corrispondente all'account di archiviazione che si vuole aggiungere.

Per altre informazioni, vedere [usare endpoint privati per archiviazione di Azure](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Archivi dati e set di dati protetti

Questa sezione illustra come usare l'archivio dati e i set di dati nell'esperienza SDK con una rete virtuale. Per altre informazioni sull'esperienza di studio, vedere [usare Azure Machine Learning Studio in una rete virtuale](how-to-enable-studio-virtual-network.md).

Per accedere ai dati tramite l'SDK, è necessario utilizzare il metodo di autenticazione richiesto dal singolo servizio in cui sono archiviati i dati. Ad esempio, se si registra un archivio dati per accedere Azure Data Lake Store Gen2, è comunque necessario usare un'entità servizio, come documentato in [connettersi ai servizi di archiviazione di Azure](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Disabilitare la convalida dei dati

Per impostazione predefinita, Azure Machine Learning esegue la validità dei dati e i controlli delle credenziali quando si tenta di accedere ai dati tramite l'SDK. Se i dati sono protetti da una rete virtuale, Azure Machine Learning possibile completare questi controlli. Per evitare questo problema, è necessario creare archivi dati e set di dati che ignorino la convalida.

### <a name="use-datastores"></a>Usare gli archivi dati

 Azure Data Lake Store Gen1 e Azure Data Lake Store Gen2 ignorano la convalida per impostazione predefinita, pertanto non è necessaria alcuna azione aggiuntiva. Per i servizi seguenti, tuttavia, è possibile usare una sintassi simile per ignorare la convalida dell'archivio dati:

- Archiviazione BLOB di Azure
- Condivisione file di Azure
- PostgreSQL
- Database SQL di Azure

L'esempio di codice seguente crea un nuovo archivio dati BLOB di Azure e imposta `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Usare i set di impostazioni

La sintassi per ignorare la convalida del set di dati è simile per i tipi di set di dati seguenti:
- File delimitato
- JSON 
- Parquet
- SQL
- File

Il codice seguente crea un nuovo set di dati JSON e imposta `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Azure Key Vault protetto

Azure Machine Learning usa un'istanza di Key Vault associata per archiviare le credenziali seguenti:
* La stringa di connessione all'account di archiviazione associato
* Le password per le istanze del repository del contenitore di Azure
* Le stringhe di connessione agli archivi dati

Per usare le funzionalità di sperimentazione di Azure Machine Learning con Azure Key Vault in una rete virtuale, seguire questa procedura:

1. Passare alla Key Vault associata all'area di lavoro.

1. Nel riquadro sinistro della pagina __Key Vault__ selezionare __rete__.

1. Nella scheda __firewall e reti virtuali__ eseguire le operazioni seguenti:
    1. In __Consenti accesso da__selezionare __endpoint privato e reti selezionate__.
    1. In __Reti virtuali__ selezionare __Aggiungi reti virtuali esistenti__ per aggiungere la rete virtuale in cui risiede il calcolo della sperimentazione.
    1. In __Consenti ai servizi Microsoft attendibili di ignorare questo firewall?__ selezionare __Sì__.

   [![Sezione "Firewall e reti virtuali" nel riquadro Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Abilitare Container Registry di Azure (ACR)

Per usare Container Registry di Azure all'interno di una rete virtuale, è necessario soddisfare i requisiti seguenti:

* Il Container Registry di Azure deve essere una versione Premium. Per altre informazioni sull'aggiornamento, vedere [Cambiare SKU](/azure/container-registry/container-registry-skus#changing-skus).

* Registro Azure Container deve essere nella stessa rete virtuale e nella stessa subnet dell'account di archiviazione e delle destinazioni di calcolo usati per il training o l'inferenza.

* L'area di lavoro di Azure Machine Learning deve contenere un [cluster di elaborazione di Azure Machine Learning](how-to-create-attach-compute-cluster.md).

    Quando Registro Azure Container è in una rete virtuale, Azure Machine Learning non può usarlo per compilare direttamente le immagini Docker. In questo caso viene usato il cluster di elaborazione per compilare le immagini.

* Prima di usare ACR con Azure Machine Learning in una rete virtuale, è necessario aprire un evento imprevisto di supporto per abilitare questa funzionalità. Per altre informazioni, vedere [gestire e aumentare le quote](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

Una volta soddisfatti questi requisiti, attenersi alla procedura seguente per abilitare Azure Container Registry.

1. Trovare il nome del Container Registry di Azure per l'area di lavoro, usando uno dei metodi seguenti:

    __Azure portal__

    Nella sezione Panoramica dell'area di lavoro il valore __Registro__ è collegato al Registro Azure Container.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro Azure Container per l'area di lavoro" border="true":::

    __Interfaccia della riga di comando di Azure__

    Se è stata installata [l'estensione Machine Learning per l'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md), è possibile usare il comando `az ml workspace show` per visualizzare le informazioni sull'area di lavoro.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Il comando restituisce un valore analogo a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. L'ultima parte della stringa è il nome del Registro Azure Container per l'area di lavoro.

1. Limitare l'accesso alla rete virtuale seguendo la procedura descritta in [configurare l'accesso alla rete per il registro di sistema](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Quando si aggiunge la rete virtuale, selezionare la rete virtuale e la subnet per le risorse di Azure Machine Learning.

1. Usare Python SDK di Azure Machine Learning per configurare un cluster di elaborazione per compilare immagini Docker. Nel frammento di codice riportato di seguito viene illustrato come effettuare questa operazione:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > L'account di archiviazione, il cluster di elaborazione e Registro Azure Container devono essere tutti nella stessa subnet della rete virtuale.
    
    Per altre informazioni, vedere il riferimento per il metodo [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-&preserve-view=true).

1. Applicare il modello di Azure Resource Manager seguente. che consente all'area di lavoro di comunicare con Registro Azure Container.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "basic",
            "name": "basic"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo fa parte di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica di rete virtuale](how-to-network-security-overview.md)
* [Parte 3: proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 4: proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)
* [Parte 5: abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)