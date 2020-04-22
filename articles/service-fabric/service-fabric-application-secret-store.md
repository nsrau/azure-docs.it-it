---
title: Azure Service Fabric Central Secrets Store
description: Questo articolo descrive come usare Central Secrets Store in Azure Service Fabric.This article describes how to use Central Secrets Store in Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770422"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Archivio dei segreti centrale nell'infrastruttura del servizio di AzureCentral Secrets Store in Azure Service Fabric 
Questo articolo descrive come usare Central Secrets Store (CSS) in Azure Service Fabric per creare segreti nelle applicazioni di Service Fabric.This article describes how to use Central Secrets Store (CSS) in Azure Service Fabric to create secrets in Service Fabric applications. CSS è una cache dell'archivio segreto locale che mantiene crittografati in memoria i dati sensibili, ad esempio una password, token e chiavi.

## <a name="enable-central-secrets-store"></a>Abilitare l'archivio dei segreti centraliEnable Central Secrets Store
Aggiungere lo script seguente alla `fabricSettings` configurazione del cluster in per abilitare CSS. Si consiglia di utilizzare un certificato diverso da un certificato del cluster per CSS. Assicurarsi che il certificato di crittografia `NetworkService` sia installato in tutti i nodi e che disponga dell'autorizzazione di lettura per la chiave privata del certificato.
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
  > Se il cluster utilizza l'autenticazione di Windows, la richiesta REST viene inviata tramite un canale HTTP non protetto. È consigliabile usare un cluster basato su X509 con endpoint sicuri.

Per creare `supersecret` una risorsa segreta utilizzando l'API `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`REST, effettuare una richiesta PUT a . Per creare una risorsa segreta, è necessario il certificato del cluster o il certificato client di amministrazione.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Impostare il valore segreto

Usare lo script seguente per usare l'API REST per impostare il valore segreto.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Esaminare il valore segreto
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Usare il segreto nell'applicazioneUse the secret in your application

Seguire questi passaggi per usare il segreto nell'applicazione Service Fabric.Follow these steps to use the secret in your Service Fabric application.

1. Aggiungere una sezione nel file **settings.xml** con il frammento di codice seguente. Si noti in questo caso`secretname:version`che il valore è nel formato .

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

   La variabile `SecretPath` di ambiente punterà alla directory in cui sono archiviati tutti i segreti. Ogni parametro `testsecrets` elencato nella sezione viene memorizzato in un file separato. L'applicazione può ora utilizzare il segreto come segue:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montare i segreti in un contenitore. L'unica modifica necessaria per rendere i `specify` segreti disponibili `<ConfigPackage>`all'interno del contenitore è in un punto di montaggio in .
Il frammento di codice seguente è il **file ApplicationManifest.xml**modificato.  

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
   I segreti sono disponibili sotto il punto di montaggio all'interno del contenitore.

1. È possibile associare un segreto a `Type='SecretsStoreRef`una variabile di ambiente di processo specificando . Il frammento di codice seguente `supersecret` è `ver1` un esempio `MySuperSecret` di come associare la versione alla variabile di ambiente in **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla [sicurezza di applicazioni e servizi](service-fabric-application-and-service-security.md).
