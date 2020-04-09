---
title: Configurare il supporto delle identità gestite in un cluster di Service Fabric esistenteConfigure managed identity support in an existing Service Fabric cluster
description: Ecco come abilitare il supporto delle identità gestite in un cluster di Azure Service Fabric esistenteHere's how to enable managed identities support in an existing Azure Service Fabric cluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 396978546b301884087c4ea51e242258d64a6b0b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983807"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Configurare il supporto delle identità gestite in un cluster di Service Fabric esistente (anteprima)Configure managed identity support in an existing Service Fabric cluster (preview)

Per usare [le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md) di Azure nelle applicazioni di Service Fabric, abilitare innanzitutto il servizio token di *identità gestita* nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni di Service Fabric che usano le identità gestite e dell'ottenimento dei token di accesso per loro conto. Una volta abilitato il servizio, è possibile visualizzarlo in Service Fabric Explorer nella sezione **System** nel riquadro sinistro, in esecuzione sotto il nome **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Per abilitare il servizio token di **identità gestita,** è necessaria la versione 6.5.658.9590 o successiva di Service Fabric.  
>
> È possibile trovare la versione di Service Fabric di un cluster dal portale di Azure aprendo la risorsa cluster e controllando la proprietà **Versione di Service Fabric** nella sezione **Essentials.**
>
> Se il cluster è in modalità di aggiornamento **manuale,** sarà necessario aggiornarlo prima a 6.5.658.9590 o versione successiva.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Abilitare il *servizio token di identità gestita* in un cluster esistenteEnable Managed Identity Token Service in an existing cluster

Per abilitare il servizio token di identità gestita in un cluster esistente, è necessario avviare un aggiornamento del cluster specificando due modifiche: (1) Abilitazione del servizio token di identità gestita e (2) la richiesta di un riavvio di ogni nodo. Aggiungere innanzitutto il frammento di codice seguente per il modello di Azure Resource Manager del cluster:First, add the following snippet your cluster Azure Resource Manager template:

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

Affinché le modifiche abbiano effetto, sarà inoltre necessario modificare i criteri di aggiornamento per specificare un riavvio forzato del runtime di Service Fabric in ogni nodo man mano che l'aggiornamento procede nel cluster. Questo riavvio garantisce che il servizio di sistema appena abilitato venga avviato e in esecuzione su ogni nodo. Nel frammento `forceRestart` di codice seguente, è l'impostazione essenziale per abilitare il riavvio. Per i parametri rimanenti, utilizzare i valori descritti di seguito o utilizzare i valori personalizzati esistenti già specificati per la risorsa cluster. Le impostazioni personalizzate per i criteri di aggiornamento dell'infrastruttura ('upgradeDescription') possono essere visualizzate dal portale di Azure selezionando l'opzione 'Fabric Upgrades' nella risorsa Service Fabric o resources.azure.com. Le opzioni predefinite per i criteri di aggiornamento ('upgradeDescription') non sono visualizzabili da PowerShell o resources.azure.com. Per ulteriori informazioni, vedere [ClusterUpgradePolicy.See ClusterUpgradePolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) for additional information.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Al termine dell'aggiornamento, non dimenticare di `forceRestart` eseguire il rollback dell'impostazione, per ridurre al minimo l'impatto degli aggiornamenti successivi. 

## <a name="errors-and-troubleshooting"></a>Errori e risoluzione dei problemi

Se la distribuzione non riesce con il messaggio seguente, significa che il cluster non è in esecuzione su una versione di Service Fabric sufficiente:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dal sistemaDeploy an Azure Service Fabric application with a system-assigned managed identity](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
* [Concedere a un'applicazione di Azure Service Fabric l'accesso ad altre risorse di AzureGrant an Azure Service Fabric application access to other Azure resources](./how-to-grant-access-other-resources.md)
