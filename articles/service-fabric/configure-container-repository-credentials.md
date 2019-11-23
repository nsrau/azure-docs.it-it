---
title: Azure Service Fabric - Configure container repository credentials | Microsoft Docs
description: Configure repository credentials to download images from container registry
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: c415739934e2318ea5287d5eed9f8235029b666f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405628"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configure repository credentials for your application to download container images

Configurare l'autenticazione del registro contenitori aggiungendo `RepositoryCredentials` a `ContainerHostPolicies` nel file ApplicationManifest.xml. Aggiungere l'account e la password per il contenitore myregistry.azurecr.io, per consentire al servizio di scaricare l'immagine del contenitore dal repository.

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

It is recommended that you encrypt the repository password by using an encipherment certificate that's deployed to all nodes of the cluster. Quando Service Fabric distribuisce il pacchetto del servizio nel cluster, il certificato di crittografia viene usato per decrittografare il testo crittografato. Il cmdlet Invoke-ServiceFabricEncryptText viene usato per creare il testo crittografato della password, che viene aggiunto al file ApplicationManifest.xml.
See [Secret Management](service-fabric-application-secret-management.md) for more on certificates and encryption semantics.

## <a name="configure-cluster-wide-credentials"></a>Configurare credenziali a livello di cluster

Service Fabric allows you to configure cluster-wide credentials which can be used as default repository credentials by applications.

This feature can be enabled or disabled by adding the `UseDefaultRepositoryCredentials` attribute to `ContainerHostPolicies` in ApplicationManifest.xml with a `true` or `false` value.

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

Service Fabric then uses the default repository credentials which can be specified in the ClusterManifest under the `Hosting` section.  Se `UseDefaultRepositoryCredentials` è `true`, Service Fabric legge i valori seguenti da ClusterManifest:

* DefaultContainerRepositoryAccountName (stringa)
* DefaultContainerRepositoryPassword (stringa)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (stringa) - Supportato a partire dal runtime 6.4

Here is an example of what can be added inside the `Hosting` section in the ClusterManifestTemplate.json file. The `Hosting` section can be added at cluster creation or later in a configuration upgrade. Per altre informazioni, vedere [Personalizzare le impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gestire i segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Leveraging the Managed Identity of the virtual machine scale set by using Managed Identity Service (MSI)

Service Fabric supports using tokens as credentials to download images for your containers.  This feature leverages the managed identity of the underlying virtual machine scale set to authenticate to the registry, eliminating the need for managing user credentials.  See [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) for more on MSI.  Using this feature requires the follows steps:

1.  Ensure that System Assigned Managed Identity is enabled for the VM (see screenshot below)

    ![Create virtual machine scale set identity](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  After that, grant permissions to the VM(SS) to pull/read images from the registry.  Go to Access Control (IAM) of your ACR via Azure Blade and give your VM(SS) the correct permissions, as seen below:

    ![Add VM principal to ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Once the above steps are completed, modify your applicationmanifest.xml file.  Find the tag labeled “ContainerHostPolicies” and add the attribute `‘UseTokenAuthenticationCredentials=”true”`.

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
    > The flag `UseDefaultRepositoryCredentials` set to true while `UseTokenAuthenticationCredentials` is true will cause an error during deployment.

## <a name="next-steps"></a>Passaggi successivi

* See more about [Container registry authentication](/azure/container-registry/container-registry-authentication).
