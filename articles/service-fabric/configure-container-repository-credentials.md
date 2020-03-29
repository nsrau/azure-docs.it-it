---
title: Azure Service Fabric - Configurare le credenziali del repository del contenitoreAzure Service Fabric - Configure container repository credentials
description: Configurare le credenziali del repository per scaricare le immagini dal Registro di sistema del contenitoreConfigure repository credentials to download images from container registry
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934988"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurare le credenziali del repository per l'applicazione per il download delle immagini del contenitoreConfigure repository credentials for your application to download container images

Configurare l'autenticazione del Registro di sistema del contenitore aggiungendo `RepositoryCredentials` alla sezione del `ContainerHostPolicies` manifesto dell'applicazione. Aggiungere l'account e la password per il registro contenitori *(myregistry.azurecr.io* nell'esempio seguente), che consente al servizio di scaricare l'immagine del contenitore dal repository.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

È consigliabile crittografare la password del repository utilizzando un certificato di cifratura distribuito in tutti i nodi del cluster. Quando Service Fabric distribuisce il pacchetto del servizio nel cluster, il certificato di crittografia viene usato per decrittografare il testo crittografato. Il cmdlet Invoke-ServiceFabricEncryptText viene usato per creare il testo crittografato della password, che viene aggiunto al file ApplicationManifest.xml.
Per altre informazioni sui certificati e la semantica di crittografia, vedere [Gestione segreta.](service-fabric-application-secret-management.md)

## <a name="configure-cluster-wide-credentials"></a>Configurare credenziali a livello di cluster

Service Fabric consente di configurare credenziali a livello di cluster che possono essere utilizzate come credenziali di repository predefinite dalle applicazioni.

Questa funzionalità può essere abilitata `UseDefaultRepositoryCredentials` o `ContainerHostPolicies` disabilitata aggiungendo l'attributo in ApplicationManifest.xml con un `true` valore o `false` .

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric utilizza quindi le credenziali del repository predefinito che `Hosting` possono essere specificate in ClusterManifest nella sezione.  Se `UseDefaultRepositoryCredentials` è `true`, Service Fabric legge i valori seguenti da ClusterManifest:

* DefaultContainerRepositoryAccountName (stringa)
* DefaultContainerRepositoryPassword (stringa)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (stringa)

Ecco un esempio di ciò `Hosting` che può essere aggiunto all'interno della sezione nel file ClusterManifestTemplate.json. La `Hosting` sezione può essere aggiunta durante la creazione del cluster o in un secondo momento in un aggiornamento della configurazione. Per altre informazioni, vedere [Personalizzare le impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gestire i segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Usare i token come credenziali del Registro di sistemaUse tokens as registry credentials

Service Fabric supporta l'uso di token come credenziali per scaricare le immagini per i contenitori.  Questa funzionalità sfrutta *l'identità gestita* del set di scalabilità della macchina virtuale sottostante per l'autenticazione nel Registro di sistema, eliminando la necessità di gestire le credenziali utente.  Per altre info, [vedere Identità gestite per le risorse di Azure.See Managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md) for more info.  L'utilizzo di questa funzionalità richiede i seguenti passaggi:

1. Verificare che *l'identità gestita assegnata dal* sistema sia abilitata per la macchina virtuale.

    ![Portale di Azure: opzione di identità del set di scalabilità delle macchine virtualiAzure portal: Create virtual machine scale set identity option](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Concedere le autorizzazioni al set di scalabilità della macchina virtuale per il pull/lettura di immagini dal Registro di sistema. Dal pannello Controllo di accesso (IAM) del Registro di sistema del contenitore di Azure nel portale di Azure aggiungere *un'assegnazione* di ruolo per la macchina virtuale:From the Access Control (IAM) blade of your Azure Container Registry in the Azure portal, add a role assignment for your virtual machine:

    ![Aggiungere l'entità macchina virtuale ad ACRAdd VM principal to ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Modificare quindi il manifesto dell'applicazione. Nella `ContainerHostPolicies` sezione aggiungere l'attributo `‘UseTokenAuthenticationCredentials=”true”`.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Il `UseDefaultRepositoryCredentials` flag impostato `UseTokenAuthenticationCredentials` su true while è true causerà un errore durante la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'autenticazione del Registro di sistema Container](../container-registry/container-registry-authentication.md).
