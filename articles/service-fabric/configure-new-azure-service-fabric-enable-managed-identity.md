---
title: Azure Service Fabric-distribuire un nuovo cluster di Azure Service Fabric con supporto per identità gestite | Microsoft Docs
description: Questo articolo illustra come creare un nuovo cluster di Service Fabric con identità gestita abilitata
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cb32251c2507f3def7f5e830af15828ff2f04c29
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640712"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Creare un nuovo cluster di Azure Service Fabric con supporto di identità gestito (anteprima)

Per accedere alla funzionalità di identità gestita per le applicazioni Azure Service Fabric, è necessario abilitare prima il servizio token di identità gestito nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni Service Fabric usando le identità gestite e per ottenere i token di accesso per loro conto. Quando il servizio è abilitato, è possibile visualizzarlo in Service Fabric Explorer nella sezione **sistema** nel riquadro sinistro, in esecuzione con il nome **Fabric:/System/ManagedIdentityTokenService** accanto ad altri servizi di sistema.

> [!NOTE]
> Per abilitare il **servizio token di identità gestito**, è necessario Service Fabric versione di runtime 6.5.658.9590 o successiva.  

## <a name="enable-the-managed-identity-token-service"></a>Abilitare il servizio token di identità gestito 
Per abilitare il servizio token di identità gestito al momento della creazione del cluster, è possibile usare il frammento di codice seguente in un modello di Azure Resource Manager:

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

Se la distribuzione ha esito negativo con questo messaggio, significa che il cluster non si trova nella versione Service Fabric richiesta (il runtime minimo supportato è 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```


## <a name="related-articles"></a>Articoli correlati
* Esaminare il [supporto di identità gestite](./concepts-managed-identity.md) in Azure Service Fabric

* [Abilitare il supporto di identità gestite in un cluster di Azure Service Fabric esistente](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)