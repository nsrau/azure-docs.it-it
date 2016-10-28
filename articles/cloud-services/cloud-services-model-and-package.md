<properties
    pageTitle="Cosa sono un modello del servizio cloud e un pacchetto | Microsoft Azure"
    description="Descrive il modello del servizio cloud (csdef, cscfg) e il pacchetto (cspkg) in Azure"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# Cos'è il modello del servizio cloud e come è possibile crearne il pacchetto?
Un servizio cloud viene creato da tre componenti: la definizione del servizio _(.csdef)_, la configurazione del servizio _(.cscfg)_ e un pacchetto del servizio _(.cspkg)_. Entrambi i file **ServiceDefinition.csdef** e **ServiceConfig.cscfg** sono basati su XML e descrivono la struttura e la configurazione del servizio cloud. Insieme costituiscono il modello. **ServicePackage.cspkg** è un file ZIP generato da **ServiceDefinition.csdef** e contiene tra l'altro tutte le dipendenze necessarie basate su file binari. Azure crea un servizio cloud sia da **ServicePackage.cspkg** che da **ServiceConfig.cscfg**.

Una volta che il servizio cloud è in esecuzione in Azure, è possibile riconfigurarlo tramite il file **ServiceConfig.cscfg**, ma non è possibile modificare la definizione.

## Quali altre informazioni sono necessarie?

* Si vuole sapere di più sui file [ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg](#cscfg).
* Si hanno già informazioni in proposito, ma sono necessari [alcuni esempi](#next-steps) sugli elementi configurabili.
* Si vuole crea [ServicePackage.cspkg](#cspkg).
* Si sta usando Visual Studio e si vuole...
    * [Crea un nuovo servizio cloud][vs_create]
    * [Riconfigurare un servizio cloud esistente][vs_reconfigure]
    * [Distribuire un progetto di servizio cloud][vs_deploy]
    * [Desktop remoto in un'istanza del servizio cloud][remotedesktop]

<a name="csdef"></a>
## ServiceDefinition.csdef
Il file **ServiceDefinition.csdef** specifica le impostazioni usate da Azure per configurare un servizio cloud. Lo [schema di definizione dei servizi di Azure (file .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fornisce il formato consentito per un file di definizione del servizio. L'esempio seguente mostra le impostazioni che possono essere definite per i ruoli Web e di lavoro:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

È possibile fare riferimento allo [schema di definizione dei servizi] [] per comprendere meglio lo schema XML usato qui, ma ecco anche una rapida spiegazione di alcuni elementi:

**Sites** Contiene le definizioni per i siti Web o per le applicazioni Web ospitate in IIS7.

**InputEndpoints** Contiene le definizioni per gli endpoint usati per contattare il servizio cloud.

**InternalEndpoints** Contiene le definizioni per gli endpoint usati dalle istanze del ruolo per comunicare tra loro.

**ConfigurationSettings** Contiene le definizioni delle impostazioni per le funzionalità di un ruolo specifico.

**Certificates** Contiene le definizioni dei certificati necessari per un ruolo. L'esempio di codice precedente mostra un certificato usato per la configurazione di Azure Connect.

**LocalResources** Contiene le definizioni per le risorse di archiviazione locale. Una risorsa di archiviazione locale è una directory riservata nel file system della macchina virtuale in cui è in esecuzione un'istanza di un ruolo.

**Imports** Contiene le definizioni per i moduli importati. L'esempio di codice precedente mostra i moduli per la connessione Desktop remoto e Azure Connect.

**Startup** Contiene le attività eseguite all'avvio del ruolo. Le attività vengono definite in un file eseguibile o con estensione cmd.



<a name="cscfg"></a>
## ServiceConfiguration.cscfg
La configurazione delle impostazioni per il servizio cloud è determinata dai valori nel file **ServiceConfiguration.cscfg**. Si specifica il numero di istanze che si vuole distribuire per ogni ruolo in questo file. I valori per le impostazioni di configurazione definite nel file di definizione del servizio vengono aggiunti al file di configurazione del servizio. Vengono aggiunte al file anche le identificazioni personali per eventuali certificati di gestione associati al servizio cloud. Lo [schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fornisce il formato consentito per un file di configurazione del servizio.

Il file di configurazione del servizio non viene incluso nel pacchetto con l'applicazione, ma viene caricato in Azure come file separato e viene usato per configurare il servizio cloud. È possibile caricare un nuovo file di configurazione del servizio senza ridistribuire il servizio cloud. I valori di configurazione per il servizio cloud possono essere modificati mentre il servizio cloud è in esecuzione. L'esempio seguente mostra le impostazioni di configurazione che possono essere definite per i ruoli Web e di lavoro:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

È possibile fare riferimento allo [schema di configurazione dei servizi](https://msdn.microsoft.com/library/azure/ee758710.aspx) per comprendere meglio lo schema XML usato qui, ma ecco anche una rapida spiegazione degli elementi:

**Instances** Configura il numero di istanze in esecuzione per il ruolo. Per evitare che il servizio cloud diventi potenzialmente non disponibile durante gli aggiornamenti, è consigliabile distribuire più istanze dei ruoli esposti al Web. In questo modo si rispettano le linee guida del [contratto di servizio (SLA, Service Level Agreement) per il servizio Calcolo di Azure](http://azure.microsoft.com/support/legal/sla/), che garantisce una connettività esterna pari al 99,95% per i ruoli esposti a Internet quando due o più istanze dei ruoli vengono distribuite per un servizio.

**ConfigurationSettings** Configura le impostazioni per le istanze in esecuzione di un ruolo. Il nome degli elementi `<Setting>` deve corrispondere alle definizioni delle impostazioni nel file di definizione del servizio.

**Certificates** Configura i certificati usati dal servizio. L'esempio di codice precedente mostra come definire il certificato per il modulo RemoteAccess. Il valore dell'attributo *thumbprint* deve essere impostato sull'identificazione personale del certificato da usare.

<p/>

 >[AZURE.NOTE] L'identificazione personale del certificato può essere aggiunta al file di configurazione usando un editor di testo oppure il valore può essere aggiunto nella scheda **Certificati** della pagina **Proprietà** del ruolo in Visual Studio.



## Definizione delle porte per le istanze del ruolo
Azure consente solo un punto di ingresso a un ruolo Web. Ciò significa che tutto il traffico viene gestito tramite un solo indirizzo IP. È possibile configurare la condivisione di una porta per i siti Web configurando l'intestazione host in modo da indirizzare la richiesta al percorso corretto. Si possono anche configurare le applicazioni in modo che siano in ascolto di porte note sull'indirizzo IP.

L'esempio seguente illustra la configurazione di un ruolo Web con un sito Web e un'applicazione Web. Il sito Web è configurato come percorso di ingresso predefinito sulla porta 80 e le applicazioni Web sono configurate in modo da ricevere le richieste da un'intestazione host alternativa denominata "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## Modifica della configurazione di un ruolo
È possibile aggiornare la configurazione del servizio cloud mentre è in esecuzione in Azure, senza portare il servizio offline. Per modificare le informazioni di configurazione, è possibile caricare un nuovo file di configurazione o modificare il file di configurazione sul posto e applicarlo al servizio in esecuzione. Alla configurazione di un servizio possono essere apportate le seguenti modifiche:

- **Modifica dei valori delle impostazioni di configurazione** Quando un'impostazione di configurazione viene modificata, è possibile applicare la modifica a un'istanza del ruolo mentre l'istanza è online oppure è possibile riciclare normalmente l'istanza e applicare la modifica mentre si trova offline.

- **Modifica della topologia del servizio delle istanze del ruolo** Le modifiche di topologia non influiscono sulle istanze in esecuzione, a eccezione del caso di rimozione di un'istanza. Non è in genere necessario riciclare tutte le istanze rimanenti; tuttavia, è possibile scegliere di riciclare le istanze del ruolo in risposta a una modifica di topologia.

- **Modifica dell'identificazione personale del certificato** È possibile aggiornare un certificato solo quando un'istanza del ruolo è offline. Se un certificato viene aggiunto, eliminato o modificato mentre un'istanza del ruolo è online, in Azure l'istanza viene portata offline normalmente per aggiornare il certificato e riportata online dopo il completamento della modifica.

### Gestione delle modifiche di configurazione con gli eventi di runtime del servizio
La [libreria di runtime di Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) include lo spazio dei nomi [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx), che fornisce le classi per l'interazione con l'ambiente di Azure da codice in esecuzione in un'istanza di un ruolo. La classe [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) definisce gli eventi seguenti generati prima e dopo una modifica di configurazione:

- **Evento [Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)** Si verifica prima che la modifica di configurazione venga applicata a un'istanza specificata di un ruolo, offrendo la possibilità di interrompere il funzionamento delle istanze del ruolo, se necessario.
- **Evento [Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)** Si verifica dopo che la modifica della configurazione è stata applicata a un'istanza specificata di un ruolo.

> [AZURE.NOTE] Poiché le modifiche ai certificati richiedono sempre che le istanze di un ruolo siano portate offline, non generano eventi RoleEnvironment.Changing o RoleEnvironment.Changed.

<a name="cspkg"></a>
## ServicePackage.cspkg
Per distribuire un'applicazione come servizio cloud in Azure, è necessario prima creare un pacchetto dell'applicazione nel formato appropriato. È possibile usare lo strumento da riga di comando **CSPack** (installato con [Azure SDK](https://azure.microsoft.com/downloads/)) per creare il file del pacchetto come alternativa a Visual Studio.

**CSPack** usa il contenuto del file di definizione del servizio e del file di configurazione del servizio per definire il contenuto del pacchetto. **CSPack** genera un file del pacchetto dell’applicazione (con estensione .cspkg) che è possibile caricare su Azure utilizzando il [portale di Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Per impostazione predefinita, il pacchetto viene denominato `[ServiceDefinitionFileName].cspkg`, ma è possibile specificare un nome diverso usando l'opzione `/out` di **CSPack**.

**CSPack** in genere si trova in `C:\Program Files\Microsoft SDKs\Azure\.NET SDK[sdk-version]\bin`

>[AZURE.NOTE]
CSPack.exe (in Windows) è disponibile eseguendo il collegamento del **prompt dei comandi di Microsoft Azure** installato con l'SDK.
>  
>Eseguire il programma CSPack.exe da solo per visualizzare la documentazione su tutte le possibili opzioni e comandi.

<p />

>[AZURE.TIP]
Eseguire il servizio cloud in locale nell'**emulatore di calcolo di Microsoft Azure**, usare l'opzione **/copyonly**. Questa opzione copia i file binari per l'applicazione in un layout di directory da cui possono essere eseguiti nell'emulatore di calcolo.

### Comando di esempio per creare un pacchetto di un servizio cloud
L'esempio seguente crea un pacchetto dell'applicazione che contiene le informazioni per un ruolo Web. Nel comando vengono specificati il file di definizione del servizio da utilizzare, la directory in cui trovare i file binari e il nome del file del pacchetto.

    cspack [DirectoryName][ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Se l'applicazione contiene sia un ruolo Web che un ruolo di lavoro, viene usato il comando seguente:

    cspack [DirectoryName][ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Dove le variabili vengono definite come segue:

| Variabile | Valore |
| ------------------------- | ----- |
| [DirectoryName] | Sottodirectory della directory radice del progetto contenente il file csdef del progetto Azure.|
| [ServiceDefinition] | Nome del file di definizione del servizio. Per impostazione predefinita, il file è denominato ServiceDefinition.csdef. |
| [OutputFileName] | Nome del file del pacchetto generato. Viene in genere impostato sul nome dell'applicazione. Se non viene specificato alcun nome file, il pacchetto dell'applicazione viene creato con il nome [ApplicationName].cspkg.|
| [RoleName] | Nome del ruolo definito nel file di definizione del servizio.|
| [RoleBinariesDirectory] | Percorso dei file binari del ruolo.|
| [VirtualPath] | Directory fisiche per ogni percorso virtuale definito nella sezione Sites della definizione del servizio.|
| [PhysicalPath] | Directory fisiche dei contenuti per ogni percorso virtuale definito nel nodo del sito della definizione del servizio.|
| [RoleAssemblyName] | Nome del file binario del ruolo.| 


## Passaggi successivi

Si sta creando un pacchetto del servizio cloud e si vuole...

* [Configurare il desktop remoto per un'istanza del servizio cloud][remotedesktop]
* [Distribuire un progetto di servizio cloud][deploy]

Si sta usando Visual Studio e si vuole...

* [Crea un nuovo servizio cloud][vs_create]
* [Riconfigurare un servizio cloud esistente][vs_reconfigure]
* [Distribuire un progetto di servizio cloud][vs_deploy]
* [Configurare il desktop remoto per un'istanza del servizio cloud][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md

<!---HONumber=AcomDC_0914_2016-->