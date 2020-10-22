---
title: Distribuire un cluster gestito di Service Fabric (anteprima)
description: In questa esercitazione verrà distribuito un cluster gestito di Service Fabric per i test.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: 0265a1393c697cbd767de13df1064a5eea957380
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316176"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Esercitazione: Distribuire un cluster gestito di Service Fabric (anteprima)

In questa serie di esercitazioni verrà illustrato quanto segue:

> [!div class="checklist"]
> * Come distribuire un cluster gestito di Service Fabric 
> * [Come aumentare il numero di istanze di un cluster gestito di Service Fabric](tutorial-managed-cluster-scale.md)
> * [Come aggiungere e rimuovere nodi in un cluster gestito di Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Come distribuire un'applicazione in un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy-app.md)

Questa parte della serie descrive come:

> [!div class="checklist"]
> * Connettersi all'account di Azure
> * Creare un nuovo gruppo di risorse
> * Distribuire un cluster gestito di Service Fabric
> * Aggiungere un tipo di nodo primario al cluster

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha già una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md).

* Installare [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) (o versione successiva).

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Sostituire `<your-subscription>` con la stringa di sottoscrizione per l'account Azure, quindi connettersi:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Creare un nuovo gruppo di risorse

Successivamente, creare il gruppo di risorse per il cluster gestito di Service Fabric, sostituendo `<your-rg>` e `<location>` con il nome e la località da usare.

> [!NOTE]
> Le aree supportate per l'anteprima pubblica sono `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` e `eastus2`.

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Distribuire un cluster gestito di Service Fabric

### <a name="create-a-service-fabric-managed-cluster"></a>Creare un cluster gestito di Service Fabric

In questo passaggio si creerà un cluster gestito di Service Fabric usando il comando New-AzServiceFabricManagedCluster di PowerShell. L'esempio seguente crea un cluster denominato myCluster nel gruppo di risorse denominato myResourceGroup. Questo gruppo di risorse è stato creato nell'area eastus2 nel passaggio precedente.

Per questo passaggio, specificare valori personalizzati per i parametri seguenti:

* **Nome del cluster**: immettere un nome univoco per il cluster, ad esempio *mysfcluster*.
* **Password amministratore**: immettere una password per l'amministratore da usare per RDP nelle macchine virtuali sottostanti del cluster.
* **Identificazione personale del certificato client**: specificare l'identificazione personale del certificato client che si vuole usare per accedere al cluster. Se non si ha un certificato, seguire la procedura riportata in [Impostare e recuperare un certificato](../key-vault/certificates/quick-create-portal.md) per creare un certificato autofirmato.
* **SKU del cluster**: specificare il [tipo di cluster gestito di Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) da distribuire. I cluster dello SKU *Basic* sono destinati esclusivamente a distribuzioni di test e non consentono l'aggiunta o la rimozione di tipi di nodo.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Aggiungere un tipo di nodo primario al cluster gestito di Service Fabric

In questo passaggio verrà aggiunto un tipo di nodo primario al cluster appena creato. Ogni cluster di Service Fabric deve avere almeno un tipo di nodo primario.

Per questo passaggio, specificare valori personalizzati per i parametri seguenti:

* **Nome del tipo di nodo**: immettere un nome univoco per il tipo di nodo da aggiungere al cluster, ad esempio "NT1".

> [!NOTE]
> Se il tipo di nodo aggiunto è il primo o l'unico nel cluster, è necessario usare la proprietà primary.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Il completamento del comando può richiedere alcuni minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

### <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Una volta completata la distribuzione, trovare il valore di Service Fabric Explorer nella pagina di panoramica della risorsa cluster gestito di Service Fabric nel portale. Quando viene chiesto un certificato, usare quello per cui è stata specificata l'identificazione personale nel comando di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

In questo passaggio è stato creato e distribuito il primo cluster gestito di Service Fabric. Per altre informazioni su come dimensionare un cluster, vedere:

> [!div class="nextstepaction"]
> [Aumentare il numero di istanze in un cluster gestito di Service Fabric](tutorial-managed-cluster-scale.md)