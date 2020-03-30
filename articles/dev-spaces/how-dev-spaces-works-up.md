---
title: Funzionamento dell'esecuzione del codice con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi di esecuzione del codice nel servizio Azure Kubernetes con Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241361"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Funzionamento dell'esecuzione del codice con Azure Dev Spaces

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes (AKS). Una volta che il progetto è pronto per l'esecuzione in uno spazio di [sviluppo,][how-it-works-prep]è possibile utilizzare Dev Spaces per compilare ed eseguire il progetto nel cluster AKS.

Questo articolo descrive cosa accade eseguire il codice in AKS con Dev Spaces.This article describes what happens run your code in AKS with Dev Spaces.

## <a name="run-your-code"></a>Eseguire il codiceRun your code

Per eseguire il codice in uno `up` spazio di sviluppo, `azds.yaml` eseguire il comando nella stessa directory del file:

```cmd
azds up
```

Il `up` comando carica i file di origine dell'applicazione e altri elementi necessari per compilare ed eseguire il progetto nello spazio di sviluppo. Da lì, il controller nel tuo spazio di sviluppo:

1. Crea gli oggetti Kubernetes per distribuire l'applicazione.
1. Compila il contenitore per l'applicazione.
1. Distribuisce l'applicazione nello spazio di sviluppo.
1. Crea un nome DNS accessibile pubblicamente per l'endpoint dell'applicazione, se configurato.
1. Utilizza *portforward* per fornire l'accesso http://localhostall'endpoint dell'applicazione tramite .
1. Inoltra stdout e stderr agli strumenti lato client.


## <a name="starting-a-service"></a>Avvio di un servizio

Quando si avvia un servizio in uno spazio di sviluppo, gli strumenti lato client e il controller lavorano in coordinamento per sincronizzare i file di origine, creare il contenitore e Kubernetes oggetti ed eseguire l'applicazione.

A un livello più granulare, ecco `azds up`cosa succede quando si esegue :

1. [I file vengono sincronizzati][sync-section] dal computer dell'utente a un'archiviazione di file di Azure univoca per il cluster AKS dell'utente. Vengono caricati il codice sorgente, il grafico Helm e i file di configurazione.
1. Il controller crea una richiesta per avviare una nuova sessione. Questa richiesta contiene diverse proprietà, tra cui un ID univoco, il nome dello spazio, il percorso del codice sorgente e un flag di debug.
1. Il controller sostituisce il segnaposto *(tag)* nel grafico Helm con l'ID di sessione univoco e installa il grafico Helm per il servizio. L'aggiunta di un riferimento all'ID di sessione univoco al grafico Helm consente al contenitore distribuito nel cluster AKS per questa sessione specifica di essere associato alla richiesta di sessione e alle informazioni associate.
1. Durante l'installazione del grafico Helm, il server di ammissione webhook Kubernetes aggiunge contenitori aggiuntivi al pod dell'applicazione per la strumentazione e l'accesso al codice sorgente del progetto. I contenitori devspaces-proxy-proxy e devspaces-proxy-init vengono aggiunti per fornire la traccia HTTP e il routing dello spazio. Il contenitore devspaces-build viene aggiunto per fornire al contenitore l'accesso all'istanza Docker e al codice sorgente del progetto per la compilazione del contenitore dell'applicazione.
1. Quando viene avviato il pod dell'applicazione, il contenitore devspaces-build e il contenitore devspaces-proxy-init vengono usati per compilare il contenitore dell'applicazione. Vengono quindi avviati il contenitore dell'applicazione e i contenitori devspace-proxy.
1. Dopo l'avvio del contenitore dell'applicazione, la funzionalità lato client utilizza la funzionalità http://localhostdi *portforward* Kubernetes per fornire l'accesso HTTP all'applicazione tramite . Questo port forwarding connette il computer di sviluppo al servizio nello spazio di sviluppo.
1. Quando tutti i contenitori nel pod sono stati avviati, il servizio è in esecuzione. A questo punto, la funzionalità lato client inizia a trasmettere le tracce HTTP, stdout e stderr. Queste informazioni vengono visualizzate dalla funzionalità lato client per lo sviluppatore.

## <a name="updating-a-running-service"></a>Aggiornamento di un servizio in esecuzioneUpdating a running service

Mentre un servizio è in esecuzione, Azure Dev Spaces ha la possibilità di aggiornare il servizio se uno dei file di origine del progetto viene modificato. Dev Spaces gestisce inoltre l'aggiornamento del servizio in modo diverso a seconda del tipo di file modificato. Esistono tre modi per aggiornare un servizio in esecuzione in cui è possibile aggiornare un servizio in esecuzione:There are three ways Dev Spaces can update a running service:

* Aggiornamento diretto di un file
* Ricostruzione e riavvio del processo dell'applicazione all'interno del contenitore dell'applicazione in esecuzione
* Ricompilazione e ridistribuzione del contenitore dell'applicazioneRebuilding and redeploying the application's container

![Sincronizzazione dei file di Azure Dev SpacesAzure Dev Spaces file sync](media/how-dev-spaces-works/file-sync.svg)

Alcuni file di progetto che sono risorse statiche, ad esempio i file html, css e cshtml, possono essere aggiornati direttamente nel contenitore dell'applicazione senza riavviare nulla. Se un asset statico viene modificato, il nuovo file viene sincronizzato con lo spazio di sviluppo e quindi utilizzato dal contenitore in esecuzione.

Le modifiche apportate ai file, ad esempio il codice sorgente o i file di configurazione dell'applicazione, possono essere applicate riavviando il processo dell'applicazione all'interno del contenitore in esecuzione. Una volta sincronizzati questi file, il processo dell'applicazione viene riavviato all'interno del contenitore in esecuzione utilizzando il processo *devhostagent.* Quando si crea inizialmente il contenitore dell'applicazione, il controller sostituisce il comando di avvio per l'applicazione con un processo diverso denominato *devhostagent*. Il processo effettivo dell'applicazione viene quindi eseguito come processo figlio in *devhostagent*e il relativo output viene reindirizzato utilizzando l'output di *devhostagent.* Il processo *devhostagent* fa anche parte di Dev Spaces e può eseguire comandi nel contenitore in esecuzione per conto di Dev Spaces. Quando si esegue un riavvio, *devhostagent*:

* Interrompe il processo o i processi correnti associati all'applicazione
* Ricostruisce l'applicazione
* Riavvia il processo o i processi associati all'applicazione

Il modo in cui *devhostagent* esegue i passaggi precedenti è [ `azds.yaml`configurato in ][azds-yaml-section].

Gli aggiornamenti ai file di progetto, ad esempio Dockerfiles, csproj file o qualsiasi parte del grafico Helm, richiedono la ricompilazione e la ridistribuzione del contenitore dell'applicazione. Quando uno di questi file viene sincronizzato con lo spazio di sviluppo, il controller esegue il comando [helm upgrade][helm-upgrade] e il contenitore dell'applicazione viene ricompilato e ridistribuito.

## <a name="file-synchronization"></a>Sincronizzazione dei file

La prima volta che un'applicazione viene avviata in uno spazio di sviluppo, vengono caricati tutti i file di origine dell'applicazione. Mentre l'applicazione è in esecuzione e in un secondo momento viene riavviato, vengono caricati solo i file modificati. Per coordinare questo processo vengono utilizzati due file: un file sul lato client e un file sul lato controller.

Il file sul lato client viene archiviato in una directory temporanea e viene denominato in base a un hash della directory del progetto in esecuzione in Dev Spaces. In Windows, ad esempio, si potrebbe disporre di un file come *Utenti, NOME UTENTE, AppData, Locale, Temp, 1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdeflog per* il progetto. In Linux, il file sul lato client viene archiviato nella directory */tmp.* È possibile trovare la directory su `echo $TMPDIR` macOS eseguendo il comando.

Questo file è in formato JSON e contiene:This file is in JSON format and contains:

* Una voce per ogni file di progetto sincronizzato con lo spazio di sviluppo
* Un ID di sincronizzazione
* Timestamp dell'ultima operazione di sincronizzazione

Ogni voce del file di progetto contiene un percorso al file e il relativo timestamp.

Il file sul lato controller viene archiviato nel cluster AKS. Contiene l'ID di sincronizzazione e il timestamp dell'ultima sincronizzazione.

Si verifica una sincronizzazione quando i timestamp di sincronizzazione non corrispondono tra il lato client e i file sul lato controller. Durante una sincronizzazione, lo strumento sul lato client scorre le voci del file nel file lato client. Se il timestamp del file è dopo il timestamp di sincronizzazione, tale file viene sincronizzato con lo spazio di sviluppo. Una volta completata la sincronizzazione, i timestamp di sincronizzazione vengono aggiornati sia nei file sul lato client che sul lato controller.

Tutti i file di progetto vengono sincronizzati se il file sul lato client non è presente. Questo comportamento consente di forzare una sincronizzazione completa eliminando il file sul lato client.

## <a name="how-running-your-code-is-configured"></a>Come è configurato l'esecuzione del codiceHow running your code is configured

Azure Dev Spaces `azds.yaml` usa il file per installare e configurare il servizio. Il controller `install` utilizza la `azds.yaml` proprietà nel file per installare il grafico Helm e creare gli oggetti Kubernetes:

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

Per impostazione `prep` predefinita, il comando genererà il grafico Helm. Imposta inoltre la proprietà *install.chart* sulla directory del grafico Helm. Se si desidera utilizzare un grafico Helm in una posizione diversa, è possibile aggiornare questa proprietà per utilizzare tale posizione.

Quando si installano i grafici Helm, Azure Dev Spaces offre un modo per eseguire l'override dei valori nel grafico Helm.When installing the Helm charts, Azure Dev Spaces provides a way to override values in the Helm chart. I valori predefiniti per il `charts/APP_NAME/values.yaml`grafico Helm sono in .

Utilizzando la proprietà *install.values,* è possibile elencare uno o più file che definiscono i valori da sostituire nel grafico Helm. Ad esempio, se si desidera un nome host o una configurazione di database in modo specifico quando si esegue l'applicazione in uno spazio di sviluppo, è possibile utilizzare questa funzionalità di override. È anche possibile aggiungere un *?* alla fine di uno dei nomi di file per impostarlo come facoltativo.

La proprietà *install.set* consente di configurare uno o più valori che si desidera sostituire nel grafico Helm. Tutti i valori configurati in *install.set* sostituiranno i valori configurati nei file elencati in *install.values*. Le proprietà in *install.set* dipendono dai valori nel grafico Helm e possono essere diverse a seconda del grafico Helm generato.

Nell'esempio precedente, la proprietà *install.set.replicaCount* indica al controller il numero di istanze dell'applicazione da eseguire nello spazio di sviluppo. A seconda dello scenario, è possibile aumentare questo valore, ma avrà un impatto sulla connessione di un debugger al pod dell'applicazione. Per ulteriori informazioni, vedere [l'articolo sulla risoluzione dei problemi][troubleshooting].

Nel grafico Helm generato, l'immagine del contenitore è impostata su *. Values.image.repository : . . . . . . . . . . . . . . . . . . . . . . . . . . . . . Values.image.tag.* Il `azds.yaml` file definisce la proprietà *install.set.image.tag* come per impostazione predefinita come *(tag),* che viene utilizzata come valore per *. Values.image.tag.* Impostando la proprietà install.set.image.tag in questo modo, consente all'immagine contenitore per l'applicazione di essere contrassegnata in modo distinto quando si eseguono spazi di sviluppo di Azure.By setting the *install.set.image.tag* property in this way, it allows the container image for your application to be tagged in a distinct way when running Azure Dev Spaces. In questo caso specifico, l'immagine viene contrassegnata come * \<valore da image.repository>:* È necessario utilizzare la variabile *di* tag come valore di *install.set.image.tag* per consentire agli spazi di sviluppo di riconoscere e individuare il contenitore nel cluster AKS.

Nell'esempio precedente, `azds.yaml` defines *install.set.ingress.hosts*. La proprietà *install.set.ingress.hosts* definisce un formato di nome host per gli endpoint pubblici. In questa proprietà *vengono*inoltre utilizzati i valori forniti dal controller, *(rootSpacePrefix)*, e *(hostSuffix),* che sono i valori forniti dal controller.

Il valore *di (spacePrefix)* è il nome dello spazio di sviluppo figlio, che assume la forma di *SPACENAME.s*. Il *prefisso di rootSpace è* il nome dello spazio padre. Se, ad esempio, *azureuser* è uno spazio figlio *predefinito*, il valore di *(rootSpacePrefix)* è *il valore predefinito* e il valore di *(spacePrefix)* è *azureuser.s*. Se lo spazio non è uno spazio figlio, il valore *di (spacePrefix)* è vuoto. Ad esempio, se lo spazio *predefinito* non dispone di alcuno spazio padre, il valore di *(rootSpacePrefix)* è *il valore predefinito* e il valore di *(spacePrefix)* è vuoto. Il suffisso di ingresso di Host *Suffix* che punta al controller di ingresso Azure Dev Spaces in esecuzione nel cluster AKS. Questo suffisso DNS corrisponde a una voce DNS con caratteri jolly, ad esempio * \*. RANDOM_VALUE.eus.azds.io*, creata quando il controller Azure Dev Spaces è stato aggiunto al cluster AKS.

Nel file `azds.yaml` precedente è anche possibile aggiornare *install.set.ingress.hosts* per modificare il nome host dell'applicazione. Se, ad esempio, si desidera semplificare il nome host dell'applicazione da * (prefisso ) (prefissoprincipale) (rootSpacePrefix)* *(spacciatore) (con estensione (prefisso).*

Per compilare il contenitore per l'applicazione, `azds.yaml` il controller utilizza le sezioni seguenti del file di configurazione:

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

La proprietà *build.context* elenca la directory in cui sono presenti i file Docker. La proprietà *build.dockerfile* definisce il nome del file Docker per la compilazione della versione di produzione dell'applicazione. La proprietà *configurations.develop.build.dockerfile* configura il nome del file Docker per la versione di sviluppo dell'applicazione.

La presenza di file Docker diversi per lo sviluppo e la produzione consente di abilitare determinate operazioni durante lo sviluppo e di disabilitare tali elementi per le distribuzioni di produzione. Ad esempio, è possibile abilitare il debug o una registrazione più dettagliata durante lo sviluppo e disabilitarla in un ambiente di produzione. È inoltre possibile aggiornare queste proprietà se i file Docker sono denominati in modo diverso o si trovano in una posizione diversa.

Per facilitare l'iterazione durante lo sviluppo, Azure Dev Spaces sincronizzerà le modifiche del progetto locale e aggiornerà in modo incrementale l'applicazione. La sezione seguente `azds.yaml` nel file di configurazione viene utilizzata per configurare la sincronizzazione e l'aggiornamento:

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

I file e le directory che sincronizzeranno le modifiche sono elencati nella proprietà *configurations.develop.container.sync.* Queste directory vengono sincronizzate inizialmente `up` quando si esegue il comando e quando vengono rilevate modifiche. Se sono presenti directory aggiuntive o diverse che si desidera sincronizzare con lo spazio di sviluppo, è possibile modificare questa proprietà.

La proprietà *configurations.develop.container.iterate.buildCommands* specifica come compilare l'applicazione in uno scenario di sviluppo. La proprietà *configurations.develop.container.command* fornisce il comando per l'esecuzione dell'applicazione in uno scenario di sviluppo. È possibile aggiornare una di queste proprietà se sono presenti flag di compilazione o di runtime aggiuntivi o parametri che si desidera utilizzare durante lo sviluppo.

I processi di cui è possibile arrestare il processo da arrestare per arrestare l'applicazione sono *elencati.* È possibile aggiornare questa proprietà se si desidera modificare il comportamento di riavvio dell'applicazione durante lo sviluppo. Ad esempio, se sono state aggiornate le proprietà *configurations.develop.container.buildCommands* o *configurations.develop.container.command* per modificare la modalità di compilazione o avvio dell'applicazione, potrebbe essere necessario modificare i processi arrestati.

Quando si prepara `azds prep` il codice utilizzando il comando, è possibile aggiungere il `--enable-ingress` flag. L'aggiunta del `--enable-ingress` flag crea un URL accessibile pubblicamente per l'applicazione. Se si olascia questo flag, l'applicazione è accessibile solo all'interno del cluster o utilizzando il tunnel localhost. Dopo aver `azds prep` eseguito il comando, è possibile modificare questa `charts/APPNAME/values.yaml`impostazione modificando la proprietà *ingress.enabled* in :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete e sul modo in cui le richieste vengono instradate in Spazi di sviluppo di Azure, vedere [Funzionamento del routing con Azure Dev Spaces.][how-it-works-routing]

Per altre informazioni sull'uso degli spazi di sviluppo di Azure per l'iterazione e lo sviluppo rapidi, vedere [Come funziona la connessione del computer][how-it-works-connect] di sviluppo allo spazio di sviluppo e Come funziona il debug remoto del codice con Azure Dev [Spaces.][how-it-works-remote-debugging]

Per iniziare a usare Azure Dev Spaces per eseguire il progetto, vedere le guide introduttive seguenti:To get started using Azure Dev Spaces to run your project, see the following quickstarts:

* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e Java][quickstart-java]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e .NET][quickstart-netcore]
* [Eseguire rapidamente l'iterazione e il debug con Visual Studio Code e Node.js][quickstart-node]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio e .NET Core][quickstart-vs]
* [Utilizzo della CLI per sviluppare un'applicazione su Kubernetes][quickstart-cli]


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