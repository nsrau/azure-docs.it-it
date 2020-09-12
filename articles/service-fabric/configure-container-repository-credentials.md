---
title: Azure Service Fabric-configurare le credenziali del repository del contenitore
description: Configurare le credenziali del repository per scaricare immagini dal registro contenitori
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 142ede6fcc59063d83854712a966a90c7472923b
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421425"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurare le credenziali del repository per l'applicazione per scaricare le immagini del contenitore

Configurare l'autenticazione del registro contenitori aggiungendo `RepositoryCredentials` alla `ContainerHostPolicies` sezione del manifesto dell'applicazione. Aggiungere l'account e la password per il registro contenitori (*MyRegistry.azurecr.io* nell'esempio seguente), che consente al servizio di scaricare l'immagine del contenitore dal repository.

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

Questa funzionalità può essere abilitata o disabilitata aggiungendo l' `UseDefaultRepositoryCredentials` attributo a `ContainerHostPolicies` in ApplicationManifest.xml con `true` un `false` valore o.

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

Service Fabric usa quindi le credenziali predefinite del repository che è possibile specificare in ClusterManifest nella `Hosting` sezione.  Se `UseDefaultRepositoryCredentials` è `true`, Service Fabric legge i valori seguenti da ClusterManifest:

* DefaultContainerRepositoryAccountName (stringa)
* DefaultContainerRepositoryPassword (stringa)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (stringa)

Di seguito è riportato un esempio di ciò che è possibile aggiungere all'interno della `Hosting` sezione nella ClusterManifestTemplate.jssu file. La `Hosting` sezione può essere aggiunta in fase di creazione del cluster o successiva in un aggiornamento della configurazione. Per altre informazioni, vedere [Personalizzare le impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gestire i segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

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
          },
          {
        "name": "DefaultMSIEndpointForTokenAuthentication",
        "value": "URI"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Usare i token come credenziali del registro di sistema

Service Fabric supporta l'uso di token come credenziali per scaricare immagini per i contenitori.  Questa funzionalità sfrutta l' *identità gestita* del set di scalabilità di macchine virtuali sottostante per l'autenticazione nel registro di sistema, eliminando la necessità di gestire le credenziali utente.  Per altre informazioni, vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) .  Per usare questa funzionalità, è necessario eseguire i passaggi seguenti:

1. Verificare che l' *identità gestita assegnata dal sistema* sia abilitata per la macchina virtuale.

    ![Portale di Azure: creare un'opzione di identità del set di scalabilità di macchine virtuali](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Concedere le autorizzazioni al set di scalabilità di macchine virtuali per eseguire il pull/leggere le immagini dal registro di sistema. Nel pannello controllo di accesso (IAM) del Container Registry di Azure nella portale di Azure aggiungere un'assegnazione di *ruolo* per la macchina virtuale:

    ![Aggiungi entità macchina virtuale a ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Modificare quindi il manifesto dell'applicazione. Nella `ContainerHostPolicies` sezione aggiungere l'attributo `‘UseTokenAuthenticationCredentials=”true”` .

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
    > Il flag `UseDefaultRepositoryCredentials` impostato su true mentre `UseTokenAuthenticationCredentials` è true causerà un errore durante la distribuzione.

### <a name="using-token-credentials-outside-of-azure-global-cloud"></a>Uso delle credenziali del token all'esterno del cloud globale di Azure

Quando si usano le credenziali del registro di sistema basate su token, Service Fabric recupera un token per conto della macchina virtuale da presentare a ACR. Per impostazione predefinita, Service Fabric richiede un token il cui pubblico è l'endpoint cloud globale di Azure. Se si esegue la distribuzione in un'altra istanza cloud, ad esempio Azure Germania o Azure per enti pubblici, sarà necessario eseguire l'override del valore predefinito del parametro `DefaultMSIEndpointForTokenAuthentication` . Se non si esegue la distribuzione in un ambiente speciale, non eseguire l'override di questo parametro. In tal caso, verrà sostituito il valore predefinito, ovvero

```
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.windows.net/
```

con l'endpoint di risorsa appropriato per l'ambiente in uso. Ad esempio, per [Azure Germania](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping), l'override sarebbe 

```json
{
    "name": "DefaultMSIEndpointForTokenAuthentication",
    "value": "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.cloudapi.de/"
}
```

[Scopri di più sul recupero dei token del set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token).

## <a name="next-steps"></a>Passaggi successivi

* Vedere altre informazioni sull' [autenticazione del registro contenitori](../container-registry/container-registry-authentication.md).
