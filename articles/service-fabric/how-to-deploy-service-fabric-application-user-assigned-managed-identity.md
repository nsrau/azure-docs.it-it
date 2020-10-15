---
title: Distribuire un'app con un'identità gestita assegnata dall'utente
description: Questo articolo illustra come distribuire Service Fabric applicazione con un'identità gestita assegnata dall'utente
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260080"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Distribuire Service Fabric applicazione con un'identità gestita User-Assigned

Per distribuire un'applicazione Service Fabric con identità gestita, l'applicazione deve essere distribuita tramite Azure Resource Manager, in genere con un modello di Azure Resource Manager. Per altre informazioni su come distribuire Service Fabric applicazione tramite Azure Resource Manager, vedere [gestire le applicazioni e i servizi come risorse di Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Le applicazioni non distribuite come una risorsa di Azure **non possono** avere identità gestite. 
>
> Service Fabric distribuzione di applicazioni con identità gestita è supportata con la versione dell'API `"2019-06-01-preview"` . È anche possibile usare la stessa versione API per il tipo di applicazione, la versione del tipo di applicazione e le risorse del servizio.
>

## <a name="user-assigned-identity"></a>Identità User-Assigned

Per abilitare l'applicazione con User-Assigned identità, aggiungere prima di tutto la proprietà **Identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate dall'utente a cui si fa riferimento. Aggiungere quindi una sezione **managedIdentities** all'interno della sezione **Properties (proprietà** ) per la risorsa **dell'applicazione** contenente un elenco di nomi descrittivi per il mapping di PrincipalId per ogni identità assegnata dall'utente. Per altre informazioni sulle identità assegnate dall'utente [, vedere creare, elencare o eliminare un'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

### <a name="application-template"></a>Modello di applicazione

Per abilitare l'applicazione con l'identità assegnata dall'utente, aggiungere prima di tutto la proprietà **Identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate all'utente a cui si fa riferimento, quindi aggiungere un oggetto **managedIdentities** all'interno della sezione **Properties** che contiene un elenco di nomi descrittivi al mapping PrincipalId per ciascuna identità assegnata dall'utente.

```json
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
```

Nell'esempio precedente il nome della risorsa dell'identità assegnata dall'utente viene usato come nome descrittivo dell'identità gestita per l'applicazione. Gli esempi seguenti presuppongono che il nome descrittivo effettivo sia "AdminUser".

### <a name="application-package"></a>Pacchetto dell'applicazione

1. Per ogni identità definita nella `managedIdentities` sezione del modello di Azure Resource Manager, aggiungere un `<ManagedIdentity>` tag nel manifesto dell'applicazione nella sezione **entità** . L' `Name` attributo deve corrispondere alla `name` proprietà definita nella `managedIdentities` sezione.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Nella sezione **ServiceManifestImport** aggiungere un **IdentityBindingPolicy** per il servizio che usa l'identità gestita. Questo criterio esegue il mapping dell' `AdminUser` identità a un nome di identità specifico del servizio che deve essere aggiunto nel manifesto del servizio in un secondo momento.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aggiornare il manifesto del servizio per aggiungere un **ManagedIdentity** all'interno della sezione **Resources** con il nome corrispondente `ServiceIdentityRef` a in `IdentityBindingPolicy` del manifesto dell'applicazione:

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

* [Come usare l'identità gestita nel codice dell'applicazione Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Come concedere l'accesso dell'applicazione Service Fabric ad altre risorse di Azure](how-to-grant-access-other-resources.md)
