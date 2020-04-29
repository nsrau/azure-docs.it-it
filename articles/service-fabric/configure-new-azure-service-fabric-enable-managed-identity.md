---
title: Configurare il supporto di identità gestite per un nuovo cluster di Service Fabric
description: Ecco come abilitare il supporto per le identità gestite in un nuovo cluster di Service Fabric di Azure
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415661"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Configurare il supporto di identità gestite per un nuovo cluster di Service Fabric

Per usare le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) nelle applicazioni Service Fabric, abilitare prima il *servizio token di identità gestito* nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni Service Fabric usando le identità gestite e per ottenere i token di accesso per loro conto. Quando il servizio è abilitato, è possibile visualizzarlo in Service Fabric Explorer nella sezione **sistema** nel riquadro sinistro, in esecuzione con il nome **Fabric:/System/ManagedIdentityTokenService** accanto ad altri servizi di sistema.

> [!NOTE]
> Per abilitare il **servizio token di identità gestito**, è necessario Service Fabric versione di runtime 6.5.658.9590 o successiva.  

## <a name="enable-the-managed-identity-token-service"></a>Abilitare il servizio token di identità gestito

Per abilitare il servizio token di identità gestito al momento della creazione del cluster, aggiungere il frammento di codice seguente al modello di Azure Resource Manager del cluster:

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