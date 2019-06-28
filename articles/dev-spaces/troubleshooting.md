---
title: risoluzione dei problemi
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: e0379bbc7f26ea30f65c5eac73633ca0371aa283
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331314"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

Se si verifica un problema quando si usa spazi di sviluppo di Azure, creare un [problema nel repository GitHub di Azure Dev spazi](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Abilitazione della registrazione dettagliata

Per risolvere i problemi in modo più efficace, può essere utile creare log maggiormente dettagliati per la revisione.

Per l'estensione di Visual Studio impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati vengono scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i log più dettagliati in `%TEMP%\Azure Dev Spaces`. In un computer Mac la directory TEMP è reperibile eseguendo `echo $TMPDIR` da una finestra del terminale. In un computer Linux la directory TEMP è generalmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debug dei servizi con più istanze

Azure Dev Spaces attualmente funziona meglio durante il debug di una singola istanza, o pod. Il file azds.yaml contiene un'impostazione, *replicaCount*, che indica il numero di pod eseguiti da Kubernetes per il servizio. Se si modifica replicaCount per configurare l'app in modo che esegua più pod per un determinato servizio, il debugger si collega al primo pod, se elencati in ordine alfabetico. Il debugger si collega a un pod diverso quando il pod originale viene riciclato, determinando un comportamento imprevisto.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Errore "Failed to create Azure Dev Spaces controller" (Non è stato possibile creare il controller di Azure Dev Spaces)

### <a name="reason"></a>`Reason`
In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, per correggerlo, eliminare e ricreare il controller.

### <a name="try"></a>Prova

Eliminare il controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

È necessario utilizzare la CLI di spazi di sviluppo di Azure per eliminare un controller. Non è possibile eliminare un controller da Visual Studio. È anche possibile installare CLI Azure Dev spazi in Azure Cloud Shell in modo da Azure Cloud Shell non è possibile eliminare un controller.

Se non si dispone di spazi di sviluppo Azure CLI installata, è possibile installarlo usando il comando seguente quindi eliminare il controller:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. Vedere le [Team di sviluppo](quickstart-team-development.md) oppure [sviluppare con .NET Core](quickstart-netcore-visualstudio.md) guide introduttive per gli esempi.

## <a name="error-service-cannot-be-started"></a>Errore "Service cannot be started" (Impossibile avviare il servizio).

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. Per ottenere altre informazioni di diagnostica, apportare le modifiche seguenti ai comandi e alle impostazioni:

### <a name="try"></a>Soluzione:

Sulla riga di comando:

Quando si usa _azds.exe_, usare l'opzione della riga di comando --verbose e usare l'opzione della riga di comando --output per specificare il formato di output.
 
```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Aprire **Strumenti > Opzioni** e in **Projects and Solutions** (Progetti e soluzioni), scegliere **Build and Run** (Compila ed esegui).
2. Modificare le impostazioni per **MSBuild project build output verbosity** (Livello di dettaglio output di compilazione progetto MSBuild) in **Detailed** (Dettagliato) oppure in **Diagnostic** (Diagnostica).

    ![Screenshot della finestra di dialogo Strumenti > Opzioni](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Dockerfile a più fasi:
Viene visualizzato l'errore *Service cannot be started* quando si usa un documento Dockerfile a più fasi. In questo caso, l'output dettagliato contiene il testo seguente:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Questo errore si verifica perché i nodi del servizio Azure Kubernetes eseguono una versione precedente di Docker che non supporta compilazioni in più fasi. Per evitare compilazioni in più fasi, riscrivere il documento Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Riesecuzione di un servizio dopo la ripetizione della creazione del controller
Viene visualizzato l'errore *Service cannot be started* quando si prova a rieseguire un servizio dopo la rimozione e quindi la ricreazione del controller Azure Dev Spaces associato al cluster. In questo caso, l'output dettagliato contiene il testo seguente:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Questo errore si verifica perché la rimozione del controller Dev Spaces non comporta la rimozione dei servizi installati in precedenza da tale controller. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

È possibile configurare un endpoint URL pubblico per il servizio specificando l'opzione `--public` per il comando `azds prep` o selezionando la casella di controllo `Publicly Accessible` in Visual Studio. Il nome DNS pubblico viene registrato automaticamente quando si esegue il servizio in Dev Spaces. Se questo nome DNS non viene registrato, viene visualizzato un errore simile a *Non è possibile visualizzare la pagina* o *Non è possibile raggiungere il sito* nel Web browser quando ci si connette all'URL pubblico.

### <a name="try"></a>Soluzione:

È possibile usare il comando seguente per elencare tutti gli URL associati ai servizi Dev Spaces:

```cmd
azds list-uris
```

Se un URL mostra lo stato *In sospeso* significa che Dev Spaces è ancora in attesa di completamento della registrazione DNS. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.

Se un URL rimane nello stato *In sospeso* per più di 5 minuti, ciò potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico o con il pod del controller in ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. È possibile usare i comandi seguenti per eliminare i POD. Il servizio Azure Kubernetes ricrea automaticamente i pod eliminati.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Errore "Required tools and configurations are missing" (Gli strumenti e le configurazioni richiesti sono mancanti)

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

### <a name="try"></a>Soluzione:

Avviare VS Code da un prompt dei comandi in cui la variabile di ambiente PATH è impostata correttamente.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Errore "Required tools to build and debug 'projectname' are out of date" (Gli strumenti necessari per la compilazione e il debug di 'nome progetto' non sono aggiornati)

Questo errore viene visualizzato in Visual Studio Code se sono presenti una versione dell'estensione VS Code per Azure Dev Spaces più recente e una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

### <a name="try"></a>Prova

Scaricare e installare l'ultima versione dell'interfaccia della riga di comando di Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Errore 'azds' non riconosciuto come comando interno o esterno, programma eseguibile o file batch
 
È possibile visualizzare questo errore se azds.exe non è installato o configurato correttamente.

### <a name="try"></a>Soluzione:

1. Controllare la posizione %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev spazi CLI per azds.exe. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se azds.exe non è installato, eseguire il comando seguente:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avviso 'Dockerfile could not be generated due to unsupported language' (Dockerfile non generato a causa di un linguaggio non supportato)
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. Quando si esegue *azds prep* in una directory contenente codice scritto in uno di questi linguaggi, Azure Dev Spaces creerà automaticamente un Dockerfile appropriato per l'utente.

È comunque possibile usare Azure Dev Spaces con il codice scritto in altri linguaggi, ma è necessario creare manualmente il documento Dockerfile prima di eseguire *azds up* per la prima volta.

### <a name="try"></a>Soluzione:
Se l'applicazione è scritta in un linguaggio non supportato da Azure Dev Spaces in modo nativo, è necessario fornire un documento Dockerfile appropriato per creare un'immagine del contenitore che esegua il codice. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e [informazioni di riferimento su Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un documento Dockerfile in base alle proprie esigenze.

Quando si dispone di un Dockerfile appropriato è possibile procedere con l'esecuzione di *azds up* per eseguire l'applicazione in Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Errore "upstream connect error or disconnect/reset before headers" (errore di connessione a monte o disconnessione/reset prima delle intestazioni)
Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. 

### <a name="reason"></a>Motivo 
La porta del contenitore non è disponibile. Questo problema può verificarsi perché: 
* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il _Dockerfile_, il grafico Helm e il codice server che consente di aprire una porta.

### <a name="try"></a>Soluzione:
1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Verificare la configurazione della porta. I numeri di porta specificati devono essere **identici** in tutti gli asset seguenti:
    * **Dockerfile:** specificato dall'istruzione `EXPOSE`.
    * **[Grafico Helm](https://docs.helm.sh):** specificato dai valori `externalPort` e `internalPort` per un servizio (spesso in un file `values.yml`).
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Il file di configurazione non è stato trovato
Si esegue `azds up` e viene visualizzato l'errore seguente: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Motivo
È necessario eseguire `azds up` dalla directory radice del codice da eseguire ed è necessario inizializzare la cartella del codice per l'esecuzione con Azure Dev Spaces.

### <a name="try"></a>Soluzione:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. Se nella cartella del codice non è presente un file _azds.yaml_, eseguire `azds prep` per generare le risorse Docker, Kubernetes e Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Errore: 'The pipe program 'azds' exited unexpectedly with code 126' (Il programma della pipe 'azds' è stato chiuso in modo imprevisto con codice 126)
L'avvio del debugger di VS Code può talvolta generare questo errore.

### <a name="try"></a>Soluzione:
1. Chiudere e riaprire VS Code.
2. Premere di nuovo F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Errore di debug 'Non è stato possibile trovare l'estensione del debugger per tipo: coreclr'
L'esecuzione del debugger di VS Code genera l'errore: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Motivo
Nel computer di sviluppo non è installata l'estensione VS Code per C#. Il C# estensione include il supporto per .NET Core (CoreCLR) del debug.

### <a name="try"></a>Soluzione:
Installare l'[estensione di Visual Studio Code per C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Errore di debug "Configured debug type 'coreclr' is not supported" (Il tipo di debug configurato 'coreclr' non è supportato)
L'esecuzione del debugger di VS Code genera l'errore: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motivo
Nel computer di sviluppo non è installata l'estensione VS Code per Azure Dev Spaces.

### <a name="try"></a>Soluzione:
Installare l'[estensione VS Code per Azure](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Errore di debug "Invalid 'cwd' value '/src'. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato) o "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[percorso file binario di progetto]' non esiste)
L'esecuzione del debugger di VS Code genera l'errore `Invalid 'cwd' value '/src'. The system cannot find the file specified.` e/o `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Motivo
Per impostazione predefinita, l'estensione VS Code usa `src` come directory di lavoro per il progetto nel contenitore. Se è stato aggiornato il `Dockerfile` per specificare una directory di lavoro diversa, è possibile che venga visualizzato questo errore.

### <a name="try"></a>Soluzione:
Aggiornare il file `launch.json` nella sottodirectory `.vscode` della cartella del progetto. Modificare la direttiva `configurations->cwd` in modo da puntare alla stessa directory `WORKDIR` definita nel `Dockerfile` del progetto. Potrebbe inoltre essere necessario aggiornare anche la direttiva `configurations->program`.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Non è stato trovato il nome o il tipo spazio dei nomi "MyLibrary"

### <a name="reason"></a>Motivo 
Il contesto di compilazione è a livello di progetto/servizio per impostazione predefinita, quindi qualsiasi progetto libreria usato non può essere trovato.

### <a name="try"></a>Soluzione:
Quali azioni è necessario eseguire:
1. Modificare il file _azds.yaml_ per impostare il contesto di compilazione a livello di soluzione.
2. Modificare il _Dockerfile_ e i file _Dockerfile.develop_ in modo che facciano riferimento ai file di progetto ( _.csproj_) in modo corretto, nel rispetto del nuovo contesto di compilazione.
3. Collocare un file con estensione _.dockerignore_ accanto al file con estensione sln e modificare in base alle esigenze.

È possibile trovare un esempio in https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Errore di autorizzazione "Microsoft.DevSpaces/register/action"
Per gestire Azure Dev Spaces, è necessario l'accesso *Proprietario* o *Collaboratore* nella sottoscrizione di Azure. Questo errore può verificarsi se si prova a gestire Dev Spaces senza l'accesso *Proprietario* o *Collaboratore* alla sottoscrizione di Azure associata.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
La sottoscrizione di Azure selezionata non ha registrato lo spazio dei nomi `Microsoft.DevSpaces`.

### <a name="try"></a>Soluzione:
Un utente con accesso Proprietario o Collaboratore per la sottoscrizione di Azure può eseguire questo comando dell'interfaccia della riga di comando di Azure per registrare manualmente lo spazio dei nomi `Microsoft.DevSpaces`:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Dev Spaces raggiunge il timeout durante il passaggio *Waiting for container image build...* con i nodi virtuali del servizio Azure Kubernetes

### <a name="reason"></a>Motivo
Questo timeout si verifica quando si tenta di utilizzare gli spazi di sviluppo per eseguire un servizio che è configurato per l'esecuzione in un [nodo virtuale AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces attualmente non supporta i servizi di compilazione o debug nei nodi virtuali.

Se si esegue `azds up` con l'opzione `--verbose` o si abilita la registrazione dettagliata in Visual Studio, vengono visualizzati altri dettagli:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Il comando sopra riportato mostra che pod del servizio è stato assegnato a *virtual-nodo-aci-linux*, ovvero un nodo virtuale.

### <a name="try"></a>Soluzione:
Aggiornare il grafico Helm per il servizio per rimuovere i valori *nodeSelector* e/o *tolerations* che consentono di eseguire il servizio in un nodo virtuale. Questi valori sono in genere definiti nel file `values.yaml` del grafico.

È comunque possibile usare un cluster del servizio Azure Kubernetes con la funzionalità dei nodi virtuali abilitata, se il servizio di cui eseguire la compilazione o il debug tramite Dev Spaces viene eseguito in un nodo di macchina virtuale. Questa è la configurazione predefinita.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Error: could not find a ready tiller pod" ("Errore: impossibile trovare un pod Tiller pronto") quando si avvia Dev Spaces

### <a name="reason"></a>Motivo
Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

### <a name="try"></a>Soluzione:
Il riavvio dei nodi agente nel cluster risolve in genere questo problema.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Errore: rilascio azds -\<identifier\>-\<spacename\>-\<servicename\> non è riuscita: servizi\<servicename\>' esiste già "o" accesso negato per eseguire il Pull \<nomeservizio\>, repository non esiste o richiedano 'docker login' "

### <a name="reason"></a>`Reason`
Questi errori possono verificarsi se si combinano l'esecuzione di comandi Helm diretti (, ad esempio `helm install`, `helm upgrade`, o `helm delete`) con i comandi di sviluppo spazi (ad esempio `azds up` e `azds down`) all'interno dello stesso spazio di sviluppo. Si verificano perché gli spazi di sviluppo ha una propria istanza Tiller, che è in conflitto con il proprio istanza Tiller in esecuzione nello stesso spazio di sviluppo.

### <a name="try"></a>Soluzione:
È possibile usare sia i comandi di Helm e i comandi di spazi di sviluppo con lo stesso cluster AKS, ma ogni spazio dei nomi di abilitata spazi di sviluppo deve usare uno o l'altro.

Si supponga, ad esempio, che si usa un comando di Helm per eseguire l'intera applicazione in uno spazio di sviluppo padre. È possibile creare figlio spazi dev disattivare tale padre, usare spazi di sviluppo per l'esecuzione dei singoli servizi nel figlio dev spazi e testare i servizi insieme. Quando si è pronti per archiviare le modifiche, usare un comando di Helm per distribuire il codice aggiornato allo spazio dev padre. Non usare `azds up` per eseguire il servizio aggiornato nell'elemento padre dello spazio di sviluppo, poiché in conflitto con il servizio eseguito inizialmente l'uso di Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Il proxy Azure Dev Spaces può interferire con altri pod in esecuzione in uno spazio di sviluppo

### <a name="reason"></a>Motivo
Quando si abilita Dev Spaces in uno spazio dei nomi nel cluster servizio Azure Kubernetes, un contenitore aggiuntivo chiamato _mindaro-proxy_ viene installato in ciascuno dei pod in esecuzione all'interno di tale spazio dei nomi. Questo contenitore intercetta le chiamate ai servizi nel pod, che è parte integrante delle funzionalità di sviluppo in team di Dev Spaces. Può tuttavia interferire con alcuni servizi in esecuzione in tali pod. È possibile sapere interferisca con il POD in esecuzione di Cache di Azure per Redis, causando errori di connessione ed errori nella comunicazione primario/secondario.

### <a name="try"></a>Soluzione:
È possibile spostare i pod interessati in uno spazio dei nomi all'interno del cluster _senza_ Dev Spaces abilitato e continuare a eseguire il resto dell'applicazione in uno spazio dei nomi abilitato per Dev Spaces. Dev Spaces non installerà il contenitore _mindaro-proxy_ all'interno di spazi dei nomi in cui Dev Spaces non è attivato.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces non sembra utilizzare il Dockerfile esistente per compilare un contenitore

### <a name="reason"></a>Motivo
Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non usi il documento _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito ad Azure Dev Spaces quale Dockerfile usare. 

### <a name="try"></a>Soluzione:
Aprire il file _azds.yaml_ generato da Azure Dev Spaces nel progetto. Usare la direttiva *configurations->develop->build->dockerfile* per puntare al documento Dockerfile che si vuole usare:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Errore "watch interno non è riuscita: guarda ENOSPC" quando si collega il debug di un'applicazione Node. js

### <a name="reason"></a>Motivo

Il nodo che esegue il pod con l'applicazione Node. js si sta tentando di connettersi a un debugger ha superato il *fs.inotify.max_user_watches* valore. In alcuni casi [il valore predefinito *fs.inotify.max_user_watches* potrebbe essere troppo piccola per la gestione connessione di un debugger direttamente a un pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Prova
Soluzione alternativa temporanea per risolvere questo problema consiste nell'aumentare il valore di *fs.inotify.max_user_watches* in ogni nodo del cluster e riavviare il nodo rendere effettive le modifiche.

## <a name="new-pods-are-not-starting"></a>Nuovi POD non viene avviato

### <a name="reason"></a>Motivo

L'inizializzatore di Kubernetes non è possibile applicare il PodSpec per nuovi POD a causa di modifiche alle autorizzazioni RBAC per la *privilegi di amministratore cluster* ruolo del cluster. Il nuovo pod può anche avere un PodSpec non valido, ad esempio l'account del servizio associato nel pod non esiste più. Per visualizzare il numero di POD in una *in sospeso* dello stato a causa del problema di inizializzatore, usare il `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Questo problema può influire sulle POD nel *tutti gli spazi dei nomi* del cluster, inclusi gli spazi dei nomi in cui non è abilitato spazi di sviluppo di Azure.

### <a name="try"></a>Prova

[L'aggiornamento della riga di comando di spazi di sviluppo per la versione più recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e quindi eliminando il *azds InitializerConfiguration* dal controller di spazi di sviluppo di Azure:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Dopo aver rimosso il *azds InitializerConfiguration* dal controller di spazi di sviluppo di Azure, usare `kubectl delete` per rimuovere tutti i POD in una *sospeso* dello stato. Dopo tutto in sospeso POD sono state rimosse, ridistribuire POD.

Se nuovi POD sono comunque bloccati in un *in sospeso* dopo una ridistribuzione, utilizzare `kubectl delete` per rimuovere tutti i POD in una *in sospeso* dello stato. Dopo tutto in sospeso POD sono state rimosse, eliminare il controller dal cluster e reinstallarlo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Dopo la reinstallazione del controller, ridistribuire il POD.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorizzazioni RBAC non corrette per la chiamata di API e controller di spazi di sviluppo

### <a name="reason"></a>`Reason`
L'utente l'accesso al controller di spazi di sviluppo di Azure deve avere accesso in lettura admin *kubeconfig* nel cluster AKS. Ad esempio, questa autorizzazione è disponibile nel [incorporati ruolo di amministratore Cluster Azure Kubernetes Service](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). L'utente l'accesso al controller di spazi di sviluppo di Azure deve avere anche il *collaboratori* o *proprietario* dei ruoli RBAC per il controller.

### <a name="try"></a>Prova
Sono disponibili altri dettagli sull'aggiornamento delle autorizzazioni di un utente per un cluster AKS [qui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aggiornare il ruolo RBAC dell'utente per il controller:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare al gruppo di risorse che contiene il controller che è in genere quello utilizzato per il cluster AKS.
1. Abilitare la *Mostra tipi nascosti* casella di controllo.
1. Fare clic sul controller.
1. Aprire il *controllo di accesso (IAM)* riquadro.
1. Fare clic sui *assegnazioni di ruolo* scheda.
1. Fare clic su *Add* quindi *aggiungere un'assegnazione di ruolo*.
    * Per *ruolo* selezionando *collaboratore* oppure *proprietario*.
    * Per la *assegna accesso a* selezionate *utente, gruppo o entità servizio Azure AD*.
    * Per la *seleziona* cercare l'utente che si desidera concedere le autorizzazioni.
1. Fare clic su *Save*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Creare controller non riuscite perché la lunghezza del nome controller

### <a name="reason"></a>`Reason`
Nome del controller di spazi di sviluppo di Azure non può contenere più di 31 caratteri. Se il nome del controller supera 31 caratteri quando si abilita spazi di sviluppo in un cluster servizio contenitore di AZURE o creare un controller, si riceverà un errore, ad esempio:

*Non è stato possibile creare un controller di spazi di sviluppo per il cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Nome del Controller di spazi di sviluppo Azure 'a-controller-name-that-is-way-too-long-aks-east-us' non è valido. Violata di vincoli: I nomi dei Controller di spazi di sviluppo di Azure può solo essere al massimo il 31 caratteri*

### <a name="try"></a>Prova

Creare un controller con un nome alternativo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Abilitazione di spazi di sviluppo esito negativo quando il pool di nodi di Windows viene aggiunti a un cluster del servizio contenitore di AZURE

### <a name="reason"></a>`Reason`
Attualmente, gli spazi di sviluppo di Azure è destinato all'esecuzione in Linux il POD e solo i nodi. Quando si dispone di un cluster del servizio contenitore di AZURE con un pool di nodi di Windows, è necessario assicurarsi che Azure Dev spazi POD vengono programmate solo nei nodi Linux. Se un podcast di spazi di sviluppo di Azure è stato pianificato per l'esecuzione in un nodo di Windows, quel pod non verrà avviato e l'abilitazione di spazi di sviluppo avrà esito negativo.

### <a name="try"></a>Prova
[Aggiungere un aspetto](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) del cluster servizio contenitore di AZURE per garantire Linux POD non pianificati per l'esecuzione in un nodo di Windows.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Errore "non trovato alcun nodo Linux intatta nello stato pronto nel cluster. Deve essere presente almeno un nodo Linux intatta nello stato pronto per distribuire i POD nello spazio dei nomi 'azds'."

### <a name="reason"></a>`Reason`

Spazi di sviluppo Azure Impossibile creare un controller nel cluster AKS perché non è stato trovato un nodo intatta in un *pronti* dello stato per pianificare i POD in. Spazi di sviluppo Azure richiede almeno un nodo Linux in un *pronti* stato che consente di pianificare i POD senza specificare tolerations.

### <a name="try"></a>Prova
[Aggiornare la configurazione dell'aspetto](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) nel cluster AKS per assicurarsi che almeno un Linux consente a nodo per la pianificazione di POD senza specificare tolerations. Inoltre, verificare che almeno un nodo Linux che consente la programmazione POD senza specificare tolerations sia nel *pronti* dello stato. Se il nodo viene richiesto un tempo per raggiungere la *pronti* lo stato, è possibile provare a riavviare il nodo.
