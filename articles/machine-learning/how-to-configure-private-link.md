---
title: Configurare un endpoint privato (anteprima)
titleSuffix: Azure Machine Learning
description: Usare il collegamento privato di Azure per accedere in modo sicuro all'area di lavoro di Azure Machine Learning da una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 28fa3fdfdb8915d528d0765bd1fded8128621208
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166519"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configurare il collegamento privato di Azure per un'area di lavoro Azure Machine Learning (anteprima)

In questo documento si apprenderà come usare il collegamento privato di Azure con l'area di lavoro Azure Machine Learning. 

> [!IMPORTANT]
> L'uso del collegamento privato di Azure con Azure Machine Learning area di lavoro è attualmente disponibile in anteprima pubblica. Questa funzionalità è disponibile solo nelle aree **Stati Uniti orientali** e **Stati Uniti occidentali 2** . Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro solo per gli indirizzi IP privati. Il collegamento privato consente di ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Il collegamento privato di Azure non produce il piano di controllo di Azure (operazioni di gestione), ad esempio l'eliminazione dell'area di lavoro o la gestione delle risorse di calcolo. Ad esempio la creazione, l'aggiornamento o l'eliminazione di una destinazione di calcolo. Queste operazioni vengono eseguite sulla rete Internet pubblica come di consueto.
>
> L'anteprima delle istanze di calcolo di Azure Machine Learning non è supportata in un'area di lavoro in cui è abilitato il collegamento privato.
>
> Se si usa Mozilla Firefox, è possibile che si verifichino problemi durante il tentativo di accedere all'endpoint privato per l'area di lavoro. Questo problema può essere correlato a DNS su HTTPS in Mozilla. È consigliabile usare Microsoft Edge di Google Chrome come soluzione alternativa.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creare un'area di lavoro che usa un endpoint privato

> [!IMPORTANT]
> Attualmente è supportata solo l'abilitazione di un endpoint privato quando si crea una nuova area di lavoro Azure Machine Learning.

Il [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) può essere usato per creare un'area di lavoro con un endpoint privato.

Per informazioni sull'uso di questo modello, inclusi gli endpoint privati, vedere [usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso di un'area di lavoro su un endpoint privato

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> Per evitare l'interferenza temporanea della connettività, Microsoft consiglia di scaricare la cache DNS nei computer che si connettono all'area di lavoro dopo aver abilitato il collegamento privato. 

Per informazioni sulle macchine virtuali di Azure, vedere la [documentazione relativa alle macchine virtuali](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Uso di Archiviazione di Azure

Per proteggere l'account di archiviazione di Azure usato dall'area di lavoro, inserirlo all'interno della rete virtuale.

Per informazioni sull'inserimento dell'account di archiviazione nella rete virtuale, vedere [usare un account di archiviazione per l'area di lavoro](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning non supporta l'uso di un account di archiviazione di Azure con collegamento privato abilitato.

## <a name="using-azure-key-vault"></a>Uso di Azure Key Vault

Per proteggere il Azure Key Vault usato dall'area di lavoro, è possibile inserirlo all'interno della rete virtuale o abilitarvi un collegamento privato.

Per informazioni sull'inserimento dell'insieme di credenziali delle chiavi nella rete virtuale, vedere [usare un'istanza di Key Vault con l'area di lavoro](how-to-enable-virtual-network.md#key-vault-instance).

Per informazioni sull'abilitazione del collegamento privato per l'insieme di credenziali delle chiavi, vedere [integrare Key Vault con il collegamento privato di Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Uso dei servizi Kubernetes di Azure

Per proteggere i servizi Kubernetes di Azure usati dall'area di lavoro, inserirli all'interno di una rete virtuale. Per altre informazioni, vedere [usare i servizi Kubernetes di Azure con l'area di lavoro](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning supporta ora l'uso di un servizio Azure Kubernetes con collegamento privato abilitato.
Per creare un cluster AKS privato, seguire i documenti [qui](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Registro Azure Container

Per informazioni sulla protezione di Container Registry di Azure all'interno della rete virtuale, vedere [usare azure container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Se si usa un collegamento privato per l'area di lavoro di Azure Machine Learning e si inserisce il Container Registry di Azure per l'area di lavoro in una rete virtuale, è necessario applicare anche il modello di Azure Resource Manager seguente. Questo modello consente all'area di lavoro di comunicare con ACR tramite il collegamento privato.

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
          "tier": "enterprise",
          "name": "enterprise"
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

Per ulteriori informazioni sulla protezione dell'area di lavoro Azure Machine Learning, vedere l'articolo relativo alla [sicurezza aziendale](concept-enterprise-security.md) .