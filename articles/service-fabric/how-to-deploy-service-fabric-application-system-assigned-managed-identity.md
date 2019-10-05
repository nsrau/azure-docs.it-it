---
title: Service Fabric di Azure-distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema | Microsoft Docs
description: Questo articolo illustra come assegnare un'identità gestita assegnata dal sistema a un'applicazione Service Fabric di Azure
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cf971d71c2566d91bc5a2490d47521725c62b17d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973415"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Distribuire Service Fabric applicazione con identità gestita assegnata dal sistema (anteprima)

Per accedere alla funzionalità di identità gestita per le applicazioni Azure Service Fabric, è necessario abilitare prima il servizio token di identità gestito nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni Service Fabric usando le identità gestite e per ottenere i token di accesso per loro conto. Quando il servizio è abilitato, è possibile visualizzarlo in Service Fabric Explorer nella sezione **sistema** nel riquadro sinistro, in esecuzione con il nome **Fabric:/System/ManagedIdentityTokenService** accanto ad altri servizi di sistema.

> [!NOTE] 
> La distribuzione di applicazioni Service Fabric con identità gestite è supportata a partire dalla versione API `"2019-06-01-preview"`. È anche possibile usare la stessa versione API per il tipo di applicazione, la versione del tipo di applicazione e le risorse del servizio. Il runtime di Service Fabric minimo supportato è 6,5 CU2. In additoin l'ambiente di compilazione/pacchetto deve avere anche SF .NET SDK a CU2 o versione successiva

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

### <a name="application-template"></a>Modello di applicazione

Per abilitare l'applicazione con un'identità gestita assegnata dal sistema, aggiungere la proprietà **Identity** alla risorsa dell'applicazione, con il tipo **systemAssigned** , come illustrato nell'esempio seguente:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Questa proprietà dichiara (per Azure Resource Manager e i provider di risorse di identità e Service Fabric gestiti, rispettivamente, che la risorsa deve avere un'identità gestita implicita (`system assigned`).

### <a name="application-and-service-package"></a>Pacchetto di applicazione e servizio

1. Aggiornare il manifesto dell'applicazione per aggiungere un elemento **ManagedIdentity** nella sezione **Principals** , che contiene una singola voce, come illustrato di seguito:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Viene eseguito il mapping dell'identità assegnata all'applicazione come risorsa a un nome descrittivo, per un'ulteriore assegnazione ai servizi che costituiscono l'applicazione. 

2. Nella sezione **ServiceManifestImport** che corrisponde al servizio a cui viene assegnata l'identità gestita, aggiungere un elemento **IdentityBindingPolicy** , come indicato di seguito:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Questo elemento assegna l'identità dell'applicazione al servizio. senza questa assegnazione, il servizio non sarà in grado di accedere all'identità dell'applicazione. Nel frammento di codice precedente, l'identità `SystemAssigned` (che è una parola chiave riservata) viene mappata alla definizione del servizio con il nome descrittivo `WebAdmin`.

3. Aggiornare il manifesto del servizio per aggiungere un elemento **ManagedIdentity** nella sezione **Resources** con il nome che corrisponde al valore dell'impostazione `ServiceIdentityRef` della definizione `IdentityBindingPolicy` nel manifesto dell'applicazione:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Si tratta del mapping equivalente di un'identità a un servizio, come descritto in precedenza, ma dal punto di vista della definizione del servizio. Il nome descrittivo (`WebAdmin`) dell'identità viene usato come dichiarato nel manifesto dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Esaminare il [supporto di identità gestite](./concepts-managed-identity.md) in Azure Service Fabric
* [Distribuire un nuovo](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric cluster con supporto di identità gestite 
* [Abilitare l'identità gestita](./configure-existing-cluster-enable-managed-identity-token-service.md) in un cluster di Service Fabric Azure esistente
* Utilizzare un'identità gestita dell'applicazione Service Fabric [dal codice sorgente](./how-to-managed-identity-service-fabric-app-code.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)
