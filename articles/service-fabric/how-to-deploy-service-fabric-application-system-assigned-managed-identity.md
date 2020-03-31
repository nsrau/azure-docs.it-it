---
title: Distribuire un'app Service Fabric con MI assegnato dal sistemaDeploy a Service Fabric app with system-assigned MI
description: Questo articolo illustra come assegnare un'identità gestita assegnata dal sistema a un'applicazione azure Service Fabric
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614826"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Distribuire l'applicazione Service Fabric con l'identità gestita assegnata dal sistema (anteprima)Deploy Service Fabric application with system-assigned managed identity (preview)

Per accedere alla funzionalità di identità gestita per le applicazioni di Azure Service Fabric, è innanzitutto necessario abilitare il servizio token di identità gestita nel cluster. Questo servizio è responsabile dell'autenticazione delle applicazioni di Service Fabric che usano le identità gestite e dell'ottenimento dei token di accesso per loro conto. Una volta abilitato il servizio, è possibile visualizzarlo in Service Fabric Explorer nella sezione **Sistema** nel riquadro sinistro, in esecuzione sotto il nome **fabric:/System/ManagedIdentityTokenService** accanto ad altri servizi di sistema.

> [!NOTE] 
> La distribuzione di applicazioni Service Fabric con identità `"2019-06-01-preview"`gestite è supportata a partire dalla versione DELL'API . È anche possibile usare la stessa versione dell'API per il tipo di applicazione, la versione del tipo di applicazione e le risorse del servizio. Il runtime minimo supportato di Service Fabric è 6.5 CU2. In additoin, l'ambiente di compilazione / pacchetto deve avere anche SF .Net SDK a CU2 o versione successiva

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

### <a name="application-template"></a>Modello di applicazione

Per abilitare l'applicazione con un'identità gestita assegnata dal sistema, aggiungere la proprietà identity alla risorsa dell'applicazione, con il tipo **systemAssigned,** come illustrato nell'esempio seguente:To enable application with a system-assigned managed identity, add the **identity** property to the application resource, with type systemAssigned as shown in the example below:

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
Questa proprietà dichiara (in Azure Resource Manager e i provider di risorse per l'identità`system assigned`gestita e l'infrastruttura del servizio, rispettivamente, che questa risorsa deve avere un'identità implicita ( ) gestita.

### <a name="application-and-service-package"></a>Pacchetto di applicazioni e servizi

1. Aggiornare il manifesto dell'applicazione per aggiungere un elemento ManagedIdentity nella sezione **Principals,** contenente una singola voce, come illustrato di seguito:Update the application manifest to add **a ManagedIdentity** element in the Principals section, containing a single entry as shown below:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    In questo modo viene eseguito il mapping dell'identità assegnata all'applicazione come risorsa a un nome descrittivo, per un'ulteriore assegnazione ai servizi che comprendono l'applicazione. 

2. Nella sezione **ServiceManifestImport** corrispondente al servizio a cui viene assegnata l'identità gestita aggiungere un elemento **IdentityBindingPolicy,** come indicato di seguito:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Questo elemento assegna l'identità dell'applicazione al servizio; senza questa assegnazione, il servizio non sarà in grado di accedere all'identità dell'applicazione. Nel frammento di `SystemAssigned` codice precedente, l'identità (che è una parola chiave `WebAdmin`riservata) viene mappata alla definizione del servizio sotto il nome descrittivo .

3. Aggiornare il manifesto del servizio per aggiungere un elemento **ManagedIdentity** `ServiceIdentityRef` all'interno `IdentityBindingPolicy` della sezione **Resources** con il nome corrispondente al valore dell'impostazione della definizione nel manifesto dell'applicazione:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Si tratta del mapping equivalente di un'identità a un servizio come descritto in precedenza, ma dal punto di vista della definizione del servizio. All'identità viene fatto riferimento`WebAdmin`qui dal relativo nome descrittivo ( ), come dichiarato nel manifesto dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Esaminare il supporto delle [identità gestite](./concepts-managed-identity.md) in Azure Service FabricReview managed identity support in Azure Service Fabric
* [Distribuire un nuovo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster di Azure Service Fabric con supporto delle identità gestiteAzure Service Fabric cluster with managed identity support 
* [Abilitare l'identità gestita](./configure-existing-cluster-enable-managed-identity-token-service.md) in un cluster di Azure Service Fabric esistenteEnable managed identity in an existing Azure Service Fabric cluster
* Sfruttare [l'identità gestita](./how-to-managed-identity-service-fabric-app-code.md) di un'applicazione Service Fabric dal codice sorgente
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Concedere a un'applicazione di Azure Service Fabric l'accesso ad altre risorse di AzureGrant an Azure Service Fabric application access to other Azure resources](./how-to-grant-access-other-resources.md)
