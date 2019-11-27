---
title: Azure Service Fabric-configurare le credenziali del repository del contenitore | Microsoft Docs
description: Configurare le credenziali del repository per scaricare immagini dal registro contenitori
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
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurare le credenziali del repository per l'applicazione per scaricare le immagini del contenitore

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

È consigliabile crittografare la password del repository usando un certificato di crittografia distribuito in tutti i nodi del cluster. Quando Service Fabric distribuisce il pacchetto del servizio nel cluster, il certificato di crittografia viene usato per decrittografare il testo crittografato. Il cmdlet Invoke-ServiceFabricEncryptText viene usato per creare il testo crittografato della password, che viene aggiunto al file ApplicationManifest.xml.
Per ulteriori informazioni sui certificati e sulla semantica di crittografia, vedere [gestione dei segreti](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>Configurare credenziali a livello di cluster

Service Fabric consente di configurare le credenziali a livello di cluster che possono essere usate come credenziali predefinite del repository da applicazioni.

Questa funzionalità può essere abilitata o disabilitata aggiungendo l'attributo `UseDefaultRepositoryCredentials` `ContainerHostPolicies` in ApplicationManifest. XML con un valore `true` o `false`.

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

Service Fabric usa quindi le credenziali predefinite del repository che è possibile specificare in ClusterManifest nella sezione `Hosting`.  Se `UseDefaultRepositoryCredentials` è `true`, Service Fabric legge i valori seguenti da ClusterManifest:

* DefaultContainerRepositoryAccountName (stringa)
* DefaultContainerRepositoryPassword (stringa)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (stringa) - Supportato a partire dal runtime 6.4

Di seguito è riportato un esempio di ciò che è possibile aggiungere all'interno della sezione `Hosting` nel file ClusterManifestTemplate. JSON. La sezione `Hosting` può essere aggiunta in fase di creazione del cluster o in un secondo momento in un aggiornamento della configurazione. Per altre informazioni, vedere [Personalizzare le impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gestire i segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Uso dell'identità gestita del set di scalabilità di macchine virtuali tramite il servizio identità gestito (MSI)

Service Fabric supporta l'uso di token come credenziali per scaricare immagini per i contenitori.  Questa funzionalità sfrutta l'identità gestita del set di scalabilità di macchine virtuali sottostante per l'autenticazione nel registro di sistema, eliminando la necessità di gestire le credenziali utente.  Per ulteriori informazioni su MSI, vedere [identità del servizio gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .  Per usare questa funzionalità, è necessario eseguire i passaggi seguenti:

1.  Verificare che l'identità gestita assegnata dal sistema sia abilitata per la macchina virtuale (vedere la schermata riportata di seguito)

    ![Crea identità del set di scalabilità di macchine virtuali](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Successivamente, concedere le autorizzazioni alla VM (SS) per eseguire il pull/leggere le immagini dal registro di sistema.  Passare a controllo di accesso (IAM) del pannello ACR tramite Azure e assegnare alla VM (SS) le autorizzazioni corrette, come illustrato di seguito:

    ![Aggiungi entità macchina virtuale a ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Una volta completati i passaggi precedenti, modificare il file ApplicationManifest. XML.  Trovare il tag denominato "ContainerHostPolicies" e aggiungere l'attributo `‘UseTokenAuthenticationCredentials=”true”`.

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
    > Il flag `UseDefaultRepositoryCredentials` impostato su true, mentre `UseTokenAuthenticationCredentials` è true causerà un errore durante la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Vedere altre informazioni sull' [autenticazione del registro contenitori](/azure/container-registry/container-registry-authentication).
