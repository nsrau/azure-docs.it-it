---
title: Configurare il supporto di identità gestite in un cluster di Service Fabric esistente
description: Ecco come abilitare il supporto per le identità gestite in un cluster di Azure Service Fabric esistente
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 722c507300cc5766d162f336f77f60293c5c90dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257609"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Configurare il supporto di identità gestite in un cluster di Service Fabric esistente

Per usare le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) nelle applicazioni Service Fabric, abilitare prima il *servizio token di identità gestito* nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni Service Fabric usando le identità gestite e per ottenere i token di accesso per loro conto. Quando il servizio è abilitato, è possibile visualizzarlo in Service Fabric Explorer nella sezione **sistema** nel riquadro sinistro, in esecuzione con il nome **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Per abilitare il **servizio token di identità gestito**, è necessario Service Fabric versione di runtime 6.5.658.9590 o successiva.  
>
> È possibile trovare la versione Service Fabric di un cluster dalla portale di Azure aprendo la risorsa cluster e controllando la proprietà **versione Service Fabric** **nella sezione informazioni** di base.
>
> Se il cluster è in modalità di aggiornamento **manuale** , è necessario prima aggiornarlo a 6.5.658.9590 o versione successiva.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Abilitare il *servizio token di identità gestito* in un cluster esistente

Per abilitare il servizio token di identità gestito in un cluster esistente, è necessario avviare un aggiornamento del cluster specificando due modifiche: (1) abilitando il servizio token di identità gestito e (2) richiedendo un riavvio di ogni nodo. Per prima cosa, aggiungere il frammento di codice seguente Azure Resource Manager modello di cluster:

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

Per rendere effettive le modifiche, sarà anche necessario modificare i criteri di aggiornamento per specificare un riavvio forzato del runtime di Service Fabric in ogni nodo durante l'avanzamento dell'aggiornamento attraverso il cluster. Questo riavvio garantisce che il servizio di sistema appena abilitato venga avviato e in esecuzione in ogni nodo. Nel frammento di codice seguente `forceRestart` è l'impostazione essenziale per abilitare il riavvio. Per i parametri rimanenti, usare i valori descritti di seguito o usare i valori personalizzati esistenti già specificati per la risorsa cluster. Le impostazioni personalizzate per i criteri di aggiornamento dell'infrastruttura (' upgradeDescription ') possono essere visualizzate nel portale di Azure selezionando l'opzione ' aggiornamenti dell'infrastruttura ' nella risorsa Service Fabric o in resources.azure.com. Le opzioni predefinite per i criteri di aggiornamento (' upgradeDescription ') non sono visualizzabili da PowerShell o da resources.azure.com. Per ulteriori informazioni, vedere [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) .  

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
> Al termine dell'aggiornamento, non dimenticare di eseguire il rollback dell' `forceRestart` impostazione per ridurre al minimo l'effetto degli aggiornamenti successivi. 

## <a name="errors-and-troubleshooting"></a>Errori e risoluzione dei problemi

Se la distribuzione ha esito negativo con il messaggio seguente, significa che il cluster non è in esecuzione in una versione di Service Fabric sufficientemente elevata:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)
