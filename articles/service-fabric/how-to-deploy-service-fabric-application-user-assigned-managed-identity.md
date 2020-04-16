---
title: Distribuire un'app con un'identità gestita assegnata dall'utenteDeploy app with a user-assigned managed identity
description: Questo articolo illustra come distribuire l'applicazione Service Fabric con un'identità gestita assegnata dall'utente
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415637"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Distribuire l'applicazione Service Fabric con un'identità gestita assegnata dall'utenteDeploy Service Fabric application with a User-Assigned Managed Identity

Per distribuire un'applicazione di Service Fabric con identità gestita, l'applicazione deve essere distribuita tramite Azure Resource Manager, in genere con un modello di Azure Resource Manager.To deploy a Service Fabric application with managed identity, the application needs to be deployed through Azure Resource Manager, typically with an Azure Resource Manager template. Per altre informazioni su come distribuire l'applicazione Service Fabric tramite Azure Resource Manager, vedere Gestire applicazioni e servizi come risorse di Azure Resource Manager.For more information on how to deploy Service Fabric application through Azure Resource Manager, see [Manage applications and services as Azure Resource Manager resources.](service-fabric-application-arm-resource.md)

> [!NOTE] 
> 
> Le applicazioni che non vengono distribuite come risorsa di Azure **non possono** avere identità gestite. 
>
> La distribuzione dell'applicazione Service Fabric `"2019-06-01-preview"`con identità gestita è supportata con la versione API. È anche possibile usare la stessa versione dell'API per il tipo di applicazione, la versione del tipo di applicazione e le risorse del servizio.
>

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Per abilitare l'applicazione con identità assegnata dall'utente, aggiungere innanzitutto la proprietà **identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate dall'utente a cui si fa riferimento. Aggiungere quindi una sezione **managedIdentities** all'interno della sezione **delle proprietà** per la risorsa **dell'applicazione** che contiene un elenco di nome descrittivo per il mapping principalId per ognuna delle identità assegnate dall'utente. Per ulteriori informazioni sulle identità assegnate dall'utente, vedere [Creare, elencare o eliminare un'identità gestita assegnata dall'utente.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)

### <a name="application-template"></a>Modello di applicazione

Per abilitare l'applicazione con identità assegnata dall'utente, aggiungere innanzitutto la proprietà **identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate dall'utente a cui si fa riferimento, quindi aggiungere un oggetto **managedIdentities** all'interno della sezione **delle proprietà** che contiene un elenco di nome descrittivo al mapping principalId per ognuna delle identità assegnate all'utente.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

Nell'esempio precedente il nome di risorsa dell'identità assegnata dall'utente viene utilizzato come nome descrittivo dell'identità gestita per l'applicazione. Negli esempi seguenti si presuppone che il nome descrittivo effettivo sia "AdminUser".

### <a name="application-package"></a>Pacchetto dell'applicazione

1. Per ogni identità `managedIdentities` definita nella sezione del modello `<ManagedIdentity>` di Azure Resource Manager, aggiungere un tag nel manifesto dell'applicazione nella sezione **Principals.For** each identity defined in the section in the Azure Resource Manager template, add a tag in the application manifest under Principals section. L'attributo `Name` deve `name` corrispondere alla `managedIdentities` proprietà definita nella sezione.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Nella sezione **ServiceManifestImport** aggiungere un **oggetto IdentityBindingPolicy** per il servizio che usa l'identità gestita. Questo criterio `AdminUser` esegue il mapping dell'identità a un nome di identità specifico del servizio che deve essere aggiunto al manifesto del servizio in un secondo momento.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aggiornare il manifesto del servizio per aggiungere un **ManagedIdentity** `IdentityBindingPolicy` all'interno di **Resources** sezione con il nome corrispondente `ServiceIdentityRef` nel nel manifesto dell'applicazione:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Come usare l'identità gestita nel codice dell'applicazione di Service FabricHow to use Managed Identity in Service Fabric application code](how-to-managed-identity-service-fabric-app-code.md)
* [Come concedere l'accesso all'applicazione di Service Fabric ad altre risorse di AzureHow to Grant Service Fabric application access to other Azure resources](how-to-grant-access-other-resources.md)
