---
title: Guida alla conversione di ruoli di lavoro e Web in servizi senza stato di Service Fabric | Microsoft Docs
description: Questa guida confronta i ruoli di lavoro e Web di Servizi Cloud con i servizi senza stato di Service Fabric per facilitare la migrazione da Servizi cloud a Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Guida alla conversione di ruoli di lavoro e Web in servizi senza stato di Service Fabric
Questo articolo descrive come eseguire la migrazione di ruoli di lavoro e Web di Servizi cloud a servizi senza stato di Service Fabric. Questo è il percorso di migrazione più semplice da Servizi cloud a Service Fabric per le applicazioni la cui architettura complessiva rimarrà approssimativamente la stessa.

## Da progetto di Servizi cloud a progetto di applicazione di Service Fabric
 Un progetto di Servizi cloud e un progetto di applicazione di Service Fabric hanno una struttura simile ed entrambi rappresentano l'unità di distribuzione per l'applicazione, ovvero ognuno definisce il pacchetto completo che viene distribuito per eseguire l'applicazione. Un progetto di Servizi cloud contiene uno o più ruoli di lavoro o Web. In modo analogo un progetto di applicazione di Service Fabric contiene uno o più servizi.

La differenza è che il progetto di Servizi cloud abbina la distribuzione dell'applicazione con la distribuzione di una macchina virtuale e quindi contiene le impostazioni di configurazione della macchina virtuale, mentre il progetto di applicazione di Service Fabric definisce solo un'applicazione che verrà distribuita a un set di macchine virtuali esistenti in un cluster di Service Fabric. Il cluster di Service Fabric viene distribuito solo una volta, tramite un modello di Azure Resource Manager o tramite il portale di Azure, e possono esservi distribuite più applicazioni di Service Fabric.

![Confronto tra i progetti di Servizi cloud e Service Fabric][3]

## Da ruolo di lavoro a servizio senza stato
Concettualmente un ruolo di lavoro rappresenta un carico di lavoro senza stato, ovvero ogni istanza del carico di lavoro è identica e le richieste possono essere indirizzate a qualsiasi istanza in qualsiasi momento. Non è previsto che ogni istanza ricordi la richiesta precedente. Lo stato in cui opera il carico di lavoro viene gestito da un archivio stati esterno, ad esempio un archivio tabelle di Azure o Azure DocumentDB. In Service Fabric questo tipo di carico di lavoro è rappresentato da un servizio senza stato. L'approccio più semplice per la migrazione di un ruolo di lavoro a Service Fabric può avvenire mediante la conversione di codice del ruolo di lavoro in un servizio senza stato.

![Da ruolo di lavoro a servizio senza stato][4]

## Da ruolo Web a servizio senza stato
Analogamente a un ruolo di lavoro, anche un ruolo Web rappresenta un carico di lavoro senza stato e quindi concettualmente può essere associato anch'esso a un servizio senza stato di Service Fabric. Tuttavia, a differenza dei ruoli Web, Service Fabric non supporta IIS. Per eseguire la migrazione di un'applicazione Web da un ruolo Web a un servizio senza stato è necessario passare prima a un framework Web che possa essere self-hosted e non dipenda da IIS o System.Web, ad esempio ASP.NET Core 1.

| **Applicazione** | **Supportato** | **Percorso di migrazione** |
| --- | --- | --- |
| Web Form ASP.NET |No |Convertire in ASP.NET Core 1 MVC |
| ASP.NET MVC |Con migrazione |Aggiornare ad ASP.NET Core 1 |
| API Web ASP.NET |Con migrazione |Usare un server self-hosted o ASP.NET Core 1 |
| ASP.NET Core 1 |Sì |N/D |

## API del punto di ingresso e ciclo di vita
Le API del servizio di Service Fabric e del ruolo di lavoro offrono punti di ingresso simili:

| **Punto di ingresso** | **Istanze del ruolo di lavoro** | **Servizio di Service Fabric** |
| --- | --- | --- |
| Elaborazione in corso |`Run()` |`RunAsync()` |
| Avvio della macchina virtuale |`OnStart()` |N/D |
| Arresto della macchina virtuale |`OnStop()` |N/D |
| Apertura del listener per le richieste client |N/D |<ul><li> `CreateServiceInstanceListener()` per servizio senza stato</li><li>`CreateServiceReplicaListener()` per servizio con stato</li></ul> |

### Istanze del ruolo di lavoro
```C#

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### Servizio senza stato di Service Fabric
```C#

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Entrambi hanno un override "Run" primario in cui iniziare l'elaborazione. I servizi di Service Fabric combinano `Run`, `Start` e `Stop` in un singolo punto di ingresso, `RunAsync`. Il servizio inizierà a funzionare all'avvio di `RunAsync` e verrà arrestato quando viene segnalato CancellationToken del metodo `RunAsync`.

Esistono alcune differenze principali tra il ciclo di vita e la durata dei servizi di Service Fabric e dei ruoli di lavoro:

* **Ciclo di vita:** la differenza principale è che un ruolo di lavoro è una VM e quindi il ciclo di vita è associato alla VM e include gli eventi relativi all'avvio e all'arresto della VM. Un servizio di Service Fabric ha un ciclo di vita separato dal ciclo di vita della macchina virtuale, quindi non include gli eventi relativi all'avvio e all'arresto della macchina virtuale host o del computer, perché non sono correlati.
* **Durata:** un'istanza del ruolo di lavoro verrà riciclata se il metodo `Run` viene chiuso. Il metodo `RunAsync` in un servizio di Service Fabric può tuttavia essere eseguito fino al completamento e l'istanza del servizio rimarrà operativa.

Service Fabric fornisce un punto di ingresso facoltativo di configurazione della comunicazione per i servizi in ascolto delle richieste client. Sia il punto di ingresso di comunicazione che quello di RunAsync sono sostituzioni facoltative nei servizi di Service Fabric, ovvero il servizio può scegliere di restare in ascolto solo delle richieste client o eseguire solo un ciclo di elaborazione oppure entrambi, motivo per cui il metodo RunAsync può terminare senza riavviare l'istanza del servizio, perché può continuare a rimanere in ascolto delle richieste client.

## API dell'applicazione e ambiente
L'API dell'ambiente di Servizi cloud fornisce informazioni e funzionalità per l'istanza corrente della macchina virtuale, nonché informazioni su altre istanze del ruolo VM. Service Fabric fornisce informazioni correlate al relativo runtime e alcune informazioni relative al nodo su cui un servizio è in esecuzione.

| **Attività dell'ambiente** | **Servizi cloud** | **Service Fabric** |
| --- | --- | --- |
| Impostazioni di configurazione e notifica di modifiche |`RoleEnvironment` |`CodePackageActivationContext` |
| Archiviazione locale |`RoleEnvironment` |`CodePackageActivationContext` |
| Informazioni sull'endpoint |`RoleInstance` <ul><li>Istanza corrente: `RoleEnvironment.CurrentRoleInstance`</li><li>Altri ruoli e istanza: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` per l'indirizzo del nodo corrente</li><li>`FabricClient` e `ServicePartitionResolver` per l'individuazione dell'endpoint di servizio</li> |
| Emulazione dell'ambiente |`RoleEnvironment.IsEmulated` |N/D |
| Evento di modifica simultanea |`RoleEnvironment` |N/D |

## Impostazioni di configurazione
Le impostazioni di configurazione in Servici cloud vengono impostate per un ruolo VM e si applicano a tutte le istanze di quel ruolo VM. Queste impostazioni sono coppie chiave-valore impostate nei file ServiceConfiguration.*.cscfg e sono accessibili direttamente tramite RoleEnvironment. In Service Fabric le impostazioni si applicano singolarmente a ogni servizio e ogni applicazione, invece che a una VM, perché una VM può ospitare più servizi e applicazioni. Un servizio è composto da tre pacchetti:

* **Code:** contiene i file eseguibili del servizio, i file binari, le DLL e qualsiasi altro file necessario per l'esecuzione di un servizio.
* **Config:** tutti i file di configurazione e le impostazioni per un servizio.
* **Data:** file di dati statici associati al servizio.

Per ognuno di questi pacchetti l'aggiornamento e il controllo delle versioni possono essere gestiti in modo indipendente. Analogamente a Servizi cloud, è possibile accedere a un pacchetto di configurazione a livello di codice tramite un'API e sono disponibili eventi per notificare al servizio una modifica del pacchetto di configurazione. È possibile usare un file Settings.xml per la configurazione di chiave-valore e l'accesso a livello di codice simile alla sezione app settings di un file App.config. Tuttavia, a differenza di Servizi cloud, un pacchetto di configurazione di Service Fabric può contenere qualsiasi file di configurazione in qualsiasi formato, ovvero XML, JSON, YAML o un formato binario personalizzato.

### Accesso alla configurazione
#### Microsoft Azure
Le impostazioni di configurazione di ServiceConfiguration.*.cscfg sono accessibili tramite `RoleEnvironment`. Queste impostazioni sono disponibili globalmente per tutte le istanze del ruolo nella stessa distribuzione di Servizi cloud.

```C#

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### Service Fabric
Ogni servizio ha un pacchetto di configurazione individuale. Non esiste un meccanismo predefinito per le impostazioni di configurazione globali accessibile da tutte le applicazioni in un cluster. Quando si usa il file di configurazione speciale Settings.xml di Service Fabric all'interno di un pacchetto di configurazione, i valori in Settings.XML possono essere sovrascritti a livello di applicazione, rendendo possibili le impostazioni di configurazione a livello di applicazione.

Alle impostazioni di configurazione è possibile accedere all'interno di ogni istanza del servizio tramite `CodePackageActivationContext` del servizio.

```C#

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### Eventi di aggiornamento della configurazione
#### Microsoft Azure
L'evento `RoleEnvironment.Changed` viene usato per notificare a tutte le istanze del ruolo quando si verifica una modifica nell'ambiente, ad esempio una modifica della configurazione. Viene usato per gli aggiornamenti della configurazione senza riciclare le istanze del ruolo o riavviare un processo di lavoro.

```C#

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### Service Fabric
Ognuno dei tre tipi di pacchetto in un servizio, ovvero Code, Config e Data, include eventi che inviano una notifica a un'istanza del servizio quando un pacchetto viene aggiornato, aggiunto o rimosso. Un servizio può contenere più pacchetti di ogni tipo. Ad esempio, un servizio può avere più pacchetti Config, ognuno aggiornabile e con controllo delle versioni gestito in modo indipendente.

Questi eventi sono disponibili per l'utilizzo in caso di modifiche nei pacchetti del servizio senza riavviare l'istanza del servizio.

```C#

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## Attività di avvio
Le attività di avvio sono azioni eseguite prima dell'avvio di un'applicazione. Un'attività di avvio viene in genere usata per eseguire script di installazione con privilegi elevati. Sia Servizi Cloud che Service Fabric supportano attività di avvio. La differenza principale riguarda il fatto che in Servizi cloud un'attività di avvio è collegata a una VM, perché fa parte di un'istanza del ruolo, mentre in Service Fabric un'attività di avvio è collegata a un servizio che non è associato a una VM specifica.

| Servizi cloud | Service Fabric |
| --- | --- | --- |
| Percorso di configurazione |ServiceDefinition.csdef |
| Privilegi |"limitato" o "con privilegi elevati" |
| Sequenziazione |"semplice", "background", "in primo piano" |

### Servizi cloud
In Servizi cloud è configurato un punto di ingresso di avvio per ogni ruolo in ServiceDefinition.csdef.

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### Service Fabric
In Service Fabric è configurato un punto di ingresso di avvio per ogni servizio in ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## Note sull'ambiente di sviluppo
Sia Servizi Cloud che Service Fabric sono integrati in Visual Studio con modelli di progetto e il supporto per il debug, la configurazione e la distribuzione sia locale che in Azure. Servizi cloud e Service Fabric forniscono anche un ambiente di runtime di sviluppo locale. La differenza è che mentre il runtime di sviluppo di Servizi cloud emula l'ambiente Azure in cui viene eseguito, Service Fabric non usa un emulatore, ma il runtime di Service Fabric completo. L'ambiente di Service Fabric che viene eseguito nel computer di sviluppo locale è lo stesso ambiente eseguito nell'ambiente di produzione.

## Passaggi successivi
Altre informazioni su Reliable Services di Service Fabric e le differenze fondamentali tra Servizi cloud e l'architettura dell'applicazione di Service Fabric per comprendere come sfruttare il set completo di funzionalità di Service Fabric.

* [Introduzione a Reliable Services di Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Guida concettuale alle differenze tra Servizi cloud e Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png

<!---HONumber=AcomDC_0803_2016-->