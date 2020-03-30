---
title: Azure Service Fabric - Uso dei riferimenti KeyVault dell'applicazione di Service FabricAzure Service Fabric - Using Service Fabric application KeyVault references
description: In questo articolo viene illustrato come utilizzare il supporto KeyVaultReference dell'infrastruttura del servizio per i segreti dell'applicazione.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545484"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Supporto KeyVaultReference per le applicazioni Service Fabric (anteprima)KeyVaultReference support for Service Fabric applications (preview)

Una sfida comune quando si creano applicazioni cloud è come archiviare in modo sicuro i segreti richiesti dall'applicazione. Ad esempio, è possibile archiviare le credenziali del repository del contenitore in keyvault e farvi riferimento nel manifesto dell'applicazione. Service Fabric KeyVaultReference usa l'identità gestita di Service Fabric e semplifica il riferimento ai segreti keyvault. Nella parte restante di questo articolo viene descritto in dettaglio come usare Service Fabric KeyVaultReference e include un utilizzo tipico.

## <a name="prerequisites"></a>Prerequisiti

- Identità gestita per l'applicazione (MIT)Managed Identity for Application (MIT)
    
    Il supporto Di KeyVaultReference di Service Fabric usa l'identità gestita dell'applicazione e pertanto le applicazioni che prevedono di usare KeyVaultReferences devono usare l'identità gestita. Seguire questo [documento](concepts-managed-identity.md) per abilitare l'identità gestita per l'applicazione.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) è la cache dei segreti locali crittografata di Service Fabric. CSS è una cache dell'archivio segreto locale che mantiene crittografati in memoria i dati sensibili, ad esempio una password, token e chiavi. KeyVaultReference, una volta recuperato, vengono memorizzati nella cache in CSS.

    Aggiungere quanto segue alla `fabricSettings` configurazione del cluster in per abilitare tutte le funzionalità necessarie per il supporto KeyVaultReference.Add the below to your cluster configuration under to enable all the required features for KeyVaultReference support.

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
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
                ]
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
    > Si consiglia di utilizzare un certificato di crittografia separato per CSS. È possibile aggiungerlo nella sezione "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Affinché le modifiche abbiano effetto, sarà inoltre necessario modificare i criteri di aggiornamento per specificare un riavvio forzato del runtime di Service Fabric in ogni nodo man mano che l'aggiornamento procede nel cluster. Questo riavvio garantisce che il servizio di sistema appena abilitato venga avviato e in esecuzione su ogni nodo. Nel frammento di codice seguente, forceRestart è l'impostazione essenziale; utilizzare i valori esistenti per il resto delle impostazioni.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Concedere all'autorizzazione di accesso all'identità gestita dell'applicazione per keyvaultGrant application's managed identity access permission to the keyvault

    Fare riferimento a questo documento per informazioni su come concedere l'accesso dell'identità gestita a keyvault.Reference this [document](how-to-grant-access-other-resources.md) to see how to grant managed identity access to keyvault. Si noti inoltre che se si utilizza l'identità gestita assegnata dal sistema, l'identità gestita viene creata solo dopo la distribuzione dell'applicazione.

## <a name="keyvault-secret-as-application-parameter"></a>Segreto Keyvault come parametro dell'applicazione
Si supponga che l'applicazione deve leggere la password del database back-end archiviata in keyvault, il supporto KeyVaultReference di Service Fabric semplifica l'operazione. L'esempio `DBPassword` seguente legge il segreto da keyvault usando il supporto KeyVaultReference di Service Fabric.Below reads secret from keyvault using Service Fabric KeyVaultReference support.

- Aggiungere una sezione a settings.xml

    Definire `DBPassword` il `KeyVaultReference` parametro con Tipo e Valore`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Fare riferimento alla nuova sezione in ApplicationManifest.xml in`<ConfigPackagePolicies>`

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

- Utilizzo di KeyVaultReference nell'applicazioneUsing KeyVaultReference in your application

    Service Fabric per la creazione di istanze del servizio risolverà il parametro KeyVaultReference usando l'identità gestita dell'applicazione. Ogni parametro `<Section  Name=dbsecrets>` elencato in sarà un file nella cartella a cui punta EnvironmentVariable SecretPath. Sotto frammento di codice C 'a. viene illustrato come leggere DBPassword nell'applicazione.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Per lo scenario contenitore, è possibile utilizzare `secrets` il MountPoint per controllare dove verrà montato il.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault segreto come variabile di ambienteKeyvault secret as environment variable

Service Fabric environment variables now support KeyVaultReference type, below example shows how to bind an environment variable to a secret stored in KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segreto Keyvault come password del repository contenitoreKeyvault secret as container repository password
KeyVaultReference è un tipo supportato per il contenitore RepositoryCredentials, nell'esempio seguente viene illustrato come utilizzare un riferimento keyvault come password del repository contenitore.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Domande frequenti
- L'identità gestita deve essere abilitata per il supporto di KeyVaultReference, l'attivazione dell'applicazione avrà esito negativo se KeyVaultReference viene utilizzato senza abilitare l'identità gestita.

- Se si utilizza l'identità assegnata dal sistema, questa viene creata solo dopo la distribuzione dell'applicazione e viene creata una dipendenza circolare. Una volta distribuita l'applicazione, è possibile concedere al sistema assegnato l'autorizzazione di accesso all'identità a keyvault.Once your application is deployed, you can grant the system assigned identity access permission to keyvault. È possibile trovare l'identità assegnata dal sistema in base al nome "cluster" / /nome dell'applicazione /

- Il keyvault deve essere nella stessa sottoscrizione del cluster dell'infrastruttura del servizio. 

## <a name="next-steps"></a>Passaggi successivi

* [Azure KeyVault Documentation](https://docs.microsoft.com/azure/key-vault/)
