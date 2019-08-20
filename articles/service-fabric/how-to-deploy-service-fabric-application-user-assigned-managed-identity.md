---
title: Azure Service Fabric-distribuire un'applicazione con un'identità gestita assegnata dall'utente | Microsoft Docs
description: Questo articolo illustra come distribuire Service Fabric applicazione con un'identità gestita assegnata dall'utente
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: f8dfaa39f02aefbdda1f34afa5011ce5fadbae49
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624910"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Distribuire Service Fabric applicazione con un'identità gestita assegnata dall'utente (anteprima)

Per distribuire un'applicazione Service Fabric con identità gestita, l'applicazione deve essere distribuita tramite Azure Resource Manager, in genere con un modello di Azure Resource Manager. Per altre informazioni su come distribuire Service Fabric applicazione tramite Azure Resource Manager, vedere [gestire le applicazioni e i servizi come risorse di Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Le applicazioni non distribuite come una risorsa di Azure **non possono** avere identità gestite. 
>
> Service Fabric distribuzione di applicazioni con identità gestita è supportata con la `"2019-06-01-preview"`versione dell'API. È anche possibile usare la stessa versione API per il tipo di applicazione, la versione del tipo di applicazione e le risorse del servizio.
>

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Per abilitare l'applicazione con l'identità assegnata dall'utente, aggiungere prima di tutto la proprietà **Identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate dall'utente a cui si fa riferimento. Aggiungere quindi una sezione **managedIdentities** all'interno della sezione **Properties (proprietà** ) per la risorsa **dell'applicazione** contenente un elenco di nomi descrittivi per il mapping di PrincipalId per ogni identità assegnata dall'utente.

### <a name="application-template"></a>Modello di applicazione

Per abilitare l'applicazione con l'identità assegnata dall'utente, aggiungere prima la proprietà **Identity** alla risorsa dell'applicazione con il tipo **userAssigned** e le identità assegnate all'utente a cui si fa riferimento, quindi aggiungere un oggetto **managedIdentities** all'interno del  **sezione properties** che contiene un elenco di nomi descrittivi per il mapping di PrincipalId per ogni identità assegnata dall'utente.

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

Nell'esempio precedente il nome della risorsa dell'identità assegnata dall'utente viene usato come nome descrittivo dell'identità gestita per l'applicazione. Gli esempi seguenti presuppongono che il nome descrittivo effettivo sia "AdminUser".

### <a name="application-package"></a>Pacchetto dell'applicazione

1. Per ogni identità definita nella `managedIdentities` sezione del modello di Azure Resource Manager, aggiungere un `<ManagedIdentity>` tag nel manifesto dell'applicazione nella sezione **entità** . L' `Name` attributo deve corrispondere alla `name` proprietà definita nella `managedIdentities` sezione.

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Nella sezione **ServiceManifestImport** aggiungere un **IdentityBindingPolicy** per il servizio che usa l'identità gestita. Questo criterio esegue il `AdminUser` mapping dell'identità a un nome di identità specifico del servizio che deve essere aggiunto nel manifesto del servizio in un secondo momento.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aggiornare il manifesto del servizio per aggiungere un **ManagedIdentity** all'interno della sezione Resources con `ServiceIdentityRef` il nome `IdentityBindingPolicy` corrispondente a in del manifesto dell'applicazione:

    **ServiceManifest. XML**

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