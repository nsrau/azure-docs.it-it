---
title: Archivio dei segreti di Azure Service Fabric Central
description: Questo articolo descrive come usare l'archivio dei segreti centrali in Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: c48be8945326f0f11ded7c5700cd70043830e4db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83197772"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Archivio dei segreti centrali in Azure Service Fabric 
Questo articolo descrive come usare l'archivio dei segreti centrali (CSS) in Azure Service Fabric per creare segreti nelle applicazioni Service Fabric. CSS è una cache di archivio Secret locale che consente di mantenere i dati sensibili, ad esempio password, token e chiavi, crittografati in memoria.

## <a name="enable-central-secrets-store"></a>Abilita archivio segreti centrali
Aggiungere lo script seguente alla configurazione del cluster in `fabricSettings` per abilitare CSS. Si consiglia di usare un certificato diverso da un certificato del cluster per CSS. Verificare che il certificato di crittografia sia installato in tutti i nodi e che `NetworkService` disponga dell'autorizzazione di lettura per la chiave privata del certificato.
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
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Dichiarare una risorsa segreta
È possibile creare una risorsa segreta usando l'API REST.
  > [!NOTE] 
  > Se il cluster usa l'autenticazione di Windows, la richiesta REST viene inviata tramite un canale HTTP non protetto. Si consiglia di usare un cluster basato su X509 con endpoint sicuri.

Per creare una `supersecret` risorsa segreta usando l'API REST, effettuare una richiesta Put a `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . Per creare una risorsa segreta è necessario il certificato del cluster o il certificato client di amministrazione.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Impostare il valore del segreto

Usare lo script seguente per usare l'API REST per impostare il valore del segreto.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Esaminare il valore del segreto
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Usare il segreto nell'applicazione

Seguire questa procedura per usare il segreto nell'applicazione Service Fabric.

1. Aggiungere una sezione nel file di **settings.xml** con il frammento di codice seguente. Si noti che il valore è nel formato { `secretname:version` }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importare la sezione in **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   La variabile di ambiente punterà `SecretPath` alla directory in cui sono archiviati tutti i segreti. Ogni parametro elencato nella `testsecrets` sezione viene archiviato in un file separato. L'applicazione può ora usare il segreto come indicato di seguito:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montare i segreti in un contenitore. L'unica modifica necessaria per rendere i segreti disponibili all'interno del contenitore è a `specify` un punto di montaggio in `<ConfigPackage>` .
Il frammento di codice seguente è il **ApplicationManifest.xml**modificato.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   I segreti sono disponibili nel punto di montaggio all'interno del contenitore.

1. È possibile associare un segreto a una variabile di ambiente del processo specificando `Type='SecretsStoreRef` . Il frammento di codice seguente è un esempio di come associare la `supersecret` versione `ver1` alla variabile di ambiente `MySuperSecret` in **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [sicurezza delle applicazioni e dei servizi](service-fabric-application-and-service-security.md).
