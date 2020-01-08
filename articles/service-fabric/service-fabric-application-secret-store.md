---
title: Archivio dei segreti Service Fabric
description: Questo articolo descrive come usare Service Fabric archivio dei segreti.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457516"
---
#  <a name="service-fabric-secrets-store"></a>Archivio dei segreti Service Fabric
Questo articolo descrive come creare e usare i segreti in applicazioni Service Fabric usando l'archivio di Service Fabric Secrets (CSS). CSS è una cache di archivio Secret locale, usata per conservare i dati sensibili, ad esempio una password, i token e le chiavi crittografate in memoria.

## <a name="enabling-secrets-store"></a>Abilitazione archivio segreti
 Aggiungere il codice seguente alla configurazione del cluster in `fabricSettings` per abilitare CSS. È consigliabile usare un certificato diverso dal certificato del cluster per CSS. Verificare che il certificato di crittografia sia installato in tutti i nodi e `NetworkService` disponga dell'autorizzazione di lettura per la chiave privata del certificato.
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
## <a name="declare-secret-resource"></a>Dichiarare la risorsa segreta
È possibile creare una risorsa segreta usando il modello di Gestione risorse o l'API REST.

* Con il modello di Azure Resource Manager
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
Il modello precedente crea `supersecret` risorsa privata, ma non è ancora stato impostato alcun valore per la risorsa segreta.

* Utilizzo dell'API REST

Per creare una risorsa segreta, `supersecret` effettuare una richiesta PUT a `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Per creare un segreto è necessario il certificato del cluster o il certificato client di amministrazione.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Imposta valore segreto
* Con il modello di Azure Resource Manager

Il modello di Gestione risorse seguente crea e imposta il valore per Secret `supersecret` con Version `ver1`.
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
* Utilizzo dell'API REST

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Uso del segreto nell'applicazione

1.  Aggiungere una sezione nel file Settings. XML con il contenuto seguente. Si noti che il valore è nel formato {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Importare ora la sezione in ApplicationManifest. XML
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

La variabile di ambiente ' SecretPath ' punterà alla directory in cui sono archiviati tutti i segreti. Ogni parametro elencato nella sezione `testsecrets` verrà archiviato in un file separato. L'applicazione può ora usare il segreto come illustrato di seguito
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Montaggio di segreti in un contenitore

Solo la modifica necessaria per rendere i segreti disponibili all'interno del contenitore consiste nel specificare una MountPoint in `<ConfigPackage>`.
Ecco il file ApplicationManifest. xml modificato  

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
I segreti saranno disponibili sotto il punto di montaggio all'interno del contenitore.

4. Binding del segreto a una variabile di ambiente 

È possibile associare il segreto a una variabile di ambiente del processo specificando type =' SecretsStoreRef '. Di seguito è riportato un esempio di come associare `supersecret` versione `ver1` a una variabile di ambiente `MySuperSecret` in ServiceManifest. XML.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [sicurezza dell'applicazione e del servizio](service-fabric-application-and-service-security.md)
