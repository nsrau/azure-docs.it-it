---
title: Funzionamento del codice con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi di esecuzione del codice nel servizio Azure Kubernetes con Azure Dev Spaces
keywords: azds. YAML, Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241361"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Funzionamento del codice con Azure Dev Spaces

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster di Azure Kubernetes Service (AKS). Quando il [progetto è pronto per l'esecuzione in uno spazio di sviluppo][how-it-works-prep], è possibile usare gli spazi di sviluppo per compilare ed eseguire il progetto nel cluster AKS.

Questo articolo descrive cosa accade per eseguire il codice in AKS con spazi di sviluppo.

## <a name="run-your-code"></a>Eseguire il codice

Per eseguire il codice in uno spazio di sviluppo, eseguire `up` il comando nella stessa directory del `azds.yaml` file:

```cmd
azds up
```

Il `up` comando carica i file di origine dell'applicazione e altri elementi necessari per compilare ed eseguire il progetto nello spazio di sviluppo. Da qui, il controller nello spazio di sviluppo:

1. Crea gli oggetti Kubernetes per distribuire l'applicazione.
1. Compila il contenitore per l'applicazione.
1. Distribuisce l'applicazione nello spazio di sviluppo.
1. Crea un nome DNS accessibile pubblicamente per l'endpoint dell'applicazione, se configurato.
1. Usa la *porta* per fornire l'accesso all'endpoint dell'applicazione usando http://localhost.
1. Invia stdout e stderr agli strumenti lato client.


## <a name="starting-a-service"></a>Avvio di un servizio

Quando si avvia un servizio in uno spazio di sviluppo, gli strumenti e il controller lato client funzionano in modo coordinato per sincronizzare i file di origine, creare gli oggetti contenitore e Kubernetes ed eseguire l'applicazione.

A un livello più granulare, ecco cosa accade quando si esegue `azds up`:

1. I [file vengono sincronizzati][sync-section] dal computer dell'utente a una risorsa di archiviazione file di Azure univoca per il cluster AKS dell'utente. Il codice sorgente, il grafico Helm e i file di configurazione vengono caricati.
1. Il controller crea una richiesta di avvio di una nuova sessione. Questa richiesta contiene diverse proprietà, tra cui un ID univoco, il nome dello spazio, il percorso del codice sorgente e un flag di debug.
1. Il controller sostituisce il segnaposto *$ (tag)* nel grafico Helm con l'ID di sessione univoco e installa il grafico Helm per il servizio. L'aggiunta di un riferimento all'ID di sessione univoco al grafico Helm consente al contenitore distribuito nel cluster AKS che la sessione specifica venga collegata alla richiesta di sessione e alle informazioni associate.
1. Durante l'installazione del grafico Helm, il server di ammissione del webhook Kubernetes aggiunge altri contenitori al Pod dell'applicazione per la strumentazione e l'accesso al codice sorgente del progetto. Vengono aggiunti i contenitori devspaces-proxy e devspaces-proxy-init per fornire la traccia HTTP e il routing dello spazio. Viene aggiunto il contenitore devspaces-Build per fornire al Pod l'accesso all'istanza Docker e al codice sorgente del progetto per la compilazione del contenitore dell'applicazione.
1. Quando viene avviato il Pod dell'applicazione, per compilare il contenitore dell'applicazione vengono usati il contenitore devspaces-Build e il contenitore devspaces-proxy-init. Vengono quindi avviati il contenitore dell'applicazione e i contenitori del proxy devspaces.
1. Dopo che il contenitore dell'applicazione è stato avviato, la funzionalità lato client usa la funzionalità di *portabilità in* Kubernetes per fornire l'accesso HTTP http://localhostall'applicazione tramite. Questa porta di trasmissione connette il computer di sviluppo al servizio nello spazio di sviluppo.
1. Quando sono stati avviati tutti i contenitori del Pod, il servizio è in esecuzione. A questo punto, le funzionalità lato client iniziano a trasmettere le tracce HTTP, stdout e stderr. Queste informazioni vengono visualizzate dalle funzionalità lato client per lo sviluppatore.

## <a name="updating-a-running-service"></a>Aggiornamento di un servizio in esecuzione

Mentre un servizio è in esecuzione, Azure Dev Spaces è in grado di aggiornare il servizio in caso di modifica dei file di origine del progetto. Lo spazio di sviluppo gestisce anche l'aggiornamento del servizio in modo diverso a seconda del tipo di file modificato. Esistono tre modi in cui gli spazi di sviluppo possono aggiornare un servizio in esecuzione:

* Aggiornamento diretto di un file
* Ricompilazione e riavvio del processo dell'applicazione all'interno del contenitore dell'applicazione in esecuzione
* Ricompilazione e ridistribuzione del contenitore dell'applicazione

![Sincronizzazione file di Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Alcuni file di progetto che sono asset statici, ad esempio file HTML, CSS e cshtml, possono essere aggiornati direttamente nel contenitore dell'applicazione senza riavviare nulla. Se un asset statico viene modificato, il nuovo file viene sincronizzato con lo spazio di sviluppo e quindi usato dal contenitore in esecuzione.

È possibile applicare modifiche ai file, ad esempio il codice sorgente o i file di configurazione dell'applicazione, riavviando il processo dell'applicazione all'interno del contenitore in esecuzione. Una volta sincronizzati questi file, il processo dell'applicazione viene riavviato all'interno del contenitore in esecuzione usando il processo *devhostagent* . Quando inizialmente si crea il contenitore dell'applicazione, il controller sostituisce il comando di avvio per l'applicazione con un processo diverso denominato *devhostagent*. Il processo effettivo dell'applicazione viene quindi eseguito come processo figlio in *devhostagent*e l'output viene reindirizzato tramite l'output di *devhostagent*. Il processo *devhostagent* fa anche parte di spazi di sviluppo ed è in grado di eseguire comandi nel contenitore in esecuzione per conto di spazi di sviluppo. Quando si esegue un riavvio, *devhostagent*:

* Arresta il processo o i processi correnti associati all'applicazione
* Ricompila l'applicazione
* Riavvia il processo o i processi associati all'applicazione

Il modo in cui *devhostagent* esegue i passaggi precedenti è [configurato `azds.yaml`in ][azds-yaml-section].

Per gli aggiornamenti dei file di progetto, ad esempio Dockerfile, file csproj o qualsiasi parte del grafico Helm, è necessario ricompilare e ridistribuire il contenitore dell'applicazione. Quando uno di questi file viene sincronizzato con lo spazio di sviluppo, il controller esegue il comando [Helm upgrade][helm-upgrade] e il contenitore dell'applicazione viene ricompilato e ridistribuito.

## <a name="file-synchronization"></a>Sincronizzazione file

La prima volta che un'applicazione viene avviata in uno spazio di sviluppo, vengono caricati tutti i file di origine dell'applicazione. Mentre l'applicazione è in esecuzione e in seguito viene riavviata, vengono caricati solo i file modificati. Per coordinare questo processo vengono usati due file: un file lato client e un file lato controller.

Il file lato client viene archiviato in una directory temporanea e viene denominato in base a un hash della directory del progetto in esecuzione negli spazi di sviluppo. Ad esempio, in Windows è presente un file come *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.SyncLog* per il progetto. In Linux, il file sul lato client è archiviato nella directory/ *tmp* . È possibile trovare la directory in macOS eseguendo il `echo $TMPDIR` comando.

Il file è in formato JSON e contiene:

* Una voce per ogni file di progetto sincronizzato con lo spazio di sviluppo
* ID sincronizzazione
* Timestamp dell'ultima operazione di sincronizzazione

Ogni voce del file di progetto contiene un percorso del file e il relativo timestamp.

Il file sul lato controller viene archiviato nel cluster AKS. Contiene l'ID sincronizzazione e il timestamp dell'ultima sincronizzazione.

Quando i timestamp di sincronizzazione non corrispondono tra i file lato client e lato controller, viene eseguita una sincronizzazione. Durante una sincronizzazione, tramite gli strumenti lato client viene eseguita l'iterazione delle voci di file nel file sul lato client. Se il timestamp del file è successivo al timestamp di sincronizzazione, il file viene sincronizzato con lo spazio di sviluppo. Al termine della sincronizzazione, i timestamp di sincronizzazione vengono aggiornati sia sui file lato client che sul lato controller.

Tutti i file di progetto vengono sincronizzati se il file sul lato client non è presente. Questo comportamento consente di forzare una sincronizzazione completa eliminando il file sul lato client.

## <a name="how-running-your-code-is-configured"></a>Configurazione dell'esecuzione del codice

Azure Dev Spaces usa il `azds.yaml` file per installare e configurare il servizio. Il controller usa la `install` proprietà nel `azds.yaml` file per installare il grafico Helm e creare gli oggetti Kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Per impostazione predefinita, `prep` il comando genererà il grafico Helm. Imposta anche la proprietà *Install. Chart* sulla directory del grafico Helm. Se si vuole usare un grafico Helm in una posizione diversa, è possibile aggiornare questa proprietà per usare tale percorso.

Quando si installano i grafici Helm, Azure Dev Spaces fornisce un modo per eseguire l'override dei valori nel grafico Helm. I valori predefiniti per il grafico Helm sono disponibili `charts/APP_NAME/values.yaml`in.

Utilizzando la proprietà *Install. Values* è possibile elencare uno o più file che definiscono i valori che si desidera sostituire nel grafico Helm. Se ad esempio si desidera una configurazione del nome host o del database in modo specifico durante l'esecuzione dell'applicazione in uno spazio di sviluppo, è possibile utilizzare questa funzionalità di sostituzione. È anche possibile aggiungere una *?* alla fine di uno dei nomi file per impostarlo come facoltativo.

La proprietà *Install. set* consente di configurare uno o più valori che devono essere sostituiti nel grafico Helm. Tutti i valori configurati in *Install. set* eseguiranno l'override dei valori configurati nei file elencati in *Install. Values*. Le proprietà in *Install. set* dipendono dai valori nel grafico Helm e possono essere diverse a seconda del grafico Helm generato.

Nell'esempio precedente, la proprietà *Install. set. replicaCount* indica al controller il numero di istanze dell'applicazione da eseguire nello spazio di sviluppo. A seconda dello scenario, è possibile aumentare questo valore, ma avrà un effetto sull'associazione di un debugger al Pod dell'applicazione. Per ulteriori informazioni, vedere l' [articolo sulla risoluzione dei problemi][troubleshooting].

Nel grafico Helm generato l'immagine del contenitore è impostata su *{{. Values. image. repository}}: {{. Values. image. Tag}}*. Per `azds.yaml` impostazione predefinita, il file definisce la proprietà *Install. set. image. Tag* come *$ (tag)* , che viene usata come valore per *{{. Values. image. Tag}}*. Impostando la proprietà *Install. set. image. Tag* in questo modo, l'immagine del contenitore per l'applicazione deve essere contrassegnata in modo distinto durante l'esecuzione di Azure Dev Spaces. In questo caso specifico, l'immagine è contrassegnata come * \<valore da Image. repository>: $ (tag)*. È necessario usare la variabile *$ (tag)* come valore di *Install. set. image. Tag* affinché gli spazi di sviluppo riconoscano e trovino il contenitore nel cluster AKS.

Nell'esempio precedente, `azds.yaml` definisce *Install. set. ingress. hosts*. La proprietà *Install. set. ingress. hosts* definisce un formato del nome host per gli endpoint pubblici. Questa proprietà usa anche *$ (spacePrefix)*, *$ (rootSpacePrefix)* e *$ (hostSuffix)*, che sono valori forniti dal controller.

*$ (SpacePrefix)* è il nome dello spazio di sviluppo figlio, che assume la forma di *spazioname. s*. *$ (RootSpacePrefix)* è il nome dello spazio padre. Se ad esempio *azureuser* è uno spazio figlio di *default*, il valore di *$ (rootSpacePrefix)* è *default* e il valore di *$ (spacePrefix)* è *azureuser. s*. Se lo spazio non è uno spazio figlio, *$ (spacePrefix)* è vuoto. Ad esempio, se lo spazio *predefinito* non dispone di spazio padre, il valore per *$ (rootSpacePrefix)* è *predefinito* e il valore di *$ (spacePrefix)* è vuoto. *$ (HostSuffix)* è un suffisso DNS che punta al controller di ingresso del Azure Dev Spaces in esecuzione nel cluster AKS. Questo suffisso DNS corrisponde a una voce DNS con caratteri jolly, ad esempio * \*. RANDOM_VALUE. EUS. azds. io*, creato quando è stato aggiunto il controller Azure Dev Spaces al cluster AKS.

Nel file precedente `azds.yaml` , è anche possibile aggiornare *Install. set. ingress. hosts* per modificare il nome host dell'applicazione. Ad esempio, se si vuole semplificare il nome host dell'applicazione da *$ (spacePrefix) $ (rootSpacePrefix) WebFrontEnd $ (hostSuffix)* a *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Per compilare il contenitore per l'applicazione, il controller usa le sezioni seguenti del file `azds.yaml` di configurazione:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Il controller usa un Dockerfile per compilare ed eseguire l'applicazione.

La proprietà *Build. Context* elenca la directory in cui si trovano i dockerfile. La proprietà *Build. dockerfile* definisce il nome di dockerfile per la compilazione della versione di produzione dell'applicazione. La proprietà Configurations *. develop. Build. dockerfile* configura il nome della dockerfile per la versione di sviluppo dell'applicazione.

La presenza di Dockerfile diversi per lo sviluppo e la produzione consente di abilitare determinate operazioni durante lo sviluppo e di disabilitare tali elementi per le distribuzioni di produzione. Ad esempio, è possibile abilitare il debug o la registrazione più dettagliata durante lo sviluppo e la disabilitazione in un ambiente di produzione. È anche possibile aggiornare queste proprietà se i Dockerfile sono denominati in modo diverso o si trovano in una posizione diversa.

Per consentire una rapida iterazione durante lo sviluppo, Azure Dev Spaces sincronizza le modifiche dal progetto locale e aggiorna in modo incrementale l'applicazione. La sezione seguente nel file `azds.yaml` di configurazione viene usata per configurare la sincronizzazione e l'aggiornamento:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

I file e le directory che sincronizzano le modifiche sono elencati nella proprietà *Configurations. develop. container. Sync* . Queste directory vengono sincronizzate inizialmente quando si esegue il `up` comando e quando vengono rilevate modifiche. Se sono presenti directory aggiuntive o diverse che si desidera sincronizzare con lo spazio di sviluppo, è possibile modificare questa proprietà.

La proprietà *Configurations. develop. container. Iteration. buildCommands* specifica come compilare l'applicazione in uno scenario di sviluppo. La proprietà *Configurations. develop. container. Command* fornisce il comando per l'esecuzione dell'applicazione in uno scenario di sviluppo. È possibile aggiornare una di queste proprietà se sono presenti flag o parametri di compilazione o di runtime aggiuntivi che si desidera utilizzare durante lo sviluppo.

In *Configurations. develop. container. Iteration. processesToKill* sono elencati i processi da terminare per arrestare l'applicazione. Potrebbe essere necessario aggiornare questa proprietà se si desidera modificare il comportamento di riavvio dell'applicazione durante lo sviluppo. Se ad esempio sono state aggiornate le proprietà *Configurations. develop. container. iter. buildCommands* o *Configurations. develop. container. Command* per modificare il modo in cui l'applicazione viene compilata o avviata, potrebbe essere necessario modificare i processi arrestati.

Quando si prepara il codice usando `azds prep` il comando, è possibile aggiungere il `--enable-ingress` flag. L'aggiunta `--enable-ingress` del flag consente di creare un URL accessibile pubblicamente per l'applicazione. Se si omette questo flag, l'applicazione è accessibile solo all'interno del cluster o usando il tunnel localhost. Dopo aver eseguito il `azds prep` comando, è possibile modificare questa impostazione modificando la proprietà *ingress. Enabled* in `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete e sul modo in cui le richieste vengono instradate in Azure Dev Spaces vedere [come funziona il routing con Azure Dev Spaces][how-it-works-routing].

Per ulteriori informazioni sull'utilizzo di Azure Dev Spaces per l'iterazione e lo sviluppo rapidi, vedere la pagina relativa [alla modalità di connessione del computer di sviluppo allo spazio di sviluppo][how-it-works-connect] e al funzionamento [del debug remoto del codice con Azure Dev Spaces][how-it-works-remote-debugging].

Per iniziare a usare Azure Dev Spaces per eseguire il progetto, vedere le guide introduttive seguenti:

* [Iterazione e debug rapidi con Visual Studio Code e Java][quickstart-java]
* [Iterazione e debug rapidi con Visual Studio Code e .NET][quickstart-netcore]
* [Iterazione e debug rapidi con Visual Studio Code e node. js][quickstart-node]
* [Iterazione e debug rapidi con Visual Studio e .NET Core][quickstart-vs]
* [Uso dell'interfaccia della riga di comando per sviluppare un'applicazione in Kubernetes][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md