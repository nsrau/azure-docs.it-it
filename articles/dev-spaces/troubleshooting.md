---
title: Risoluzione dei problemi | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: article
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 9973635593f7a8143ac1f3980b6e09caba44710b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413609"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

## <a name="enabling-detailed-logging"></a>Abilitazione della registrazione dettagliata

Per risolvere i problemi in modo più efficace, può essere utile per creare log maggiormente dettagliati per la revisione.

Per l'estensione di Visual Studio impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati verranno scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i registri più dettagliati in `%TEMP%\Azure Dev Spaces`. In un computer Mac la directory TEMP è reperibile eseguendo `echo $TMPDIR` da una finestra del terminale. In un computer Linux la directory TEMP è generalmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debug dei servizi con più istanze

A questo punto, Azure Dev Spaces funziona meglio durante il debug di una singola istanza (pod). Il file azds.yaml contiene un'impostazione, replicaCount, che indica il numero di pod che verrà eseguito per il servizio. Se si modifica replicaCount per configurare l'app in modo che esegua più pod per un determinato servizio, il debugger si collegherà al primo pod (se elencato in ordine alfabetico). Se il pod viene riciclato per un qualsiasi motivo, il debugger si collegherà a un pod diverso, determinando un comportamento imprevisto.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Errore "Failed to create Azure Dev Spaces controller" (Non è stato possibile creare il controller di Azure Dev Spaces)

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, è possibile correggerlo eliminando e ricreando il controller.

### <a name="try"></a>Soluzione:

Per eliminare il controller, usare l'interfaccia della riga di comando di Azure Dev Spaces. Non è possibile eseguire questa operazione in Visual Studio o in Cloud Shell. Per installare l'interfaccia della riga di comando di Azure Dev Spaces, installare innanzitutto l'interfaccia della riga di comando di Azure e quindi eseguire questo comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Eseguire quindi questo comando per eliminare il controller:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. Seguire le istruzioni nelle esercitazioni come se si iniziasse per la prima volta.


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
È possibile che questo errore venga visualizzato quando si tenta di usare un Dockerfile a più fasi. L'output dettagliato sarà simile al seguente:

```cmd
$ azds up
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

Questo perché i nodi servizio Azure Kubernetes eseguono una versione precedente di Docker che non supporta compilazioni in più fasi. Sarà necessario riscrivere il Dockerfile per evitare compilazioni in più fasi.

### <a name="re-running-a-service-after-controller-re-creation"></a>Riesecuzione di un servizio dopo la ripetizione della creazione del controller
È possibile che questo errore venga visualizzato quando si tenta di rieseguire un servizio dopo la rimozione e quindi la ricreazione del controller Azure Dev Spaces associato al cluster. L'output dettagliato sarà simile al seguente:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Questo avviene perché la rimozione del controller Dev Spaces non comporta la rimozione dei servizi installati in precedenza da tale controller. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

Quando la risoluzione dei nomi DNS ha esito negativo potrebbe essere visualizzato un errore "Impossibile visualizzare la pagina" o "Impossibile raggiungere il sito" nel browser Web quando si prova a connettersi all'URL pubblico associato al servizio Dev Spaces.

### <a name="try"></a>Soluzione:

È possibile usare il comando seguente per elencare tutti gli URL associati ai servizi Dev Spaces:

```cmd
azds list-uris
```

Se un URL mostra lo stato *In sospeso* significa che Dev Spaces è ancora in attesa di completamento della registrazione DNS. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.

Se un URL rimane nello stato *In sospeso* per più di 5 minuti, potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico e/o con il pod del controller di ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. È possibile usare i comandi seguenti per eliminare i POD. Questi verranno ricreati automaticamente.

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

Questo errore viene visualizzato in Visual Studio Code se si dispone di una versione dell'estensione VS Code per Azure Dev Spaces più recente e di una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

### <a name="try"></a>Prova

Scaricare e installare l'ultima versione dell'interfaccia della riga di comando di Azure Dev Spaces:

* [Windows](http://aka.ms/get-azds-windows)
* [Mac](http://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Errore 'azds' non riconosciuto come comando interno o esterno, programma eseguibile o file batch
 
È possibile visualizzare questo errore se azds.exe non è installato o configurato correttamente.

### <a name="try"></a>Soluzione:

1. Controllare il percorso %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview) per individuare azds.exe. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se azds.exe non è installato, eseguire il comando seguente:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avviso 'Dockerfile could not be generated due to unsupported language' (Dockerfile non generato a causa di un linguaggio non supportato)
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. Quando si esegue *azds prep* in una directory contenente codice scritto in uno di questi linguaggi, Azure Dev Spaces creerà automaticamente un Dockerfile appropriato per l'utente.

È comunque possibile usare Azure Dev Spaces con il codice scritto in altri linguaggi, ma sarà necessario creare il Dockerfile prima di eseguire *azds up* per la prima volta.

### <a name="try"></a>Soluzione:
Se l'applicazione è scritta in un linguaggio non supportato da Azure Dev Spaces in modo nativo, è necessario fornire un Dockerfile appropriato per creare un'immagine del contenitore che esegua il codice. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) nonché un [riferimento a Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un Dockerfile in base alle proprie esigenze.

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
Nel computer di sviluppo non è installata l'estensione VS Code per C#. L'estensione C# include il supporto per il debug di .Net Core (CoreCLR).

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
Il contesto di compilazione è a livello di progetto/servizio per impostazione predefinita pertanto, qualsiasi progetto libreria usato non verrà trovato.

### <a name="try"></a>Soluzione:
Quali azioni è necessario eseguire:
1. Modificare il file _azds.yaml_ per impostare il contesto di compilazione a livello di soluzione.
2. Modificare il _Dockerfile_ e i file _Dockerfile.develop_ in modo che facciano riferimento ai file di progetto (_.csproj_) in modo corretto, nel rispetto del nuovo contesto di compilazione.
3. Collocare un file con estensione _.dockerignore_ accanto al file con estensione sln e modificare in base alle esigenze.

È possibile trovare un esempio in https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Errore di autorizzazione "Microsoft.DevSpaces/register/action"
Si potrebbe verificare l'errore seguente quando si gestisce un Azure Dev Space e si lavora in una sottoscrizione di Azure per la quale non si dispone dell'accesso di tipo Proprietario o Collaboratore.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
La sottoscrizione di Azure selezionata non ha registrato lo spazio dei nomi `Microsoft.DevSpaces`.

### <a name="try"></a>Soluzione:
Un utente con accesso Proprietario o Collaboratore per la sottoscrizione di Azure può eseguire questo comando dell'interfaccia della riga di comando di Azure per registrare manualmente lo spazio dei nomi `Microsoft.DevSpaces`:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Error: could not find a ready tiller pod" ("Errore: impossibile trovare un pod Tiller pronto") quando si avvia Dev Spaces

### <a name="reason"></a>Motivo
Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

### <a name="try"></a>Soluzione:
Il riavvio dei nodi agente nel cluster risolve in genere questo problema.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Il proxy Azure Dev Spaces può interferire con altri pod in esecuzione in uno spazio di sviluppo

### <a name="reason"></a>Motivo
Quando si abilita Dev Spaces in uno spazio dei nomi nel cluster servizio Azure Kubernetes, un contenitore aggiuntivo chiamato _mindaro-proxy_ viene installato in ciascuno dei pod in esecuzione all'interno di tale spazio dei nomi. Questo contenitore intercetta le chiamate ai servizi nel pod, che è parte integrante delle funzionalità di sviluppo in team di Dev Spaces.

Sfortunatamente può interferire con determinati servizi in esecuzione in tali pod. In particolare interferisce con i pod che eseguono Cache Redis di Azure, causando errori di connessione ed errori nella comunicazione primario/secondario.

### <a name="try"></a>Soluzione:
È possibile spostare i pod interessati in uno spazio dei nomi all'interno del cluster che _non_ ha Dev Spaces abilitato, pur continuando a eseguire il resto dell'applicazione all'interno di uno spazio dei nomi con Dev Spaces attivato. Dev Spaces non installerà il contenitore _mindaro-proxy_ all'interno di spazi dei nomi in cui Dev Spaces non è attivato.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces non sembra utilizzare il Dockerfile esistente per compilare un contenitore 

### <a name="reason"></a>Motivo
Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non utilizzi il _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito a Azure Dev Spaces dove si trova il file. 

### <a name="try"></a>Soluzione:
Aprire il file _azds.yaml_ generato da Azure Dev Spaces nel progetto. Usare la direttiva `configurations->develop->build->dockerfile` per puntare al Dockerfile che si desidera utilizzare:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
