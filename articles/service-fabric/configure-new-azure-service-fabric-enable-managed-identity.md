---
title: Configurare il supporto delle identità gestite per un nuovo cluster di Service FabricConfigure managed identity support for a new Service Fabric cluster
description: Ecco come abilitare il supporto delle identità gestite in un nuovo cluster di Azure Service FabricHere's how to enable managed identities support in a new Azure Service Fabric cluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930472"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Configurare il supporto delle identità gestite per un nuovo cluster di Service Fabric (anteprima)Configure managed identity support for a new Service Fabric cluster (preview)

Per usare [le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md) di Azure nelle applicazioni di Service Fabric, abilitare innanzitutto il servizio token di *identità gestita* nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni di Service Fabric che usano le identità gestite e dell'ottenimento dei token di accesso per loro conto. Una volta abilitato il servizio, è possibile visualizzarlo in Service Fabric Explorer nella sezione **Sistema** nel riquadro sinistro, in esecuzione sotto il nome **fabric:/System/ManagedIdentityTokenService** accanto ad altri servizi di sistema.

> [!NOTE]
> Per abilitare il servizio token di **identità gestita,** è necessaria la versione 6.5.658.9590 o successiva di Service Fabric.  

## <a name="enable-the-managed-identity-token-service"></a>Abilitare il servizio token di identità gestitaEnable the Managed Identity Token Service

Per abilitare il servizio token di identità gestita al momento della creazione del cluster, aggiungere il frammento di codice seguente al modello di Azure Resource Manager del cluster:To enable the Managed Identity Token Service at cluster creation time, add the following snippet to your cluster Azure Resource Manager template:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Se la distribuzione non riesce con questo messaggio, significa che il cluster non è nella versione di Service Fabric richiesta (il runtime minimo supportato è 6.5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Articoli correlati

* Esaminare il supporto delle [identità gestite](./concepts-managed-identity.md) in Azure Service FabricReview managed identity support in Azure Service Fabric

* [Abilitare il supporto delle identità gestite in un cluster di Azure Service Fabric esistenteEnable managed identity support in an existing Azure Service Fabric cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dal sistemaDeploy an Azure Service Fabric application with a system-assigned managed identity](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
* [Concedere a un'applicazione di Azure Service Fabric l'accesso ad altre risorse di AzureGrant an Azure Service Fabric application access to other Azure resources](./how-to-grant-access-other-resources.md)