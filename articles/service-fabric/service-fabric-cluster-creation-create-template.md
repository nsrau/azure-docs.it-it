---
title: Creare un modello di cluster di Service Fabric di Azure
description: Informazioni su come creare un modello di Gestione risorse per un cluster di Service Fabric. Configurare sicurezza, Azure Key Vault e Azure Active Directory (Azure AD) per l'autenticazione client.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: a00f2bc505acd89d9fb9488565b6235bf7d146ba
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396659"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creare un modello di Gestione risorse di Service Fabric cluster

Un [cluster di Azure Service Fabric](service-fabric-deploy-anywhere.md) è un set di macchine virtuali connesso alla rete in cui vengono distribuiti e gestiti i microservizi. Un cluster Service Fabric in esecuzione in Azure è una risorsa di Azure e viene distribuito, gestito e monitorato usando il Gestione risorse.  Questo articolo descrive come creare un modello di Gestione risorse per un cluster Service Fabric in esecuzione in Azure.  Una volta completato il modello, è possibile [distribuire il cluster in Azure](service-fabric-cluster-creation-via-arm.md).

La sicurezza del cluster viene configurata al momento della prima configurazione del cluster e non può essere modificata in un secondo momento. Prima di configurare un cluster, leggere [Service Fabric scenari di sicurezza del cluster][service-fabric-cluster-security]. In Azure Service Fabric usa il certificato X509 per proteggere il cluster e i relativi endpoint, autenticare i client e crittografare i dati. Per proteggere l'accesso agli endpoint di gestione è inoltre consigliabile Azure Active Directory. Prima di creare il cluster, è necessario creare Azure AD tenant e utenti.  Per altre informazioni, vedere [configurare Azure ad per autenticare i client](service-fabric-cluster-creation-setup-aad.md).

Prima di distribuire un cluster di produzione per l'esecuzione di carichi di lavoro di produzione, assicurarsi di leggere prima l' [elenco di controllo della conformità alla produzione](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Creare il modello di Gestione risorse
Modelli di Gestione risorse di esempio sono disponibili negli [esempi di Azure su GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Questi modelli possono essere usati come punto di partenza per il modello di cluster.

Questo articolo usa il modello di esempio di [cluster sicuro a cinque nodi][service-fabric-secure-cluster-5-node-1-nodetype] e i parametri del modello. Scaricare *file azuredeploy. JSON* e *file azuredeploy. Parameters. JSON* nel computer e aprire entrambi i file nell'editor di testo preferito.

> [!NOTE]
> Per i cloud nazionali (Azure per enti pubblici, Azure Cina, Azure Germania), è necessario aggiungere anche i `fabricSettings` seguenti al modello: `AADLoginEndpoint`, `AADTokenEndpointFormat` e `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Aggiungi certificati
Per aggiungere certificati a un cluster Gestione risorse modello, fare riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. Aggiungere i parametri e i valori dell'insieme di credenziali delle chiavi in un file di parametri del modello di Gestione risorse (*file azuredeploy. Parameters. JSON*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Aggiungere tutti i certificati al set di scalabilità di macchine virtuali osProfile
Ogni certificato installato nel cluster deve essere configurato nella sezione **osProfile** della risorsa del set di scalabilità (Microsoft. Compute/virtualMachineScaleSets). Questa azione indica al provider di risorse di installare il certificato nelle macchine virtuali. Questa installazione include sia il certificato del cluster sia i certificati di sicurezza dell'applicazione che si prevede di usare per le applicazioni:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurare il certificato del cluster Service Fabric

Il certificato di autenticazione del cluster deve essere configurato sia in Service Fabric risorsa cluster (Microsoft. ServiceFabric/clusters) che nell'estensione Service Fabric per i set di scalabilità di macchine virtuali nella risorsa del set di scalabilità di macchine virtuali. Questa disposizione consente al provider di risorse Service Fabric di configurarlo per l'uso dell'autenticazione del cluster e dell'autenticazione server per gli endpoint di gestione.

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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Aggiungere le informazioni sul certificato alla risorsa cluster Service Fabric

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Aggiungere Azure AD configurazione per usare Azure AD per l'accesso client

Aggiungere la configurazione di Azure AD a un cluster Gestione risorse modello facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. Aggiungere i parametri e i valori Azure AD in un file di parametri del modello di Gestione risorse (*file azuredeploy. Parameters. JSON*). 

> [!NOTE]
> In Linux è necessario creare Azure AD tenant e utenti prima di creare il cluster.  Per altre informazioni, vedere [configurare Azure ad per autenticare i client](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Popolare il file dei parametri con i valori

Infine, usare i valori di output dell'insieme di credenziali delle chiavi e Azure AD comandi di PowerShell per popolare il file dei parametri.

Se si prevede di usare i moduli RM di PowerShell per Azure Service Fabric, non è necessario popolare le informazioni sul certificato del cluster. Se si vuole che il sistema generi il certificato autofirmato per la sicurezza del cluster, è sufficiente mantenerli come null. 

> [!NOTE]
> Affinché i moduli RM rilevino e popolano questi valori di parametro vuoti, i nomi dei parametri corrispondono ai nomi seguenti

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

Se si usano i certificati dell'applicazione o si usa un cluster esistente caricato nell'insieme di credenziali delle chiavi, è necessario ottenere queste informazioni e popolarle.

I moduli RM non hanno la possibilità di generare automaticamente la configurazione di Azure AD, quindi se si prevede di usare il Azure AD per l'accesso client, è necessario popolarlo.

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
Usare il comando di PowerShell seguente per testare il modello di Gestione risorse con un file di parametri:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e si ottengano messaggi criptici, usare "-debug" come opzione.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Il diagramma seguente illustra il punto in cui l'insieme di credenziali delle chiavi e la configurazione Azure AD rientrano nel modello di Gestione risorse.

![Mappa delle dipendenze Gestione risorse][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Passaggi successivi
Ora che è disponibile un modello per il cluster, vedere come [distribuire il cluster in Azure](service-fabric-cluster-creation-via-arm.md).  Se non è già stato fatto, leggere l' [elenco di controllo di conformità](service-fabric-production-readiness-checklist.md) per la produzione prima di distribuire un cluster di produzione.

Per informazioni sulla sintassi e sulle proprietà JSON per le risorse distribuite in questo articolo, vedere:

* [Microsoft. ServiceFabric/cluster](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft. Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
