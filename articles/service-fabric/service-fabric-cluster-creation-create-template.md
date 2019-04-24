---
title: Creare un modello di cluster di Azure Service Fabric| Microsoft Docs
description: Informazioni su come creare un modello di Resource Manager per un cluster di Service Fabric. Configurare la sicurezza, Azure Key Vault e Azure Active Directory (Azure AD) per l'autenticazione client.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 2fdea1f088dd6eabdf7d72342c837d976133a1bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386883"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creare un modello di Cluster Resource Manager di Service Fabric

Un [cluster di Azure Service Fabric](service-fabric-deploy-anywhere.md) è un set di macchine virtuali connesse in rete, in cui vengono distribuiti e gestiti i microservizi. Un cluster di Service Fabric in esecuzione in Azure è una risorsa di Azure e viene distribuito, gestito e monitorato tramite Resource Manager.  Questo articolo descrive come creare un modello di Resource Manager per un cluster di Service Fabric in esecuzione in Azure.  Dopo aver completato il modello, è possibile [distribuire il cluster in Azure](service-fabric-cluster-creation-via-arm.md).

La sicurezza del cluster viene configurata durante la configurazione iniziale del cluster e non può essere modificata in un secondo momento. Prima di configurare un cluster, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security]. In Azure, Service Fabric usa un certificato x509 per proteggere il cluster e i relativi endpoint, autenticare i client e crittografare i dati. Per proteggere l'accesso agli endpoint di gestione, è anche consigliabile usare Azure Active Directory. Prima di creare il cluster, è necessario creare i tenant e gli utenti di Azure AD.  Per altre informazioni, vedere [Set up Azure AD to authenticate clients](service-fabric-cluster-creation-setup-aad.md) (Configurare Azure AD per autenticare i client).

Prima di distribuire un cluster di produzione per l'esecuzione di carichi di lavoro di produzione, assicurarsi di leggere l'[Elenco di controllo per l'idoneità per la produzione](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager
Tra gli [esempi di Azure su GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) sono disponibili alcuni modelli di Resource Manager di esempio. Questi modelli possono essere usati come punto di partenza per il modello del cluster.

Questo articolo usa il modello di esempio di un [cluster protetto a cinque nodi][service-fabric-secure-cluster-5-node-1-nodetype] e i relativi parametri. Scaricare *azuredeploy.json* e *azuredeploy.parameters.json* sul computer e aprire entrambi i file in un editor di testo.

> [!NOTE]
> Per i cloud nazionali (Azure per enti pubblici, Azure Cina, Azure Germania), aggiungere anche i valori `fabricSettings` seguenti al modello: `AADLoginEndpoint`, `AADTokenEndpointFormat` e `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Aggiungere certificati
I certificati vengono aggiunti a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. Aggiungere i parametri e i valori dell'insieme di credenziali delle chiavi in un file di parametri del modello di Resource Manager (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Aggiungere tutti i certificati all'elemento osProfile del set di scalabilità di macchine virtuali
Ogni certificato che viene installato nel cluster deve essere configurato nella sezione **osProfile** della risorsa del set di scalabilità (Microsoft.Compute/virtualMachineScaleSets). Questa azione indica al provider di risorse di installare il certificato nelle macchine virtuali, includendo sia il certificato del cluster che eventuali certificati di sicurezza dell'applicazione che si intende usare per le applicazioni:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurare il certificato del cluster di Service Fabric

Il certificato di autenticazione del cluster deve essere configurato sia nella risorsa del cluster di Service Fabric (Microsoft.ServiceFabric/clusters) che nell'estensione di Service Fabric per i set di scalabilità di macchine virtuali nella risorsa del set di scalabilità di macchine virtuali. Questa disposizione consente al provider di risorse di Service Fabric di configurarlo per l'uso dell'autenticazione del cluster e del server per gli endpoint di gestione.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Aggiungere le informazioni sul certificato alla risorsa del set di scalabilità di macchine virtuali

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Aggiungere le informazioni sul certificato alla risorsa del cluster di Service Fabric

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Aggiungere la configurazione di Azure AD per l'uso di Azure AD per l'accesso client

La configurazione di Azure AD viene aggiunta a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi contenente le chiavi del certificato. Aggiungere i parametri e i valori di Azure AD in un file di parametri del modello di Resource Manager (*azuredeploy.parameters.json*). 

> [!NOTE]
> Prima di creare il cluster, è necessario creare i tenant e gli utenti di Azure AD.  Per altre informazioni, vedere [Configurare Azure AD per autenticare i client](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Compilare il file dei parametri con i valori

Infine, usare i valori di output dei comandi di PowerShell per Azure AD e dell'insieme di credenziali delle chiavi per compilare il file dei parametri.

Se si prevede di usare i moduli RM di PowerShell di Azure Service Fabric, non è necessario compilare le informazioni sul certificato del cluster. Se si vuole che il sistema generi il certificato autofirmato per la sicurezza del cluster, mantenere i valori null. 

> [!NOTE]
> Se si vuole che i moduli RM selezionino e compilino i valori dei parametri vuoti, i nomi dei parametri devono corrispondere a quelli riportati di seguito.

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Se si usano certificati dell'applicazione o un cluster esistente caricato nell'insieme di credenziali delle chiavi, è necessario ottenere queste informazioni e specificarle nel file.

I moduli RM non hanno la capacità di generare la configurazione di Azure AD automaticamente, pertanto se si prevede di usare Azure AD per l'accesso client, è necessario compilarla.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testare il modello
Usare il comando PowerShell seguente per testare il modello di Resource Manager con un file di parametri:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e vengano visualizzati messaggi di difficile interpretazione, usare "-Debug" come opzione.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Il diagramma seguente illustra i punti in cui la configurazione dell'insieme di credenziali delle chiavi e di Azure AD rientra nel modello di Resource Manager.

![Mappa di dipendenza di Resource Manager][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un modello per il cluster, vedere come [distribuire il cluster in Azure](service-fabric-cluster-creation-via-arm.md).  Se non è già stato fatto, prima di distribuire un cluster di produzione, leggere l'[Elenco di controllo per l'idoneità per la produzione](service-fabric-production-readiness-checklist.md).

Per informazioni sulla sintassi e le proprietà JSON per le risorse distribuite in questo articolo, vedere:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
