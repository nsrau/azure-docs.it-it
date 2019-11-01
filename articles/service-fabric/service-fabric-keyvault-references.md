---
title: Service Fabric di Azure-uso di Service Fabric riferimenti all'insieme di credenziali delle applicazioni | Microsoft Docs
description: Questo articolo illustra come usare il supporto KeyVaultReference di Service Fabric per i segreti dell'applicazione.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: b0f1a081727721ea0325276cf9edd52c6d71fb6b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243860"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Supporto di KeyVaultReference per applicazioni Service Fabric (anteprima)

Un problema comune durante la creazione di applicazioni cloud è come archiviare in modo sicuro i segreti richiesti dall'applicazione. Ad esempio, potrebbe essere necessario archiviare le credenziali del repository del contenitore nell'insieme di credenziali delle chiavi e farvi riferimento nel manifesto dell'applicazione. Service Fabric KeyVaultReference USA Service Fabric identità gestita e rende più semplice fare riferimento ai segreti dell'insieme di credenziali delle chiavi. Il resto di questo articolo illustra in dettaglio come usare Service Fabric KeyVaultReference e include un utilizzo tipico.

## <a name="prerequisites"></a>Prerequisiti

- Identità gestita per l'applicazione (MIT)
    
    Service Fabric supporto KeyVaultReference usa l'identità gestita dell'applicazione e pertanto le applicazioni che pianificano l'uso di KeyVaultReferences devono usare l'identità gestita. Seguire questo [documento](concepts-managed-identity.md) per abilitare l'identità gestita per l'applicazione.

- Archivio dei segreti centrali (CSS).

    L'archivio dei segreti centrali (CSS) è la cache dei segreti locali crittografati di Service Fabric, KeyVaultReference una volta recuperati vengono memorizzati nella cache in CSS.

    Aggiungere il seguente alla configurazione del cluster in `fabricSettings` per abilitare tutte le funzionalità necessarie per il supporto di KeyVaultReference.

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > È consigliabile usare un certificato di crittografia separato per CSS. È possibile aggiungerlo nella sezione "CentralSecretService".

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Concedere l'autorizzazione di accesso alle identità gestite dell'applicazione all'insieme di credenziali delle credenziali

    Fare riferimento a questo [documento](how-to-grant-access-other-resources.md) per vedere come concedere l'accesso all'identità gestita all'insieme di credenziali delle credenziali. Si noti anche che se si usa l'identità gestita assegnata dal sistema, l'identità gestita viene creata solo dopo la distribuzione dell'applicazione.

## <a name="keyvault-secret-as-application-parameter"></a>Segreto dell'insieme di credenziali delle credenziali come parametro dell'applicazione
Supponiamo che l'applicazione debba leggere la password del database back-end archiviata nell'insieme di credenziali delle credenziali, Service Fabric il supporto di KeyVaultReference facilita l'operazione. Nell'esempio seguente viene letta `DBPassword` Secret da Vault con Service Fabric supporto KeyVaultReference.

- Aggiungere una sezione a Settings. XML

    Definire `DBPassword` parametro con il tipo `KeyVaultReference` e il valore `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Fare riferimento alla nuova sezione in ApplicationManifest. XML in `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Uso di KeyVaultReference nell'applicazione

    Service Fabric sulla creazione di istanze del servizio risolverà il parametro KeyVaultReference usando l'identità gestita dell'applicazione. Ogni parametro elencato in `<Section  Name=dbsecrets>` sarà un file sotto la cartella a cui punta Metodo EnvironmentVariable SecretPath. Il C# frammento di codice seguente mostra come leggere dbpassword nell'applicazione.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Per lo scenario contenitore, è possibile usare MountPoint per controllare la posizione in cui verrà montato il `secrets`.

## <a name="keyvault-secret-as-environment-variable"></a>Segreto dell'insieme di credenziali delle credenziali come variabile di ambiente

Le variabili di ambiente Service Fabric ora supportano il tipo KeyVaultReference. di seguito viene illustrato come associare una variabile di ambiente a un segreto archiviato nell'insieme di credenziali delle credenziali.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segreto dell'insieme di credenziali delle credenziali come password del repository contenitore
KeyVaultReference è un tipo supportato per il RepositoryCredentials del contenitore. di seguito viene illustrato come usare un riferimento all'insieme di credenziali delle credenziali come password del repository del contenitore.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- È necessario abilitare l'identità gestita per il supporto di KeyVaultReference. l'attivazione dell'applicazione avrà esito negativo se si usa KeyVaultReference senza abilitare l'identità gestita.

- Se si usa l'identità assegnata dal sistema, questa viene creata solo dopo la distribuzione dell'applicazione e viene creata una dipendenza circolare. Una volta distribuita l'applicazione, è possibile concedere al sistema l'autorizzazione di accesso alle identità per l'insieme di credenziali delle credenziali. È possibile trovare l'identità assegnata dal sistema in base al nome {cluster}/{Application}/{ServiceName}

- L'insieme di credenziali delle credenziali deve trovarsi nella stessa sottoscrizione del cluster di Service Fabric. 

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Azure Azure Vault](https://docs.microsoft.com/azure/key-vault/)
