---
title: Funzionamento e configurazione di Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Descrive i processi che consentono di Azure Dev Spaces e il modo in cui vengono configurati nel file di configurazione azds. YAML
keywords: azds. YAML, Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 9efae0e9d6bc53e08dce604fa79aa29e158ecabd
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280144"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Funzionamento e configurazione di Azure Dev Spaces

Lo sviluppo di un'applicazione Kubernetes può risultare complesso. Sono necessari i file di configurazione Docker e Kubernetes. È necessario capire come testare l'applicazione in locale e interagire con altri servizi dipendenti. Potrebbe essere necessario gestire lo sviluppo e il testing su più servizi contemporaneamente e con un team di sviluppatori.

Azure Dev Spaces consente di sviluppare, distribuire ed eseguire il debug di applicazioni Kubernetes direttamente nel servizio Azure Kubernetes (AKS). Azure Dev Spaces consente inoltre a un team di condividere uno spazio di sviluppo. La condivisione di uno spazio di sviluppo in un team consente ai singoli membri del team di svilupparsi in isolamento senza dover replicare o simulare dipendenze o altre applicazioni nel cluster.

Azure Dev Spaces crea e usa un file di configurazione per la distribuzione, l'esecuzione e il debug delle applicazioni Kubernetes in AKS. Questo file di configurazione risiede nel codice dell'applicazione e può essere aggiunto al sistema di controllo della versione.

Questo articolo descrive i processi che alimentano Azure Dev Spaces e il modo in cui tali processi sono configurati nel file di configurazione Azure Dev Spaces. Per ottenere Azure Dev Spaces rapidamente in esecuzione e visualizzarlo in pratica, completare una delle guide introduttive:

* [Java con CLI e Visual Studio Code](quickstart-java.md)
* [.NET Core con interfaccia della riga di comando e Visual Studio Code](quickstart-netcore.md)
* [.NET Core con Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js con l'interfaccia della riga di comando e Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Funzionamento di Azure Dev Spaces

Azure Dev Spaces dispone di due componenti distinti con cui si interagisce: il controller e gli strumenti lato client.

![Componenti Azure Dev Spaces](media/how-dev-spaces-works/components.svg)

Il controller esegue le azioni seguenti:

* Gestisce la creazione e la selezione dello spazio di sviluppo.
* Installa il grafico Helm dell'applicazione e crea gli oggetti Kubernetes.
* Compila l'immagine del contenitore dell'applicazione.
* Distribuisce l'applicazione in AKS.
* Esegue le compilazioni incrementali e viene riavviato quando il codice sorgente viene modificato.
* Gestisce i log e le tracce HTTP.
* Invia stdout e stderr agli strumenti lato client.
* Consente ai membri del team di creare spazi di sviluppo figlio derivati da uno spazio di sviluppo padre.
* Configura il routing per le applicazioni in uno spazio, oltre che negli spazi padre e figlio.

Il controller si trova all'esterno di AKS. Guida il comportamento e la comunicazione tra gli strumenti lato client e il cluster AKS. Il controller viene abilitato usando l'interfaccia della riga di comando di Azure quando si prepara il cluster per l'uso di Azure Dev Spaces. Una volta abilitata, è possibile interagire con esso utilizzando gli strumenti lato client.

Gli strumenti lato client consentono all'utente di:
* Generare un Dockerfile, un grafico Helm e un file di configurazione Azure Dev Spaces per l'applicazione.
* Creare spazi di sviluppo padre e figlio.
* Informare il controller di compilare e avviare l'applicazione.

Mentre l'applicazione è in esecuzione, anche gli strumenti lato client:
* Riceve e visualizza stdout e stderr dall'applicazione in esecuzione in AKS.
* Usa la [portabilità diretta](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) per consentire l'accesso Web all'applicazione tramite http:\//localhost.
* Connette un debugger all'applicazione in esecuzione in AKS.
* Sincronizza il codice sorgente allo spazio di sviluppo quando viene rilevata una modifica per le compilazioni incrementali, consentendo un'iterazione rapida.

È possibile usare gli strumenti lato client dalla riga di comando come parte del comando `azds`. È inoltre possibile utilizzare gli strumenti lato client con:

* Visual Studio Code usando l' [estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio con [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools).

Di seguito è riportato il flusso di base per la configurazione e l'uso di Azure Dev Spaces:
1. Preparare il cluster AKS per Azure Dev Spaces
1. Preparare il codice per l'esecuzione in Azure Dev Spaces
1. Eseguire il codice in uno spazio di sviluppo
1. Eseguire il debug del codice in uno spazio di sviluppo
1. Condividere uno spazio di sviluppo

Verranno illustrati altri dettagli sul funzionamento di Azure Dev Spaces in ognuna delle sezioni seguenti.

## <a name="prepare-your-aks-cluster"></a>Preparare il cluster AKS

La preparazione del cluster AKS comporta quanto segue:
* Verificare che il cluster AKS si trovi in un'area [supportata da Azure Dev Spaces][supported-regions].
* Verifica dell'esecuzione di Kubernetes 1.10.3 o versione successiva.
* Abilitazione di Azure Dev Spaces nel cluster con `az aks use-dev-spaces`

Per ulteriori informazioni su come creare e configurare un cluster AKS per Azure Dev Spaces, vedere una delle guide introduttive:
* [Inizia a usare Azure Dev Spaces con Java](get-started-java.md)
* [Inizia a usare Azure Dev Spaces con .NET Core e Visual Studio](get-started-netcore-visualstudio.md)
* [Inizia a usare Azure Dev Spaces con .NET Core](get-started-netcore.md)
* [Introduzione a Azure Dev Spaces con node. js](get-started-nodejs.md)

Quando Azure Dev Spaces è abilitato nel cluster AKS, viene installato il controller per il cluster. Il controller è una risorsa di Azure separata all'esterno del cluster ed esegue le operazioni seguenti per le risorse del cluster:

* Crea o designa uno spazio dei nomi Kubernetes da usare come spazio di sviluppo.
* Rimuove qualsiasi spazio dei nomi Kubernetes denominato *azds*, se esistente, e ne crea uno nuovo.
* Distribuisce una configurazione webhook Kubernetes.
* Distribuisce un server di ammissione del webhook.
    

USA anche la stessa entità servizio usata dal cluster AKS per effettuare chiamate al servizio ad altri componenti Azure Dev Spaces.

![Azure Dev Spaces preparare il cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Per usare Azure Dev Spaces, deve essere presente almeno uno spazio di sviluppo. Azure Dev Spaces usa gli spazi dei nomi Kubernetes all'interno del cluster AKS per gli spazi di sviluppo. Quando si installa un controller, viene richiesto di creare un nuovo spazio dei nomi Kubernetes o scegliere uno spazio dei nomi esistente da usare come primo spazio di sviluppo. Quando uno spazio dei nomi viene designato come spazio di sviluppo, il controller aggiunge l'etichetta *azds.io/Space=true* a tale spazio dei nomi per identificarlo come uno spazio di sviluppo. Lo spazio di sviluppo iniziale creato o designato è selezionato per impostazione predefinita dopo aver preparato il cluster. Quando si seleziona uno spazio, questo viene usato da Azure Dev Spaces per la creazione di nuovi carichi di lavoro.

Per impostazione predefinita, il controller crea uno spazio di sviluppo denominato *predefinito* aggiornando lo spazio dei nomi Kubernetes *predefinito* esistente. È possibile usare gli strumenti lato client per creare nuovi spazi di sviluppo e rimuovere gli spazi di sviluppo esistenti. A causa di una limitazione in Kubernetes, non è possibile rimuovere lo spazio di sviluppo *predefinito* . Il controller rimuove anche eventuali spazi dei nomi Kubernetes esistenti denominati *azds* per evitare conflitti con il comando `azds` usato dagli strumenti lato client.

Il server di ammissione del webhook Kubernetes viene usato per inserire i pod con tre contenitori durante la distribuzione per la strumentazione: un contenitore devspaces-proxy, un contenitore devspaces-proxy-init e un contenitore devspaces-Build. **Tutti e tre questi contenitori vengono eseguiti con accesso alla radice nel cluster AKS.** Usano anche la stessa entità servizio utilizzata dal cluster AKS per effettuare chiamate al servizio ad altri componenti Azure Dev Spaces.

![Server di ammissione del webhook Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Il contenitore devspaces-proxy è un contenitore sidecar che gestisce tutto il traffico TCP da e verso il contenitore dell'applicazione e consente di eseguire il routing. Il contenitore devspaces-Proxy reindirizza i messaggi HTTP se vengono usati determinati spazi. Ad esempio, può essere utile per indirizzare i messaggi HTTP tra le applicazioni negli spazi padre e figlio. Tutto il traffico non HTTP passa attraverso devspaces-proxy senza modifiche. Il contenitore devspaces-proxy registra anche tutti i messaggi HTTP in ingresso e in uscita e li invia agli strumenti lato client come tracce. Queste tracce possono quindi essere visualizzate dallo sviluppatore per esaminare il comportamento dell'applicazione.

Il contenitore devspaces-proxy-init è un [contenitore init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) che aggiunge regole di routing aggiuntive basate sulla gerarchia dello spazio al contenitore dell'applicazione. Aggiunge le regole di routing aggiornando il file */etc/resolv.conf* del contenitore dell'applicazione e la configurazione di iptables prima dell'avvio. Gli aggiornamenti di */etc/resolv.conf* consentono la risoluzione DNS dei servizi negli spazi padre. Gli aggiornamenti della configurazione di iptables assicurano che tutto il traffico TCP all'interno e all'esterno del contenitore dell'applicazione venga instradato tramite devspaces-proxy. Tutti gli aggiornamenti da devspaces-proxy-init si verificano oltre alle regole aggiunte da Kubernetes.

Il contenitore devspaces-Build è un contenitore init ed è installato il codice sorgente del progetto e il socket docker. Il codice sorgente del progetto e l'accesso a Docker consentono di compilare il contenitore dell'applicazione direttamente dal Pod.

> [!NOTE]
> Azure Dev Spaces usa lo stesso nodo per compilare il contenitore dell'applicazione ed eseguirlo. Di conseguenza, Azure Dev Spaces non necessita di un registro contenitori esterno per la compilazione e l'esecuzione dell'applicazione.

Il server di ammissione del webhook Kubernetes è in attesa di qualsiasi nuovo pod creato nel cluster AKS. Se il Pod viene distribuito in qualsiasi spazio dei nomi con l'etichetta *azds.io/Space=true* , inserisce tale Pod con i contenitori aggiuntivi. Il contenitore devspaces-Build viene inserito solo se il contenitore dell'applicazione viene eseguito con gli strumenti lato client.

Dopo aver preparato il cluster AKS, è possibile usare gli strumenti lato client per preparare ed eseguire il codice nello spazio di sviluppo.

## <a name="prepare-your-code"></a>Preparare il codice

Per eseguire l'applicazione in uno spazio di sviluppo, è necessario che venga inserito in un contenitore ed è necessario definire il modo in cui deve essere distribuita in Kubernetes. Per distribuire l'applicazione, è necessario un Dockerfile. Per definire il modo in cui l'applicazione viene distribuita in Kubernetes, è necessario un [grafico Helm](https://docs.helm.sh/). Per semplificare la creazione del grafico Dockerfile e Helm per l'applicazione, gli strumenti lato client forniscono il comando `prep`:

```cmd
azds prep --public
```

Il comando `prep` osserverà i file del progetto e tenterà di creare il grafico Dockerfile e Helm per l'esecuzione dell'applicazione in Kubernetes. Attualmente, il `prep` comando genererà un grafico Dockerfile e Helm con le seguenti lingue:

* Java
* Node.js
* .NET Core

È *necessario* eseguire il comando `prep` da una directory che contiene il codice sorgente. L'esecuzione del comando `prep` dalla directory corretta consente agli strumenti lato client di identificare la lingua e creare un Dockerfile appropriato per distribuire l'applicazione. È anche possibile eseguire il comando `prep` da una directory che contiene un file *POM. XML* per i progetti Java.

Se si esegue il comando `prep` dalla directory che non contiene codice sorgente, gli strumenti lato client non genereranno un Dockerfile. Verrà inoltre visualizzato un errore che informa che *non è stato possibile generare Dockerfile a causa della lingua non supportata*. Questo errore si verifica anche se gli strumenti lato client non riconoscono il tipo di progetto.

Quando si esegue il comando `prep`, è possibile specificare il flag di `--public`. Questo flag indica al controller di creare un endpoint accessibile da Internet per questo servizio. Se non si specifica questo flag, il servizio è accessibile solo dall'interno del cluster o usando il tunnel localhost creato dagli strumenti lato client. È possibile abilitare o disabilitare questo comportamento dopo l'esecuzione del `prep` comando aggiornando il grafico Helm generato.

Il comando `prep` non sostituisce i grafici Dockerfile o Helm esistenti presenti nel progetto. Se un grafico Dockerfile o Helm esistente utilizza la stessa convenzione di denominazione dei file generati dal comando `prep`, il `prep` comando ignorerà la generazione di tali file. In caso contrario, il `prep` comando genererà il proprio Dockerfile o il proprio grafico Helm lungo i file esistenti.

Il comando `prep` genererà anche un file di `azds.yaml` alla radice del progetto. Azure Dev Spaces usa questo file per compilare, installare, configurare ed eseguire l'applicazione. Questo file di configurazione elenca il percorso del grafico Dockerfile e Helm e fornisce anche una configurazione aggiuntiva su tali elementi.

Di seguito è riportato un esempio di file azds. YAML creato con l' [applicazione di esempio .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
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
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
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
```

Il file di `azds.yaml` generato dal comando `prep` dovrebbe funzionare correttamente per un semplice scenario di sviluppo di un progetto singolo. Se il progetto specifico presenta una maggiore complessità, potrebbe essere necessario aggiornare il file dopo l'esecuzione del comando `prep`. Ad esempio, il progetto potrebbe richiedere alcune modifiche al processo di compilazione o avvio in base alle esigenze di sviluppo e debug. È anche possibile che nel progetto siano presenti più applicazioni, che richiedono più processi di compilazione o un contenuto di compilazione diverso.

## <a name="run-your-code"></a>Eseguire il codice

Per eseguire il codice in uno spazio di sviluppo, eseguire il comando `up` nella stessa directory del file di `azds.yaml`:

```cmd
azds up
```

Il comando `up` carica i file di origine dell'applicazione e altri elementi necessari per compilare ed eseguire il progetto nello spazio di sviluppo. Da qui, il controller nello spazio di sviluppo:

1. Crea gli oggetti Kubernetes per distribuire l'applicazione.
1. Compila il contenitore per l'applicazione.
1. Distribuisce l'applicazione nello spazio di sviluppo.
1. Crea un nome DNS accessibile pubblicamente per l'endpoint dell'applicazione, se configurato.
1. Usa la *porta* per fornire l'accesso all'endpoint dell'applicazione usando http://localhost.
1. Invia stdout e stderr agli strumenti lato client.


### <a name="starting-a-service"></a>Avvio di un servizio

Quando si avvia un servizio in uno spazio di sviluppo, gli strumenti e il controller lato client funzionano in modo coordinato per sincronizzare i file di origine, creare gli oggetti contenitore e Kubernetes ed eseguire l'applicazione.

A un livello più granulare, ecco cosa accade quando si esegue `azds up`:

1. I file vengono sincronizzati dal computer dell'utente in una risorsa di archiviazione file di Azure univoca per il cluster AKS dell'utente. Il codice sorgente, il grafico Helm e i file di configurazione vengono caricati. Ulteriori informazioni sul processo di sincronizzazione sono disponibili nella sezione successiva.
1. Il controller crea una richiesta di avvio di una nuova sessione. Questa richiesta contiene diverse proprietà, tra cui un ID univoco, il nome dello spazio, il percorso del codice sorgente e un flag di debug.
1. Il controller sostituisce il segnaposto *$ (tag)* nel grafico Helm con l'ID di sessione univoco e installa il grafico Helm per il servizio. L'aggiunta di un riferimento all'ID di sessione univoco al grafico Helm consente al contenitore distribuito nel cluster AKS che la sessione specifica venga collegata alla richiesta di sessione e alle informazioni associate.
1. Durante l'installazione del grafico Helm, il server di ammissione del webhook Kubernetes aggiunge altri contenitori al Pod dell'applicazione per la strumentazione e l'accesso al codice sorgente del progetto. Vengono aggiunti i contenitori devspaces-proxy e devspaces-proxy-init per fornire la traccia HTTP e il routing dello spazio. Viene aggiunto il contenitore devspaces-Build per fornire al Pod l'accesso all'istanza Docker e al codice sorgente del progetto per la compilazione del contenitore dell'applicazione.
1. Quando viene avviato il Pod dell'applicazione, per compilare il contenitore dell'applicazione vengono usati il contenitore devspaces-Build e il contenitore devspaces-proxy-init. Vengono quindi avviati il contenitore dell'applicazione e i contenitori del proxy devspaces.
1. Dopo che il contenitore dell'applicazione è stato avviato, la funzionalità lato client usa la funzionalità di *portabilità in* Kubernetes per fornire l'accesso HTTP all'applicazione tramite http://localhost. Questa porta di trasmissione connette il computer di sviluppo al servizio nello spazio di sviluppo.
1. Quando sono stati avviati tutti i contenitori del Pod, il servizio è in esecuzione. A questo punto, le funzionalità lato client iniziano a trasmettere le tracce HTTP, stdout e stderr. Queste informazioni vengono visualizzate dalle funzionalità lato client per lo sviluppatore.

### <a name="updating-a-running-service"></a>Aggiornamento di un servizio in esecuzione

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

Il modo in cui *devhostagent* esegue i passaggi precedenti viene configurato nel file di configurazione `azds.yaml`. Questa configurazione è descritta in dettaglio in una sezione successiva.

Per gli aggiornamenti dei file di progetto, ad esempio Dockerfile, file csproj o qualsiasi parte del grafico Helm, è necessario ricompilare e ridistribuire il contenitore dell'applicazione. Quando uno di questi file viene sincronizzato con lo spazio di sviluppo, il controller esegue il comando [Helm upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) e il contenitore dell'applicazione viene ricompilato e ridistribuito.

### <a name="file-synchronization"></a>Sincronizzazione file

La prima volta che un'applicazione viene avviata in uno spazio di sviluppo, vengono caricati tutti i file di origine dell'applicazione. Mentre l'applicazione è in esecuzione e in seguito viene riavviata, vengono caricati solo i file modificati. Per coordinare questo processo vengono usati due file: un file lato client e un file lato controller.

Il file lato client viene archiviato in una directory temporanea e viene denominato in base a un hash della directory del progetto in esecuzione negli spazi di sviluppo. Ad esempio, in Windows è presente un file come *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.SyncLog* per il progetto. In Linux, il file sul lato client è archiviato nella directory/ *tmp* . È possibile trovare la directory in macOS eseguendo il comando `echo $TMPDIR`.

Il file è in formato JSON e contiene:

* Una voce per ogni file di progetto sincronizzato con lo spazio di sviluppo
* ID sincronizzazione
* Timestamp dell'ultima operazione di sincronizzazione

Ogni voce del file di progetto contiene un percorso del file e il relativo timestamp.

Il file sul lato controller viene archiviato nel cluster AKS. Contiene l'ID sincronizzazione e il timestamp dell'ultima sincronizzazione.

Quando i timestamp di sincronizzazione non corrispondono tra i file lato client e lato controller, viene eseguita una sincronizzazione. Durante una sincronizzazione, tramite gli strumenti lato client viene eseguita l'iterazione delle voci di file nel file sul lato client. Se il timestamp del file è successivo al timestamp di sincronizzazione, il file viene sincronizzato con lo spazio di sviluppo. Al termine della sincronizzazione, i timestamp di sincronizzazione vengono aggiornati sia sui file lato client che sul lato controller.

Tutti i file di progetto vengono sincronizzati se il file sul lato client non è presente. Questo comportamento consente di forzare una sincronizzazione completa eliminando il file sul lato client.

### <a name="how-routing-works"></a>Funzionamento del routing

Uno spazio di sviluppo si basa su AKS e usa gli stessi [concetti di rete](../aks/concepts-network.md). Azure Dev Spaces dispone anche di un servizio *ingressmanager* centralizzato e distribuisce il proprio controller di ingresso nel cluster AKS. Il servizio *ingressmanager* monitora i cluster AKS con spazi di sviluppo e aumenta il controller di ingresso Azure Dev Spaces nel cluster con gli oggetti in ingresso per il routing ai pod dell'applicazione. Il contenitore devspaces-proxy in ogni pod aggiunge un'intestazione HTTP `azds-route-as` per il traffico HTTP a uno spazio di sviluppo basato sull'URL. Ad esempio, una richiesta all'URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* otterrebbe un'intestazione HTTP con `azds-route-as: azureuser`. Il contenitore devspaces-proxy non aggiungerà un'intestazione `azds-route-as` se ne è già presente una.

Quando viene effettuata una richiesta HTTP a un servizio dall'esterno del cluster, la richiesta viene inviata al controller di ingresso. Il controller di ingresso instrada la richiesta direttamente al Pod appropriato in base alle regole e agli oggetti in ingresso. Il contenitore devspaces-proxy nel pod riceve la richiesta, aggiunge l'intestazione `azds-route-as` in base all'URL, quindi instrada la richiesta al contenitore dell'applicazione.

Quando viene effettuata una richiesta HTTP a un servizio da un altro servizio all'interno del cluster, la richiesta passa prima di tutto al contenitore devspaces-proxy del servizio chiamante. Il contenitore devspaces-proxy esamina la richiesta HTTP e controlla l'intestazione `azds-route-as`. In base all'intestazione, il contenitore del proxy di devspaces cercherà l'indirizzo IP del servizio associato al valore dell'intestazione. Se viene trovato un indirizzo IP, il contenitore devspaces-Proxy reindirizza la richiesta a tale indirizzo IP. Se non viene trovato un indirizzo IP, il contenitore devspaces-proxy instrada la richiesta al contenitore dell'applicazione padre.

Ad esempio, le applicazioni *servicea* e *serviceB* vengono distribuite in uno spazio di sviluppo padre denominato *default*. *servicea* si basa su *serviceB* ed effettua chiamate http. L'utente di Azure crea uno spazio di sviluppo figlio basato sullo spazio *predefinito* denominato *azureuser*. L'utente di Azure distribuisce anche la propria versione di *servicea* nello spazio figlio. Quando viene effettuata una richiesta per *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Routing Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Il controller di ingresso cerca l'IP per il Pod associato all'URL, che è *servicea. azureuser*.
1. Il controller di ingresso trova l'indirizzo IP per il Pod nello spazio di sviluppo dell'utente di Azure e instrada la richiesta al Pod *servicea. azureuser* .
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la richiesta e aggiunge `azds-route-as: azureuser` come intestazione HTTP.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* instrada la richiesta al contenitore dell'applicazione *servicea* nel pod *servicea. azureuser* .
1. L'applicazione *servicea* nel pod *servicea. azureuser* effettua una chiamata a *serviceB*. L'applicazione *servicea* contiene anche il codice per mantenere l'intestazione `azds-route-as` esistente, che in questo caso è `azds-route-as: azureuser`.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la richiesta e cerca l'IP di *serviceB* in base al valore dell'intestazione `azds-route-as`.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* non trova un IP per *serviceB. azureuser*.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* cerca l'IP per *serviceB* nello spazio padre, ovvero *serviceB. default*.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* trova l'indirizzo IP per *serviceB. default* e instrada la richiesta al Pod *serviceB. default* .
1. Il contenitore devspaces-proxy nel pod *serviceB. default* riceve la richiesta e instrada la richiesta al contenitore dell'applicazione *serviceb* nel pod *serviceB. default* .
1. L'applicazione *serviceB* nel pod *serviceB. default* restituisce una risposta al Pod *servicea. azureuser* .
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la risposta e instrada la risposta al contenitore dell'applicazione *servicea* nel pod *servicea. azureuser* .
1. L'applicazione *servicea* riceve la risposta e quindi restituisce la propria risposta.
1. Il contenitore devspaces-proxy nel pod *servicea. azureuser* riceve la risposta dal contenitore dell'applicazione *servicea* e instrada la risposta al chiamante originale all'esterno del cluster.

Tutto il resto del traffico TCP che non è HTTP passa attraverso il controller di ingresso e i contenitori devspaces-proxy non modificati.

### <a name="how-running-your-code-is-configured"></a>Configurazione dell'esecuzione del codice

Azure Dev Spaces usa il file di `azds.yaml` per installare e configurare il servizio. Il controller usa la proprietà `install` nel file `azds.yaml` per installare il grafico Helm e creare gli oggetti Kubernetes:

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

Per impostazione predefinita, il comando `prep` genererà il grafico Helm. Imposta anche la proprietà *Install. Chart* sulla directory del grafico Helm. Se si vuole usare un grafico Helm in una posizione diversa, è possibile aggiornare questa proprietà per usare tale percorso.

Quando si installano i grafici Helm, Azure Dev Spaces fornisce un modo per eseguire l'override dei valori nel grafico Helm. I valori predefiniti per il grafico Helm sono `charts/APP_NAME/values.yaml`.

Utilizzando la proprietà *Install. Values* è possibile elencare uno o più file che definiscono i valori che si desidera sostituire nel grafico Helm. Se ad esempio si desidera una configurazione del nome host o del database in modo specifico durante l'esecuzione dell'applicazione in uno spazio di sviluppo, è possibile utilizzare questa funzionalità di sostituzione. È anche possibile aggiungere una *?* alla fine di uno dei nomi file per impostarlo come facoltativo.

La proprietà *Install. set* consente di configurare uno o più valori che devono essere sostituiti nel grafico Helm. Tutti i valori configurati in *Install. set* eseguiranno l'override dei valori configurati nei file elencati in *Install. Values*. Le proprietà in *Install. set* dipendono dai valori nel grafico Helm e possono essere diverse a seconda del grafico Helm generato.

Nell'esempio precedente, la proprietà *Install. set. replicaCount* indica al controller il numero di istanze dell'applicazione da eseguire nello spazio di sviluppo. A seconda dello scenario, è possibile aumentare questo valore, ma avrà un effetto sull'associazione di un debugger al Pod dell'applicazione. Per ulteriori informazioni, vedere l' [articolo sulla risoluzione dei problemi](troubleshooting.md).

Nel grafico Helm generato l'immagine del contenitore è impostata su *{{. Values. image. repository}}: {{. Values. image. Tag}}* . Il file di `azds.yaml` definisce la proprietà *Install. set. image. Tag* come *$ (tag)* per impostazione predefinita, usata come valore per *{{. Values. image. Tag}}* . Impostando la proprietà *Install. set. image. Tag* in questo modo, l'immagine del contenitore per l'applicazione deve essere contrassegnata in modo distinto durante l'esecuzione di Azure Dev Spaces. In questo caso specifico, l'immagine viene contrassegnata come *\<valore da Image. repository >: $ (tag)* . È necessario usare la variabile *$ (tag)* come valore di *Install. set. image. Tag* affinché gli spazi di sviluppo riconoscano e trovino il contenitore nel cluster AKS.

Nell'esempio precedente `azds.yaml` definisce *Install. set. ingress. hosts*. La proprietà *Install. set. ingress. hosts* definisce un formato del nome host per gli endpoint pubblici. Questa proprietà usa anche *$ (spacePrefix)* , *$ (rootSpacePrefix)* e *$ (hostSuffix)* , che sono valori forniti dal controller. 

*$ (SpacePrefix)* è il nome dello spazio di sviluppo figlio, che assume la forma di *spazioname. s*. *$ (RootSpacePrefix)* è il nome dello spazio padre. Se ad esempio *azureuser* è uno spazio figlio di *default*, il valore di *$ (rootSpacePrefix)* è *default* e il valore di *$ (spacePrefix)* è *azureuser. s*. Se lo spazio non è uno spazio figlio, *$ (spacePrefix)* è vuoto. Ad esempio, se lo spazio *predefinito* non dispone di spazio padre, il valore per *$ (rootSpacePrefix)* è *predefinito* e il valore di *$ (spacePrefix)* è vuoto. *$ (HostSuffix)* è un suffisso DNS che punta al controller di ingresso del Azure Dev Spaces in esecuzione nel cluster AKS. Questo suffisso DNS corrisponde a una voce DNS con caratteri jolly, ad esempio *\*. RANDOM_VALUE. EUS. azds. io*, creato quando è stato aggiunto il controller Azure Dev Spaces al cluster AKS.

Nel file di `azds.yaml` precedente, è anche possibile aggiornare *Install. set. ingress. hosts* per modificare il nome host dell'applicazione. Ad esempio, se si vuole semplificare il nome host dell'applicazione da *$ (spacePrefix) $ (rootSpacePrefix) WebFrontEnd $ (hostSuffix)* a *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Per compilare il contenitore per l'applicazione, il controller usa le sezioni seguenti del file di configurazione `azds.yaml`:

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

Per consentire una rapida iterazione durante lo sviluppo, Azure Dev Spaces sincronizza le modifiche dal progetto locale e aggiorna in modo incrementale l'applicazione. La sezione seguente nel file di configurazione `azds.yaml` viene usata per configurare la sincronizzazione e l'aggiornamento:

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

Quando si prepara il codice usando il comando `azds prep`, è possibile aggiungere il flag di `--public`. L'aggiunta del flag `--public` crea un URL accessibile pubblicamente per l'applicazione. Se si omette questo flag, l'applicazione è accessibile solo all'interno del cluster o usando il tunnel localhost. Dopo aver eseguito il comando `azds prep`, è possibile modificare questa impostazione modificando la proprietà *ingress. Enabled* in `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Eseguire il debug del codice

Per le applicazioni Java, .NET e node. js, è possibile eseguire il debug dell'applicazione in esecuzione direttamente nello spazio di sviluppo usando Visual Studio Code o Visual Studio. Visual Studio Code e Visual Studio forniscono gli strumenti per connettersi allo spazio di sviluppo, avviare l'applicazione e collegare un debugger. Dopo l'esecuzione di `azds prep`, è possibile aprire il progetto in Visual Studio Code o in Visual Studio. Visual Studio Code o Visual Studio genererà i propri file di configurazione per la connessione, che è separato dall'esecuzione di `azds prep`. Dall'interno Visual Studio Code o Visual Studio, è possibile impostare punti di interruzione e avviare l'applicazione nello spazio di sviluppo.

![Debug del codice](media/get-started-node/debug-configuration-nodejs2.png)

Quando si avvia l'applicazione con Visual Studio Code o Visual Studio per il debug, questi gestiscono l'avvio e la connessione allo spazio di sviluppo in modo analogo all'esecuzione di `azds up`. Gli strumenti lato client in Visual Studio Code e Visual Studio forniscono anche un parametro aggiuntivo con informazioni specifiche per il debug. Il parametro contiene il nome dell'immagine del debugger, il percorso del debugger all'interno dell'immagine del debugger e il percorso di destinazione all'interno del contenitore dell'applicazione per montare la cartella del debugger.

L'immagine del debugger viene determinata automaticamente dagli strumenti lato client. Usa un metodo simile a quello usato durante la generazione di Dockerfile e Helm Chart durante l'esecuzione di `azds prep`. Quando il debugger viene montato nell'immagine dell'applicazione, viene eseguito utilizzando `azds exec`.

## <a name="sharing-a-dev-space"></a>Condivisione di uno spazio di sviluppo

Quando si lavora con un team, è possibile [condividere uno spazio di sviluppo in un intero team](how-to/share-dev-spaces.md) e creare spazi di sviluppo derivati. Uno spazio di sviluppo può essere usato da chiunque disponga dell'accesso come collaboratore al gruppo di risorse dello spazio di sviluppo.

È anche possibile creare un nuovo spazio di sviluppo derivato da un altro spazio di sviluppo. Quando si crea uno spazio di sviluppo derivato, l'etichetta *azds.io/Parent-Space=Parent-Space-Name* viene aggiunta allo spazio dei nomi dello spazio di sviluppo derivato. Inoltre, tutte le applicazioni dallo spazio di sviluppo padre vengono condivise con lo spazio di sviluppo derivato. Se si distribuisce una versione aggiornata di un'applicazione nello spazio di sviluppo derivato, questa sarà presente solo nello spazio di sviluppo derivato e lo spazio dev padre rimarrà inalterato. È possibile disporre di un massimo di tre livelli di spazi di sviluppo o di spazi *padre* derivati.

Lo spazio di sviluppo derivato effettuerà anche il routing intelligente delle richieste tra le proprie applicazioni e le applicazioni condivise dal relativo elemento padre. Il routing funziona tentando di indirizzare la richiesta a un'applicazione nello spazio di sviluppo derivato e di eseguire il fallback all'applicazione condivisa dallo spazio di sviluppo padre. Se l'applicazione non si trova nello spazio padre, il routing verrà eseguito nuovamente nell'applicazione condivisa nell'area del padre.

Ad esempio:
* Il *valore predefinito* dello spazio di sviluppo include le applicazioni *servicea* e *serviceB* .
* Il *valore predefinito*è derivato dallo spazio di sviluppo *azureuser* .
* Una versione aggiornata di *servicea* viene distribuita a *azureuser*.

Quando si usa *azureuser*, tutte le richieste a *servicea* verranno indirizzate alla versione aggiornata in *azureuser*. Una richiesta a *serviceB* tenterà prima di essere indirizzata alla versione *azureuser* di *serviceB*. Poiché non esiste, verrà instradato alla versione *predefinita* di *serviceB*. Se viene rimossa la versione *azureuser* di *servicea* , tutte le richieste a *servicea* eseguiranno il fallback utilizzando la versione *predefinita* di *servicea*.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Dev Spaces, vedere le guide introduttive seguenti:

* [Java con CLI e Visual Studio Code](quickstart-java.md)
* [.NET Core con interfaccia della riga di comando e Visual Studio Code](quickstart-netcore.md)
* [.NET Core con Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js con l'interfaccia della riga di comando e Visual Studio Code](quickstart-nodejs.md)

Per iniziare a usare lo sviluppo in team, vedere gli articoli procedure seguenti:

* [Sviluppo team-Java con CLI e Visual Studio Code](team-development-java.md)
* [Sviluppo team-.NET Core con CLI e Visual Studio Code](team-development-netcore.md)
* [Sviluppo team-.NET Core con Visual Studio](team-development-netcore-visualstudio.md)
* [Sviluppo team-node. js con CLI e Visual Studio Code](team-development-nodejs.md)



[supported-regions]: about.md#supported-regions-and-configurations
