---
title: Azure Service Fabric-configurare un cluster di Azure Service Fabric esistente per abilitare il supporto per le identità gestite | Microsoft Docs
description: Questo articolo illustra come configurare un cluster di Azure Service Fabric esistente per abilitare il supporto per le identità gestite
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: adc21358011454c8687998dc5d257052959b933b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640742"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Configurare un cluster di Azure Service Fabric esistente per abilitare il supporto per le identità gestite (anteprima)
Per accedere alla funzionalità di identità gestita per le applicazioni Azure Service Fabric, è necessario abilitare prima il **servizio token di identità gestito** nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni Service Fabric usando le identità gestite e per ottenere i token di accesso per loro conto. Quando il servizio è abilitato, è possibile visualizzarlo in Service Fabric Explorer nella sezione **sistema** nel riquadro sinistro, in esecuzione con il nome **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Per abilitare il **servizio token di identità gestito**, è necessario Service Fabric versione di runtime 6.5.658.9590 o successiva.  
> 
> È possibile trovare la versione Service Fabric di un cluster dalla portale di Azure aprendo la risorsa cluster e controllando la proprietà **versione Service Fabric** nella sezione informazioni di base.
> 
> Se il cluster è in modalità di aggiornamento **manuale** , è necessario prima aggiornarlo a 6.5.658.9590 o versione successiva.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Abilitare il servizio token di identità gestito in un cluster esistente
Per abilitare il servizio token di identità gestito in un cluster esistente, è necessario avviare un aggiornamento del cluster specificando due modifiche: abilitazione del servizio token di identità gestito e richiesta di riavvio di ciascun nodo. A tale scopo, aggiungere i due frammenti di codice seguenti nel modello di Azure Resource Manager:

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

Per rendere effettive le modifiche, sarà anche necessario modificare i criteri di aggiornamento per specificare un riavvio forzato del runtime di Service Fabric in ogni nodo durante l'avanzamento dell'aggiornamento attraverso il cluster. Questo riavvio garantisce che il servizio di sistema appena abilitato venga avviato e in esecuzione in ogni nodo. Nel frammento di codice `forceRestart` riportato di seguito è l'impostazione essenziale. utilizzare i valori esistenti per il resto delle impostazioni.  

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