---
title: Archivio dei segreti di Azure Service Fabric Central
description: Questo articolo descrive come usare l'archivio dei segreti centrali in Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980934"
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
È possibile creare una risorsa segreta usando il modello di Azure Resource Manager o l'API REST.

### <a name="use-resource-manager"></a>Usa Gestione risorse

Usare il modello seguente per usare Gestione risorse per creare la risorsa segreta. Il modello crea una risorsa `supersecret` Secret, ma non è ancora stato impostato alcun valore per la risorsa segreta.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>Usare l'API REST

Per creare una risorsa `supersecret` Secret usando l'API REST, effettuare una richiesta PUT per `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Per creare una risorsa segreta è necessario il certificato del cluster o il certificato client di amministrazione.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Impostare il valore del segreto

### <a name="use-the-resource-manager-template"></a>Usare il modello di Gestione risorse

Usare il modello di Gestione risorse seguente per creare e impostare il valore del segreto. Questo modello imposta il valore del segreto per la risorsa `supersecret` Secret come versione `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>Usare l'API REST

Usare lo script seguente per usare l'API REST per impostare il valore del segreto.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Usare il segreto nell'applicazione

Seguire questa procedura per usare il segreto nell'applicazione Service Fabric.

1. Aggiungere una sezione nel file **Settings. XML** con il frammento di codice seguente. Si noti che il valore è nel formato {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importare la sezione in **ApplicationManifest. XML**.
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

   La variabile di ambiente `SecretPath` punterà alla directory in cui sono archiviati tutti i segreti. Ogni parametro elencato nella sezione `testsecrets` viene archiviato in un file separato. L'applicazione può ora usare il segreto come indicato di seguito:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montare i segreti in un contenitore. L'unica modifica necessaria per rendere i segreti disponibili all'interno del contenitore consiste nel `specify` un punto di montaggio in `<ConfigPackage>`.
Il frammento di codice seguente è il **file ApplicationManifest. XML**modificato.  

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

1. È possibile associare un segreto a una variabile di ambiente di processo specificando `Type='SecretsStoreRef`. Il frammento di codice seguente è un esempio di come associare la versione `supersecret` `ver1` alla variabile di ambiente `MySuperSecret` in **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [sicurezza delle applicazioni e dei servizi](service-fabric-application-and-service-security.md).
