
<properties
   pageTitle="Gestione dei segreti nelle applicazioni di Service Fabric | Microsoft Azure"
   description="Questo articolo descrive come proteggere i valori dei segreti in un'applicazione di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="vturecek"/>

# Gestione dei segreti nelle applicazioni di Service Fabric

Questa guida descrive la procedura di gestione dei segreti in un'applicazione di Service Fabric. I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

Questa guida usa l'insieme di credenziali delle chiavi di Azure per gestire chiavi e segreti. Tuttavia, l'*uso* di segreti in un'applicazione è indipendente dalla piattaforma cloud per consentire alle applicazioni di essere distribuite in un cluster ospitato in un punto qualsiasi.

## Overview

Il metodo consigliato per gestire le impostazioni di configurazione del servizio è tramite i [pacchetti di configurazione del servizio][config-package]. I pacchetti di configurazione dispongono di controllo delle versioni e sono aggiornabili tramite gli aggiornamenti in sequenza gestiti con convalida dell'integrità e rollback automatico. Questo approccio è da preferire alla configurazione globale in quanto riduce le probabilità di un'interruzione del servizio globale. I segreti crittografati non rappresentano un'eccezione. Service Fabric offre funzionalità incorporate per crittografare e decrittografare i valori in un file Settings.xml del pacchetto configurazione tramite la crittografia del certificato.

Il diagramma seguente illustra il flusso di base per la gestione dei segreti in un'applicazione di Service Fabric:

![panoramica della gestione dei segreti][overview]

In questo flusso sono presenti quattro passaggi principali:

 1. Ottenere un certificato di crittografia dei dati.
 2. Installare il certificato nel cluster.
 3. Crittografare i valori dei segreti quando si distribuisce un'applicazione con il certificato e inserirli nel file di configurazione Settings.xml del servizio.
 4. Leggere i valori crittografati risultati da Settings. XML eseguendo la decrittografia con lo stesso certificato di crittografia.

L'[insieme di credenziali delle chiavi di Azure][key-vault-get-started] viene usato come percorso di archiviazione sicuro per i certificati e come un modo per ottenere i certificati installati nei cluster Service Fabric in Azure. Se non si esegue la distribuzione in Azure, non è necessario usare l'insieme di credenziali delle chiavi per gestire i segreti nelle applicazioni di Service Fabric.

## Certificato di crittografia dei dati

Il certificato di crittografia dei dati viene usato esclusivamente per la crittografia e decrittografia dei valori di configurazione del file Settings.xml del servizio e non viene impiegato per l'autenticazione. Il certificato deve soddisfare i requisiti seguenti:

 - Il certificato deve includere una chiave privata.
 - Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
 - L'uso delle chiavi del certificato deve includere la crittografia dei dati (10) e non deve includere l'autenticazione del server o del client.
 
 Ad esempio, quando si crea un certificato autofirmato tramite PowerShell, il flag `KeyUsage` deve essere impostato su `DataEncipherment`:

 ```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```


## Installare il certificato nel cluster

Questo certificato deve essere installato su ogni nodo del cluster. Verrà usato in fase di esecuzione per decrittografare i valori archiviati in nel file Settings.xml del servizio. Per le istruzioni di installazione, vedere l'articolo che spiega [come creare un cluster con Azure Resource Manager][service-fabric-cluster-creation-via-arm].

## Eseguire la crittografia dei segreti dell'applicazione

L'SDK di Service Fabric ha funzioni di crittografia e decrittografia dei segreti predefinite. I valori dei segreti possono essere crittografati in fase di compilazione e quindi decrittografati e letti a livello di programmazione nel codice del servizio.

Il comando PowerShell seguente viene usato per crittografare un segreto. È necessario usare lo stesso certificato di crittografia installato nel cluster per produrre il testo crittografato per i valori del segreto:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

La stringa Base 64 risultante contiene sia il testo crittografato dei segreti che le informazioni relative al certificato usato per crittografarlo. La stringa con codifica Base 64 può essere inserita in un parametro nel file di configurazione Settings.xml del servizio con l'attributo `IsEncrypted` impostato su `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### Inserire i segreti dell'applicazione nelle istanze dell'applicazione  

Idealmente, la distribuzione in ambienti diversi dovrebbe essere il più possibile automatizzata. Questo può essere ottenuto tramite l'esecuzione della crittografia dei segreti in un ambiente di compilazione e l'uso dei segreti crittografati come parametri durante la creazione delle istanze dell'applicazione.

#### Usare parametri sostituibili in Settings.xml

Il file di configurazione Settings.xml consente di usare parametri sostituibili che possono essere forniti al momento della creazione dell'applicazione. Usare l'attributo `MustOverride` invece di fornire un valore per un parametro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
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

## Decrittografare i segreti dal codice del servizio

I servizi di Service Fabric vengono eseguiti in NETWORK SERVICE per impostazione predefinita su Windows e non hanno accesso ai certificati installati nel nodo senza un'impostazione aggiuntiva.

Quando si usa un certificato di crittografia dati, è necessario assicurarsi che NETWORK SERVICE o qualsiasi account utente sia in esecuzione nel servizio disponga dell'accesso alla chiave privata del certificato. Service Fabric gestirà automaticamente la concessione dell'accesso per il servizio se viene configurato a tale scopo. Questa configurazione può essere eseguita in ApplicationManifest definendo utenti e criteri di protezione per i certificati. Nell'esempio seguente, all'account NETWORK SERVICE viene concesso l'accesso in lettura a un certificato definito dalla relativa identificazione personale:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [AZURE.NOTE] Quando si copia un'identificazione personale di un certificato dallo snap-in dell'archivio dei certificati su Windows, viene inserito un carattere invisibile all'inizio della stringa di identificazione personale. Questo carattere invisibile può causare un errore durante il tentativo di individuare un certificato tramite identificazione personale. Accertarsi di eliminare questo carattere aggiuntivo.

### Usare i segreti dell'applicazione nel codice del servizio

L'API per accedere ai valori di configurazione da Settings.xml in un pacchetto di configurazione consente di decrittografare facilmente i valori con l'attributo `IsEncrypted` impostato su `true`. Poiché il testo crittografato contiene informazioni sul certificato usato per la crittografia, non è necessario individuare manualmente il certificato. Il certificato deve essere solo installato sul nodo su cui è in esecuzione il servizio. È sufficiente chiamare il metodo `DecryptValue()` per recuperare il valore del segreto originale:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## Passaggi successivi

Altre informazioni sull'[esecuzione di applicazioni con autorizzazioni di sicurezza diverse](service-fabric-application-runas-security.md)

<!-- Links -->
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-model.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]: ./media/service-fabric-application-secret-management/overview.png

<!---HONumber=AcomDC_0824_2016-->