---
title: Gestire i segreti nelle applicazioni di Azure Service Fabric | Microsoft Docs
description: Informazioni su come proteggere i valori dei segreti in un'applicazione di Service Fabric (indipendente dalla piattaforma).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614475"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gestire i segreti crittografati nelle applicazioni di Service Fabric
Questa guida descrive la procedura di gestione dei segreti in un'applicazione di Service Fabric. I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

Per usare i segreti crittografati in un'applicazione di Service Fabric è necessario completare tre passaggi:
* Configurare un certificato di crittografia e crittografare i segreti.
* Specificare i segreti crittografati in un'applicazione.
* Decrittografare i segreti crittografati dal codice del servizio.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurare un certificato di crittografia e crittografare i segreti
La configurazione di un certificato di crittografia e il suo utilizzo per crittografare i segreti sono operazioni che vengono eseguite diversamente in Windows rispetto a Linux.
* [Configurare un certificato di crittografia e crittografare segreti in cluster Windows.][secret-management-windows-specific-link]
* [Configurare un certificato di crittografia e crittografare segreti in cluster Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Specificare i segreti crittografati in un'applicazione
Il passaggio precedente illustra come crittografare un segreto con un certificato e produrre una stringa con codifica Base 64 da usare in un'applicazione. Questa stringa con codifica Base 64 può essere specificata come [parametro][parameters-link] crittografato nel file Settings.xml di un servizio o come [variabile di ambiente][environment-variables-link] nel file ServiceManifest.xml di un servizio.

Specificare un [parametro][parameters-link] crittografato nel file di configurazione Settings.xml del servizio con l'attributo `IsEncrypted` impostato su `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Specificare una [variabile di ambiente][environment-variables-link] crittografata nel file ServiceManifest.xml del servizio con l'attributo `Type` impostato su `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserire i segreti dell'applicazione nelle istanze dell'applicazione
Idealmente, la distribuzione in ambienti diversi dovrebbe essere il più possibile automatizzata. Questo può essere ottenuto tramite l'esecuzione della crittografia dei segreti in un ambiente di compilazione e l'uso dei segreti crittografati come parametri durante la creazione delle istanze dell'applicazione.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Usare parametri sostituibili in Settings.xml
Il file di configurazione Settings.xml consente di usare parametri sostituibili che possono essere forniti al momento della creazione dell'applicazione. Usare l'attributo `MustOverride` invece di fornire un valore per un parametro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Per sostituire i valori in Settings.xml, dichiarare un parametro di sostituzione per il servizio in ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

A questo punto, il valore può essere specificato come un *parametro dell'applicazione* quando si crea un'istanza dell'applicazione. La creazione di un'istanza dell'applicazione può generare uno script con PowerShell o essere scritta in C#, per semplificare l'integrazione in un processo di compilazione.

Tramite PowerShell, viene fornito il parametro del comando `New-ServiceFabricApplication` come [tabella hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Tramite C#, i parametri dell'applicazione vengono specificati in `ApplicationDescription` come `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Decrittografare i segreti crittografati dal codice del servizio
Le API per l'accesso a [parametri][parameters-link] e [variabili di ambiente][environment-variables-link] consentono di decrittografare facilmente i valori crittografati. Poiché la stringa crittografata contiene informazioni sul certificato usato per la crittografia, non è necessario specificare manualmente il certificato. Il certificato deve essere solo installato sul nodo su cui è in esecuzione il servizio.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [sicurezza dell'applicazione e del servizio](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
