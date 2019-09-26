---
title: Risoluzione dei problemi
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 87aa96614b6aec4843723233a77d0a1dc1b66453
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300358"
---
# <a name="troubleshooting-guide"></a>Guida alla risoluzione dei problemi

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

Se si verifica un problema durante l'uso di Azure Dev Spaces, creare un [problema nel repository GitHub di Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Prima di iniziare

Per risolvere i problemi in modo più efficace, può essere utile creare log maggiormente dettagliati per la revisione.

Per l'estensione di Visual Studio impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati vengono scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i log più dettagliati in `%TEMP%\Azure Dev Spaces`. In un computer Mac la directory TEMP è reperibile eseguendo `echo $TMPDIR` da una finestra del terminale. In un computer Linux la directory TEMP è generalmente `/tmp`.

Azure Dev Spaces funziona anche quando si esegue il debug di una singola istanza o di un pod. Il `azds.yaml` file contiene un'impostazione, *replicaCount*, che indica il numero di Pod eseguiti da Kubernetes per il servizio. Se si modifica *replicaCount* per configurare l'applicazione in modo che esegua più POD per un determinato servizio, il debugger si connette al primo pod, quando è elencato in ordine alfabetico. Il debugger si collega a un pod diverso quando il pod originale viene riciclato, determinando un comportamento imprevisto.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemi comuni durante l'abilitazione di Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Errore "non è stato possibile creare Azure Dev Spaces controller"

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, per correggerlo, eliminare e ricreare il controller.

È anche possibile provare a eliminare il controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Usare l'interfaccia della riga di comando Azure Dev Spaces per eliminare un controller. Non è possibile eliminare un controller da Visual Studio. Non è inoltre possibile installare l'interfaccia della riga di comando Azure Dev Spaces nel Azure Cloud Shell quindi non è possibile eliminare un controller dal Azure Cloud Shell.

Se l'interfaccia della riga di comando di Azure Dev Spaces non è installata, è possibile installarla usando il comando seguente e quindi eliminare il controller:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. Per esempi, vedere le guide introduttive per [lo sviluppo di team o lo sviluppo](quickstart-team-development.md) [con .NET Core](quickstart-netcore-visualstudio.md) .

### <a name="controller-create-failing-because-of-controller-name-length"></a>Creazione del controller non riuscita a causa della lunghezza del nome del controller

Il nome di un controller di Azure Dev Spaces non può contenere più di 31 caratteri. Se il nome del controller supera i 31 caratteri quando si abilitano gli spazi di sviluppo in un cluster AKS o si crea un controller, si riceverà un errore. Esempio:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Per risolvere questo problema, creare un controller con un nome alternativo. Esempio:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Errore di abilitazione degli spazi di sviluppo quando i pool di nodi di Windows vengono aggiunti a un cluster AKS

Attualmente, Azure Dev Spaces è progettato per l'esecuzione solo su Pod e nodi Linux. Quando si dispone di un cluster AKS con un pool di nodi di Windows, è necessario assicurarsi che i pod Azure Dev Spaces siano pianificati solo nei nodi Linux. Se è pianificata l'esecuzione di un pod Azure Dev Spaces in un nodo Windows, il Pod non verrà avviato e l'abilitazione di spazi di sviluppo avrà esito negativo.

Per risolvere questo problema, [aggiungere una macchia](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) al cluster AKS per assicurarsi che i pod Linux non siano pianificati per l'esecuzione in un nodo di Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Errore "non sono stati trovati nodi Linux non corrotti nello stato pronto nel cluster. Per distribuire i pod nello spazio dei nomi ' azds ' è necessario che sia presente almeno un nodo Linux non corrotto nello stato pronto.

Azure Dev Spaces non è stato possibile creare un controller nel cluster AKS perché non è stato possibile trovare un nodo non infetto in uno stato *pronto* per la pianificazione dei pod. Azure Dev Spaces richiede almeno un nodo Linux in uno stato *pronto* che consente di pianificare i pod senza specificare le tolleranze.

Per risolvere questo problema, [aggiornare la configurazione di Taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) nel cluster AKS per assicurarsi che almeno un nodo Linux consenta la pianificazione di Pod senza specificare le tolleranze. Inoltre, assicurarsi che almeno un nodo Linux che consente la pianificazione di Pod senza specificare le tolleranze sia nello stato *pronto* . Se il nodo impiega molto tempo per raggiungere lo stato *pronto* , è possibile provare a riavviare il nodo.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Errore "Azure Dev Spaces l'interfaccia della riga di comando non è installata correttamente" durante l'esecuzione`az aks use-dev-spaces`

È stato modificato il percorso di installazione di un aggiornamento dell'interfaccia della riga di comando Azure Dev Spaces. Se si usa una versione dell'interfaccia della riga di comando di Azure precedente a 2.0.63, è possibile che venga visualizzato questo errore. Per visualizzare la versione dell'interfaccia della riga di comando `az --version`di Azure, usare.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Nonostante il messaggio di errore durante `az aks use-dev-spaces` l'esecuzione con una versione dell'interfaccia della riga di comando di Azure prima di 2.0.63, l'installazione ha esito positivo. È possibile continuare a utilizzare `azds` senza problemi.

Per risolvere questo problema, aggiornare l'installazione dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) a 2.0.63 o versione successiva. Questo aggiornamento risolverà il messaggio di errore ricevuto durante l' `az aks use-dev-spaces`esecuzione di. In alternativa, è possibile continuare a usare la versione corrente dell'interfaccia della riga di comando di Azure e l'interfaccia della riga di comando Azure Dev Spaces.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemi comuni durante la preparazione del progetto per Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avviso "Impossibile generare Dockerfile a causa di un linguaggio non supportato"
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. Quando si esegue `azds prep` in una directory con il codice scritto in uno di questi linguaggi, Azure Dev Spaces crea automaticamente un Dockerfile appropriato.

È comunque possibile usare Azure Dev Spaces con il codice scritto in altri linguaggi, ma è necessario creare manualmente il Dockerfile prima di `azds up` eseguire per la prima volta.

Se l'applicazione è scritta in una lingua che Azure Dev Spaces non supporta in modo nativo, è necessario fornire un Dockerfile appropriato per compilare un'immagine del contenitore che esegue il codice. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e [informazioni di riferimento su Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un documento Dockerfile in base alle proprie esigenze.

Dopo aver creato un Dockerfile appropriato, eseguire `azds up` per eseguire l'applicazione in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemi comuni durante l'avvio o l'arresto di servizi con Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Errore "Impossibile trovare il file di configurazione:"

Quando è `azds up`in esecuzione, è possibile che venga visualizzato questo errore. Sia `azds up` che`azds prep` devono essere eseguiti dalla directory radice del progetto che si vuole eseguire nello spazio di sviluppo.

Per risolvere il problema:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. Se non si dispone di un file _azds. YAML_ nella cartella del codice, `azds prep` eseguire per generare asset Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout in attesa della compilazione dell'immagine del contenitore... Esegui istruzione con i nodi virtuali AKS

Questo timeout si verifica quando si tenta di usare gli spazi di sviluppo per eseguire un servizio configurato per l'esecuzione su un [nodo virtuale AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Gli spazi di sviluppo attualmente non supportano la compilazione o il debug di servizi nei nodi virtuali.

Se si esegue `azds up` con l'opzione `--verbose` o si abilita la registrazione dettagliata in Visual Studio, vengono visualizzati altri dettagli:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Il comando precedente mostra che il pod del servizio è stato assegnato a *Virtual-node-ACI-Linux*, che è un nodo virtuale.

Per risolvere questo problema, aggiornare il grafico Helm per il servizio in modo da rimuovere tutti i valori *nodeSelector* o *Tollers* che consentono di eseguire il servizio in un nodo virtuale. Questi valori sono in genere definiti nel file `values.yaml` del grafico.

È comunque possibile usare un cluster AKS con la funzionalità nodi virtuali abilitata, se il servizio di cui si vuole eseguire la compilazione o il debug tramite spazi di sviluppo viene eseguito in un nodo della macchina virtuale. L'esecuzione di un servizio con spazi di sviluppo in un nodo della macchina virtuale è la configurazione predefinita.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Errore "non è stato possibile trovare un pod pronto per il Tiller" quando si avviano gli spazi di sviluppo

Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

Per risolvere questo problema, riavviare i nodi dell'agente nel cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Errore "versione azds-\<identificatore\>-spacename\>ServiceName\> non riuscita: Services'\<ServiceName-\<\<\>' già esistente ' o ' accesso negato per \<ServiceName\>, il repository non esiste o potrebbe richiedere ' Docker login '

Questi errori possono verificarsi se si combinano i comandi Direct Helm (ad `helm install`esempio `helm upgrade`, o `helm delete` `azds up` ) con i comandi di spazi di sviluppo, `azds down`ad esempio e, all'interno dello stesso spazio di sviluppo. Si verificano perché gli spazi di sviluppo hanno una propria istanza del timone, che è in conflitto con l'istanza di un proprio utente in esecuzione nello stesso spazio di sviluppo.

È consigliabile usare i comandi Helm e dev Spaces sullo stesso cluster AKS, ma ogni spazio dei nomi abilitato per gli spazi di sviluppo deve usare uno o l'altro.

Si supponga, ad esempio, di usare un comando Helm per eseguire l'intera applicazione in uno spazio di sviluppo padre. È possibile creare spazi di sviluppo figlio da tale elemento padre, usare gli spazi di sviluppo per eseguire singoli servizi all'interno degli spazi di sviluppo figlio e testare i servizi insieme. Quando si è pronti per archiviare le modifiche, usare un comando Helm per distribuire il codice aggiornato nello spazio di sviluppo padre. Non usare `azds up` per eseguire il servizio aggiornato nello spazio di sviluppo padre, perché sarà in conflitto con il servizio eseguito inizialmente con Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile esistente non usato per compilare un contenitore

Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non usi il documento _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito ad Azure Dev Spaces quale Dockerfile usare. 

Per risolvere questo problema, aprire il file _azds. YAML_ che Azure Dev Spaces generato nel progetto. Configurazioni di aggiornamento *: develop: Build: dockerfile* in modo che punti al dockerfile che si vuole usare. Esempio:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Errore "non autorizzato: autenticazione obbligatoria" quando si tenta di usare un'immagine Docker da un registro privato

Si sta usando un'immagine Docker da un registro privato che richiede l'autenticazione.

Per risolvere questo problema, è possibile consentire agli spazi di sviluppo di eseguire l'autenticazione e il pull delle immagini da questo registro privato usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Per usare imagePullSecrets, [creare un segreto Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) nello spazio dei nomi in cui si sta usando l'immagine. Fornire quindi il segreto come imagePullSecret in `azds.yaml`.

Di seguito è riportato un esempio di un oggetto `azds.yaml`che specifica imagePullSecrets in.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> L'impostazione di `azds.yaml` imagePullSecrets in eseguirà l'override `values.yaml`di imagePullSecrets specificato in.

### <a name="error-service-cannot-be-started"></a>Errore "Impossibile avviare il servizio".

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. Per ottenere altre informazioni di diagnostica, abilitare la registrazione più dettagliata all'avvio del servizio.

Dalla riga di comando, usare per `--verbose` abilitare la registrazione più dettagliata. È anche possibile specificare un formato di output `--output`usando. Esempio:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Aprire **Strumenti > Opzioni** e in **Projects and Solutions** (Progetti e soluzioni), scegliere **Build and Run** (Compila ed esegui).
2. Modificare le impostazioni per **MSBuild project build output verbosity** (Livello di dettaglio output di compilazione progetto MSBuild) in **Detailed** (Dettagliato) oppure in **Diagnostic** (Diagnostica).

    ![Screenshot della finestra di dialogo Strumenti > Opzioni](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Riesecuzione di un servizio dopo la ripetizione della creazione del controller

Viene visualizzato l'errore *Service cannot be started* quando si prova a rieseguire un servizio dopo la rimozione e quindi la ricreazione del controller Azure Dev Spaces associato al cluster. In questo caso, l'output dettagliato contiene il testo seguente:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Questo errore si verifica perché la rimozione del controller dev Spaces non rimuove i servizi installati in precedenza dal controller. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Errore "Impossibile avviare il servizio". Quando si usa Dockerfile in più fasi

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

Questo errore si verifica perché i nodi AKS eseguono una versione precedente di Docker che non supporta le compilazioni in più fasi. Per evitare compilazioni in più fasi, riscrivere il documento Dockerfile.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemi comuni con Visual Studio e Visual Studio Code con Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Errore "mancano gli strumenti e le configurazioni richiesti"

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

Provare a avviare VS Code da un prompt dei comandi in cui la variabile di ambiente PATH è impostata correttamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Errore "Required tools to build and debug 'projectname' are out of date" (Gli strumenti necessari per la compilazione e il debug di 'nome progetto' non sono aggiornati)

Questo errore viene visualizzato in Visual Studio Code se sono presenti una versione dell'estensione VS Code per Azure Dev Spaces più recente e una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

Provare a scaricare e installare la versione più recente dell'interfaccia della riga di comando di Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Errore: "Impossibile trovare l'estensione del debugger per il tipo: CoreCLR"

Questo errore può essere visualizzato durante l'esecuzione del debugger Visual Studio Code. È possibile che non si disponga dell'estensione C# vs code per installato nel computer di sviluppo. L' C# estensione include il supporto del debug per .NET Core (CoreCLR).

Per risolvere questo problema, installare l' [estensione vs code per C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Errore "il tipo di debug ' CoreCLR ' configurato non è supportato"

Questo errore può essere visualizzato durante l'esecuzione del debugger Visual Studio Code. È possibile che non si disponga dell'estensione VS Code per Azure Dev Spaces installato nel computer di sviluppo.

Per risolvere questo problema, installare l' [estensione vs code per Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Errore "valore ' CWD ' non valido '/src '. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato) o "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[percorso file binario di progetto]' non esiste)

Questo errore può essere visualizzato durante l'esecuzione del debugger Visual Studio Code. Per impostazione predefinita, l'estensione VS Code usa `src` come directory di lavoro per il progetto nel contenitore. Se è stato aggiornato il `Dockerfile` per specificare una directory di lavoro diversa, è possibile che venga visualizzato questo errore.

Per risolvere questo problema, aggiornare il `launch.json` file `.vscode` nella sottodirectory della cartella del progetto. Modificare la direttiva `configurations->cwd` in modo da puntare alla stessa directory `WORKDIR` definita nel `Dockerfile` del progetto. Potrebbe inoltre essere necessario aggiornare anche la direttiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Errore "il programma pipe ' azds ' è stato terminato in modo imprevisto con il codice 126".

Questo errore può essere visualizzato durante l'esecuzione del debugger Visual Studio Code.

Per risolvere il problema, chiudere e riaprire Visual Studio Code. Riavviare il debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Errore "l'espressione di controllo interno non è riuscita: guardare ENOSPC" quando si connette il debug a un'applicazione Node. js

Questo errore si verifica quando il nodo che esegue il pod con l'applicazione Node. js a cui si sta tentando di connettersi con un debugger ha superato il valore di *_user_watches FS. inotify. max* . In alcuni casi, [il valore predefinito di *FS. inotify. max _user_watches* può essere troppo piccolo per gestire il connessione di un debugger direttamente a un pod](https://github.com/Azure/AKS/issues/772).

Una soluzione alternativa temporanea per questo problema consiste nell'aumentare il valore di *FS. inotify. max _user_watches* in ogni nodo del cluster e riavviare il nodo per rendere effettive le modifiche.

## <a name="other-common-issues"></a>Altri problemi comuni

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>L'errore "azds" non è riconosciuto come comando interno o esterno, programma eseguibile o file batch

Questo errore può verificarsi se `azds.exe` non è installato o configurato correttamente.

Per risolvere il problema:

1. Controllare il percorso% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI `azds.exe`per. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se `azds.exe` non è installato, eseguire il comando seguente:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Errore di autorizzazione "Microsoft. DevSpaces/Register/Action"

Per gestire Azure Dev Spaces, è necessario l'accesso *Proprietario* o *Collaboratore* nella sottoscrizione di Azure. Se si sta tentando di gestire gli spazi di sviluppo e non si dispone dell'accesso come *proprietario* o *collaboratore* alla sottoscrizione di Azure associata, potrebbe essere visualizzato un errore di autorizzazione. Esempio:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Per risolvere questo problema, usando un account con accesso *proprietario* o *collaboratore* alla sottoscrizione di Azure, registrare manualmente `Microsoft.DevSpaces` lo spazio dei nomi:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>I nuovi Pod non vengono avviati

L'inizializzatore di Kubernetes non può applicare il PodSpec per i nuovi pod a causa delle modifiche delle autorizzazioni RBAC al ruolo di *amministratore del cluster* nel cluster. Il nuovo pod potrebbe avere anche un PodSpec non valido, ad esempio l'account del servizio associato al Pod non esiste più. Per visualizzare i pod in uno stato in *sospeso* a causa del problema dell'inizializzatore, usare il `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Questo problema può avere un effetto sui pod in *tutti gli spazi dei nomi* del cluster, inclusi gli spazi dei nomi in cui Azure Dev Spaces non è abilitato.

Per risolvere questo problema, [aggiornare l'interfaccia della riga di comando di dev Spaces alla versione più recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e quindi eliminare il *InitializerConfiguration di azds* dal controller Azure Dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Dopo aver rimosso il *InitializerConfiguration azds* dal controller Azure Dev Spaces, usare `kubectl delete` per rimuovere tutti i pod in uno stato in *sospeso* . Dopo la rimozione di tutti i pod in sospeso, ridistribuire i pod.

Se i nuovi Pod rimangono bloccati in uno stato in *sospeso* dopo una ridistribuzione, usare `kubectl delete` per rimuovere tutti i pod in uno stato in *sospeso* . Dopo la rimozione di tutti i pod in sospeso, eliminare il controller dal cluster e reinstallarlo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Dopo la reinstallazione del controller, ridistribuire i pod.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorizzazioni RBAC non corrette per la chiamata di API e controller degli spazi di sviluppo

L'utente che accede al controller di Azure Dev Spaces deve avere accesso per leggere il *kubeconfig* di amministrazione nel cluster AKS. Ad esempio, questa autorizzazione è disponibile nel [ruolo di amministratore del cluster di servizi Kubernetes di Azure predefinito](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). L'utente che accede al controller di Azure Dev Spaces deve avere anche il ruolo Collaboratore o controllo degli accessi in base al ruolo del *proprietario* per il controller. Altre informazioni sull'aggiornamento delle autorizzazioni di un utente per un cluster AKS sono disponibili [qui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Per aggiornare il ruolo RBAC dell'utente per il controller:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare al gruppo di risorse contenente il controller, che in genere corrisponde al cluster AKS.
1. Abilitare la casella di controllo *Mostra tipi nascosti* .
1. Fare clic sul controller.
1. Aprire il riquadro *controllo di accesso (IAM)* .
1. Fare clic sulla scheda assegnazioni di *ruolo* .
1. Fare clic su *Aggiungi* e quindi su *Aggiungi assegnazione ruolo*.
    * Per *ruolo*selezionare *collaboratore* o *proprietario*.
    * In *Assegna accesso a* selezionare *Utente, gruppo o entità servizio di Azure AD*.
    * Per *Select*, cercare l'utente a cui si vogliono concedere le autorizzazioni.
1. Fare clic su *Salva*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

È possibile configurare un endpoint URL pubblico per il servizio specificando l'opzione `--public` per il comando `azds prep` o selezionando la casella di controllo `Publicly Accessible` in Visual Studio. Il nome DNS pubblico viene registrato automaticamente quando si esegue il servizio in Dev Spaces. Se questo nome DNS non viene registrato, viene visualizzato un errore simile a *Non è possibile visualizzare la pagina* o *Non è possibile raggiungere il sito* nel Web browser quando ci si connette all'URL pubblico.

Per risolvere il problema:

* Verificare lo stato di tutti gli URL associati ai servizi dev Spaces:

  ```console
  azds list-uris
  ```

* Se un URL è nello stato in *sospeso* , gli spazi di sviluppo restano in attesa del completamento della registrazione DNS. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.
* Se un URL rimane nello stato *In sospeso* per più di 5 minuti, ciò potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico o con il pod del controller in ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. Usare i comandi seguenti per eliminare questi pod e consentire ad AKS di ricrearli automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Errore "errore di connessione upstream o disconnessione/reimpostazione prima delle intestazioni"

Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. Questo errore indica che la porta del contenitore non è disponibile. Questo può essere per i motivi seguenti:

* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il _Dockerfile_, il grafico Helm e il codice server che consente di aprire una porta.

Per risolvere il problema:

1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Verificare la configurazione della porta. I numeri di porta specificati devono essere **identici** in tutti gli asset seguenti:
    * **Dockerfile:** specificato dall'istruzione `EXPOSE`.
    * **[Grafico Helm](https://docs.helm.sh):** specificato dai valori `externalPort` e `internalPort` per un servizio (spesso in un file `values.yml`).
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Il tipo o il nome dello spazio dei nomi "mylibrary" non è stato trovato

Non è possibile trovare un progetto di libreria che si sta usando. Con gli spazi di sviluppo, per impostazione predefinita il contesto di compilazione è a livello di progetto/servizio.  

Per risolvere il problema:

1. Modificare il `azds.yaml` file per impostare il contesto di compilazione sul livello della soluzione.
2. Modificare i `Dockerfile` file `Dockerfile.develop` e per fare riferimento ai file di progetto, ad `.csproj`esempio correttamente rispetto al nuovo contesto di compilazione.
3. Aggiungere un `.dockerignore` oggetto nella stessa directory del `.sln` file.
4. `.dockerignore` Aggiornare con altre voci in base alle esigenze.

Un esempio è disponibile [qui](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Scalabilità automatica del Pod orizzontale non funzionante in uno spazio di sviluppo

Quando si esegue un servizio in uno spazio di sviluppo, il pod del servizio viene [inserito con contenitori aggiuntivi per la strumentazione](how-dev-spaces-works.md#prepare-your-aks-cluster) e tutti i contenitori in un pod devono disporre di limiti e richieste di risorse impostati per la scalabilità automatica dei Pod orizzontali.

Per risolvere questo problema, applicare una richiesta di risorse e limitare i contenitori di spazi di sviluppo inseriti. È possibile applicare le richieste e i limiti delle risorse per il contenitore inserito (devspaces-proxy) aggiungendo `azds.io/proxy-resources` l'annotazione alla specifica pod. Il valore deve essere impostato su un oggetto JSON che rappresenta la sezione delle risorse della specifica del contenitore per il proxy.

Di seguito è riportato un esempio di un'annotazione proxy-Resources da applicare alla specifica pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```