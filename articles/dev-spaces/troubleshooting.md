---
title: Risoluzione dei problemi
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Informazioni su come risolvere i problemi comuni relativi all'abilitazione e all'uso di Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 9fcf14bf42fc843a126fea269038087ee7fb0c6c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382055"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Risoluzione dei problemi relativi a Azure Dev SpacesAzure Dev Spaces troubleshooting

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

In caso di problemi durante [l'utilizzo](https://github.com/Azure/dev-spaces/issues)di Azure Dev Spaces, creare un problema nel repository GitHub di Azure Dev Spaces.

## <a name="before-you-begin"></a>Prima di iniziare

Per risolvere i problemi in modo più efficace, può essere utile creare log maggiormente dettagliati per la revisione.

Per l'estensione di Visual Studio impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati vengono scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i log più dettagliati in `%TEMP%\Azure Dev Spaces`. Su un Mac, la directory *TEMP* può essere trovata eseguendouna `echo $TMPDIR` da una finestra del terminale. In un computer *TEMP* Linux, la `/tmp`directory TEMP è in genere . Verificare inoltre che la registrazione sia abilitata nel file di [configurazione dell'interfaccia della riga](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)di comando di Azure.

Azure Dev Spaces funziona anche meglio quando si esegue il debug di una singola istanza o pod. Il `azds.yaml` file contiene un'impostazione, *replicaCount*, che indica il numero di pod eseguiti da Kubernetes per il servizio. Se si modifica *replicaCount* per configurare l'applicazione per l'esecuzione di più pod per un determinato servizio, il debugger si connette al primo pod, quando è elencato in ordine alfabetico. Il debugger si collega a un pod diverso quando il pod originale viene riciclato, determinando un comportamento imprevisto.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemi comuni durante l'abilitazione degli spazi di sviluppo di AzureCommon issues when enabling Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Errore "Impossibile creare il controller di Azure Dev Spaces"

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, per correggerlo, eliminare e ricreare il controller.

Puoi anche provare a eliminare il controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Usare l'interfaccia della riga di comando Di Azure Dev Spaces per eliminare un controller. Non è possibile eliminare un controller da Visual Studio.It's not possible to delete a controller from Visual Studio. Non è inoltre possibile installare la riga di comando di Azure Dev Spaces in Azure Cloud Shell, pertanto non è possibile eliminare un controller da Azure Cloud Shell.

Se l'interfaccia della riga di comando di Azure Dev Spaces non è installata, è possibile installarla utilizzando il comando seguente e quindi eliminare il controller:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. Per alcuni esempi, vedere le guide introduttive Sviluppo o sviluppo in team con .NET Core.See the [Team development](quickstart-team-development.md) or [Develop with .NET Core](quickstart-netcore-visualstudio.md) quickstarts for examples.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Creazione del controller non riuscita a causa della lunghezza del nome del controller

Il nome di un controller Azure Dev Spaces non può essere più lungo di 31 caratteri. Se il nome del controller supera i 31 caratteri quando abiliti spazi dev in un cluster AKS o crei un controller, riceverai un errore. Ad esempio:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Per risolvere questo problema, creare un controller con un nome alternativo. Ad esempio:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Abilitazione di Dev Spaces con esito negativo quando i pool di nodi di Windows vengono aggiunti a un cluster AKSEnabling Dev Spaces failing when Windows node pools are added to an AKS cluster

Attualmente, Azure Dev Spaces è destinato solo all'esecuzione su pod e nodi Linux.Currently, Azure Dev Spaces is intended to run on Linux pods and nodess only. Quando si dispone di un cluster AKS con un pool di nodi di Windows, è necessario assicurarsi che i pod di Azure Dev Spaces siano pianificati solo nei nodi Linux.When you have an AKS cluster with a Windows node pool, you must ensure that Azure Dev Spaces pods are only scheduled on Linux nodes. Se è pianificato un pod Spazi di sviluppo di Azure per l'esecuzione in un nodo Windows, tale pod non verrà avviato e l'abilitazione di Dev Spaces avrà esito negativo.

Per risolvere questo problema, [aggiungere una macchia](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) al cluster AKS per garantire che i pod Linux non sono pianificati per l'esecuzione su un nodo di Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Errore "Trovato nessun nodo Linux non intainted nello stato Pronto sul cluster. È necessario che sia presente almeno un nodo Linux non intainted nello stato Ready per distribuire i pod nello spazio dei nomi 'azds'".

Azure Dev Spaces non è riuscito a creare un controller nel cluster AKS perché non è riuscito a trovare un nodo non intainted nello stato *Pronto* per pianificare i pod. Azure Dev Spaces richiede almeno un nodo Linux in uno stato *Ready* che consente di pianificare i pod senza specificare le torazioni.

Per risolvere questo problema, [aggiornare la configurazione taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) nel cluster AKS per garantire che almeno un nodo Linux consenta di pianificare i pod senza specificare le tolarazioni. Assicurarsi inoltre che almeno un nodo Linux che consenta di pianificare i pod senza specificare le tolazioni sia nello stato *Pronto.* Se il nodo impiega molto tempo per raggiungere lo stato *Pronto,* è possibile provare a riavviare il nodo.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Errore "Interfaccia della riga di comando Azure Dev Spaces non installata correttamente" durante l'esecuzione di az aks use-dev-spaces

Un aggiornamento dell'interfaccia della riga di comando di Azure Dev Spaces ha modificato il percorso di installazione. Se si usa una versione dell'interfaccia della riga di comando di Azure precedente alla 2.0.63, è possibile che venga visualizzato questo errore. Per visualizzare la versione dell'interfaccia della riga di comando di Azure, usare `az --version`.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Nonostante il messaggio `az aks use-dev-spaces` di errore durante l'esecuzione con una versione dell'interfaccia della riga di comando di Azure precedente alla 2.0.63, l'installazione ha esito positivo. È possibile continuare `azds` a utilizzare senza problemi.

Per risolvere questo problema, aggiornare l'installazione [dell'interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure alla versione 2.0.63 o successiva. Questo aggiornamento risolverà il messaggio `az aks use-dev-spaces`di errore visualizzato durante l'esecuzione di . In alternativa, è possibile continuare a usare la versione corrente dell'interfaccia della riga di comando di Azure e dell'interfaccia della riga di comando di Azure Dev Spaces.Alternatively, you can continue to use your current version of the Azure CLI and the Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Errore "Impossibile raggiungere kube-apiserver"

Questo errore potrebbe essere visualizzato quando Azure Dev Spaces non è in grado di connettersi al server API del cluster AKS.

Se l'accesso al server API del cluster AKS è bloccato o se per il cluster AKS sono abilitati intervalli di [indirizzi IP autorizzati al server API,](../aks/api-server-authorized-ip-ranges.md) è necessario [creare](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) o [aggiornare](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) anche il cluster per [consentire intervalli aggiuntivi in base all'area.](https://github.com/Azure/dev-spaces/tree/master/public-ips)

Assicurarsi che il server API sia disponibile eseguendo i comandi kubectl. Se il server API non è disponibile, contattare il supporto AKS e riprovare quando il server API funziona.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemi comuni durante la preparazione del progetto per Azure Dev SpacesCommon issues when preparing your project for Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avviso "Impossibile generare Dockerfile a causa di una lingua non supportata"
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. Quando si `azds prep` esegue in una directory con codice scritto in uno di questi linguaggi, Azure Dev Spaces crea automaticamente un Dockerfile appropriato.

È comunque possibile usare Azure Dev Spaces con codice scritto in altri linguaggi, ma è necessario creare manualmente il Dockerfile prima di eseguire `azds up` per la prima volta.

Se l'applicazione è scritta in un linguaggio non supportato in modo nativo da Azure Dev Spaces, è necessario fornire un Dockerfile appropriato per compilare un'immagine contenitore che esegue il codice. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e [informazioni di riferimento su Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un documento Dockerfile in base alle proprie esigenze.

Dopo aver verificato un Dockerfile appropriato, eseguire per eseguire l'applicazione in Azure Dev Spaces.Once you have an appropriate Dockerfile in place, you run `azds up` to run your application in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemi comuni all'avvio o all'arresto dei servizi con Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Errore "File di configurazione non trovato:"

Durante `azds up`l'esecuzione , è possibile che venga visualizzato questo errore. Entrambi `azds up` `azds prep` e devono essere eseguiti dalla directory radice del progetto che si desidera eseguire nello spazio di sviluppo.

Per risolvere il problema:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. Se non si dispone di un file _azds.yaml_ nella cartella del codice, eseguire `azds prep` per generare gli asset Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout in "In attesa della compilazione dell'immagine del contenitore..." passaggio con i nodi virtuali AKS

Questo timeout si verifica quando si tenta di utilizzare Spazi dev per eseguire un servizio configurato per l'esecuzione in un [nodo virtuale AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces attualmente non supporta la compilazione o il debug di servizi su nodi virtuali.

Se si esegue `azds up` con l'opzione `--verbose` o si abilita la registrazione dettagliata in Visual Studio, vengono visualizzati altri dettagli:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Il comando precedente mostra che il pod del servizio è stato assegnato a *virtual-node-aci-linux*, che è un nodo virtuale.

Per risolvere questo problema, aggiornare il grafico Helm per il servizio per rimuovere eventuali *nodeSelector* o valori di *torazioni* che consentono l'esecuzione del servizio su un nodo virtuale. Questi valori sono in genere definiti nel file `values.yaml` del grafico.

È comunque possibile usare un cluster AKS con la funzionalità dei nodi virtuali abilitata, se il servizio che si desidera compilare o eseguire il debug tramite Dev Spaces viene eseguito in un nodo VM. L'esecuzione di un servizio con dev Space in un nodo VM è la configurazione predefinita.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Errore "Impossibile trovare un pod di timtore pronto" all'avvio di Dev Spaces

Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

Per risolvere questo problema, riavviare i nodi agente nel cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Errore "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\> \<'\>already exists" o "Pull access denied for servicename , repository does not exist or may require 'docker login'"

Questi errori possono verificarsi se si combinano `helm upgrade`i `helm delete`comandi Helm diretto in `azds up` `azds down`esecuzione (ad `helm install`esempio , , o ) con i comandi Dev Spaces (ad esempio e ) all'interno dello stesso spazio di sviluppo. Si verificano perché Dev Spaces ha la propria istanza Tiller, che è in conflitto con la propria istanza di Tiller in esecuzione nello stesso spazio di sviluppo.

È consigliabile usare sia i comandi Helm che i comandi Dev Spaces nello stesso cluster AKS, ma ogni spazio dei nomi abilitato per Dev Spaces deve usare uno o l'altro.

Si supponga, ad esempio, di utilizzare un comando Helm per eseguire l'intera applicazione in uno spazio di sviluppo padre. È possibile creare spazi di sviluppo figlio all'interno di tale elemento padre, usare gli spazi di sviluppo per eseguire singoli servizi all'interno degli spazi di sviluppo figlio e testare i servizi insieme. Quando si è pronti a archiviare le modifiche, usare un comando Helm per distribuire il codice aggiornato nello spazio di sviluppo padre. Non usare `azds up` per eseguire il servizio aggiornato nello spazio di sviluppo padre, perché sarà in conflitto con il servizio inizialmente eseguito con Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile esistente non utilizzato per compilare un contenitore

I spazi di sviluppo di Azure possono essere configurati in modo da puntare a un _Dockerfile_ specifico nel progetto. Se sembra che Azure Dev Spaces non usi il documento _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito ad Azure Dev Spaces quale Dockerfile usare. 

Per risolvere questo problema, aprire il file _azds.yaml_ generato da Azure Dev Spaces nel progetto. Aggiorna *configurazioni: develop: build: dockerfile* in modo che punti al Dockerfile che si desidera utilizzare. Ad esempio:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Errore "non autorizzato: autenticazione necessaria" quando si tenta di utilizzare un'immagine Docker da un Registro di sistema privato

Si utilizza un'immagine Docker da un registro privato che richiede l'autenticazione.

Per risolvere questo problema, è possibile consentire spazi di sviluppo per autenticare ed estrarre immagini da questo registro privato utilizzando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Per usare imagePullSecrets, [crea un segreto Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) nello spazio dei nomi in cui stai usando l'immagine. Quindi fornire il segreto come `azds.yaml`imagePullSecret in .

Di seguito è riportato un esempio `azds.yaml`di specifica di imagePullSecrets in .

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
> L'impostazione `azds.yaml` di imagePullSecrets in `values.yaml`eseguirà l'override di imagePullSecrets specificato nell'oggetto .

### <a name="error-service-cannot-be-started"></a>Errore "Impossibile riavviare il servizio".

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. Per ottenere ulteriori informazioni di diagnostica, abilitare la registrazione più dettagliata all'avvio del servizio.

Dalla riga di comando, utilizzare il `--verbose` per abilitare la registrazione più dettagliata. È inoltre possibile specificare `--output`un formato di output utilizzando . Ad esempio:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Aprire **Strumenti > Opzioni** e in **Projects and Solutions** (Progetti e soluzioni), scegliere **Build and Run** (Compila ed esegui).
2. Modificare le impostazioni per **MSBuild project build output verbosity** (Livello di dettaglio output di compilazione progetto MSBuild) in **Detailed** (Dettagliato) oppure in **Diagnostic** (Diagnostica).

    ![Screenshot della finestra di dialogo Strumenti > Opzioni](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Riesecuzione di un servizio dopo la ripetizione della creazione del controller

Viene visualizzato l'errore *Service cannot be started* quando si prova a rieseguire un servizio dopo la rimozione e quindi la ricreazione del controller Azure Dev Spaces associato al cluster. In questo caso, l'output dettagliato contiene il testo seguente:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Questo errore si verifica perché la rimozione del controller Dev Spaces non rimuove i servizi installati in precedenza dal controller. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Errore "Impossibile riavviare il servizio". quando si utilizza Dockerfiles multi-stage

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

Questo errore si verifica perché Azure Dev Spaces non supporta attualmente le compilazioni a più fasi. Per evitare compilazioni in più fasi, riscrivere il documento Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Il traffico di rete non viene inoltrato al cluster AKS quando si connette il computer di sviluppo

Quando si usano [Azure Dev Spaces per connettere il cluster AKS al computer](how-to/connect.md)di sviluppo , è possibile che si verifichi un problema a quando il traffico di rete non viene inoltrato tra il computer di sviluppo e il cluster AKS.

Quando si connette il computer di sviluppo al cluster AKS, Azure Dev Spaces inoltra il traffico `hosts` di rete tra il cluster AKS e il computer di sviluppo modificando il file del computer di sviluppo. Azure Dev Spaces crea `hosts` una voce nel con l'indirizzo del servizio Kubernetes che si sta sostituendo come nome host. Questa voce viene utilizzata con l'inoltro delle porte per indirizzare il traffico di rete tra il computer di sviluppo e il cluster AKS. Se un servizio nel computer di sviluppo è in conflitto con la porta del servizio Kubernetes che si sta sostituendo, Azure Dev Spaces non può inoltrare il traffico di rete per il servizio Kubernetes. Ad esempio, il servizio *Windows BranchCache* è in genere associato a *0.0.0.0:80*, che i conflitti causeranno un conflitto per la porta 80 in tutti gli indirizzi IP locali.

Per risolvere questo problema, è necessario arrestare tutti i servizi o processi in conflitto con la porta del servizio Kubernetes che si sta tentando di sostituire. È possibile utilizzare strumenti, ad esempio *netstat*, per esaminare quali servizi o processi nel computer di sviluppo sono in conflitto.

Ad esempio, per arrestare e disabilitare il servizio BranchCache di *Windows:*
* Eseguire `services.msc` dal prompt dei comandi.
* Fare clic con il pulsante destro del mouse su *BranchCache* e scegliere *Proprietà*.
* Fare clic su *Arresta*.
* Facoltativamente, è possibile disabilitarlo impostando *Tipo di avvio* *su Disabilitato*.
* Fare clic su *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Errore "nessun AzureAssignedIdentity trovato per pod:azds/azds-webhook-deployment id\<\> nello stato assegnato"

Quando si esegue un servizio con Azure Dev Spaces in un cluster AKS con [un'identità gestita](../aks/use-managed-identity.md) e [identità gestite pod installate,](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) il processo potrebbe bloccarsi dopo il passaggio di installazione del *grafico.* Se si esamina il *azds-injector-webhook* nello spazio dei nomi *azds,* è possibile che venga visualizzato questo errore.

I servizi che Azure Dev Spaces viene eseguito nel cluster usano l'identità gestita del cluster per comunicare con i servizi back-end azure Dev Spaces all'esterno del cluster. Quando viene installata l'identità gestita dal pod, le regole di rete vengono configurate nei nodi del cluster per reindirizzare tutte le chiamate per le credenziali dell'identità gestita a un [DaemonSet Identità gestita nodo (NMI) installato nel cluster.](https://github.com/Azure/aad-pod-identity#node-managed-identity) Questo NMI DaemonSet identifica il pod chiamante e garantisce che il pod sia stato etichettato in modo appropriato per accedere all'identità gestita richiesta. Azure Dev Spaces non è in grado di rilevare se in un cluster è installata l'identità gestita da pod e non è possibile eseguire la configurazione necessaria per consentire ai servizi Azure Dev Spaces di accedere all'identità gestita del cluster. Poiché i servizi Azure Dev Spaces non sono stati configurati per accedere all'identità gestita del cluster, NMI DaemonSet non consentirà loro di ottenere un token AAD per l'identità gestita e non comunicherà con i servizi back-end di Azure Dev Spaces.

Per risolvere questo problema, applicare [un'eccezione AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) per i pod *azds-injector-webhook* e update instrumentati da Azure Dev Spaces per accedere all'identità gestita.

Creare un file denominato *webhookException.yaml* e copiare la seguente definizione YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Il file precedente crea un oggetto *AzurePodIdentityException* per *azds-injector-webhook*. Per distribuire questo `kubectl`oggetto, utilizzare :

```cmd
kubectl apply -f webhookException.yaml
```

Per aggiornare i pod instrumentati da Azure Dev Spaces per accedere all'identità gestita, aggiornare *lo spazio dei nomi* nella definizione YAML seguente e usarlo `kubectl` per applicarlo per ogni spazio di sviluppo.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

In alternativa, è possibile creare oggetti *AzureIdentity* e *AzureIdentityBinding* e aggiornare le etichette dei pod per i carichi di lavoro in esecuzione in spazi instrumentati da Azure Dev Spaces per accedere all'identità gestita creata dal cluster AKS.

Per elencare i dettagli dell'identità gestita, eseguire il comando seguente per il cluster AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Il comando precedente restituisce *clientId* e *resourceId* per l'identità gestita. Ad esempio:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Per creare un oggetto *AzureIdentity,* creare un file denominato *clusteridentity.yaml* e usare la definizione YAML seguente aggiornata con i dettagli dell'identità gestita dal comando precedente:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Per creare un oggetto *AzureIdentityBinding,* creare un file denominato *clusteridentitybinding.yaml* e usare la definizione YAML seguente:To create an AzureIdentityBinding object, create a file named clusteridentitybinding.yaml and use the following YAML definition:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Per distribuire gli oggetti AzureIdentity e `kubectl` *AzureIdentityBinding,* usare:To deploy the *AzureIdentity* and AzureIdentityBinding objects, use:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Dopo aver distribuito gli oggetti *AzureIdentity* e *AzureIdentityBinding,* qualsiasi carico di lavoro con *aadpodidbinding: l'etichetta my-label-value* può accedere all'identità gestita del cluster. Aggiungere questa etichetta e ridistribuire tutti i carichi di lavoro in esecuzione in qualsiasi spazio di sviluppo. Ad esempio:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemi comuni relativi all'uso del codice di Visual Studio e Visual Studio con gli spazi di sviluppo di AzureCommon issues using Visual Studio and Visual Studio Code with Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Errore "Strumenti e configurazioni necessari mancanti"

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

Provare ad avviare il codice VS da un prompt dei comandi in cui la variabile di ambiente PATH è impostata correttamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Errore "Required tools to build and debug 'projectname' are out of date" (Gli strumenti necessari per la compilazione e il debug di 'nome progetto' non sono aggiornati)

Questo errore viene visualizzato in Visual Studio Code se sono presenti una versione dell'estensione VS Code per Azure Dev Spaces più recente e una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

Provare a scaricare e installare la versione più recente dell'interfaccia della riga di comando di Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Errore: "Impossibile trovare l'estensione del debugger per type:coreclr"

Questo errore può essere visualizzato durante l'esecuzione del debugger del codice di Visual Studio. È possibile che nel computer di sviluppo sia installata l'estensione del codice Visual Basic per C. L'estensione di C' include il supporto per il debug per .NET Core (CoreCLR).

Per risolvere questo problema, installare [l'estensione di codice VS per C .](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Errore "Tipo di debug configurato 'coreclr' non supportato"

Questo errore può essere visualizzato durante l'esecuzione del debugger del codice di Visual Studio. È possibile che nel computer di sviluppo sia installata l'estensione del codice VS per Azure Dev Spaces.You might not have the VS Code extension for Azure Dev Spaces installed on your development machine.

Per risolvere questo problema, installare [l'estensione di codice VS per Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Errore "Valore 'cwd' non valido '/src'. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato) o "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[percorso file binario di progetto]' non esiste)

Questo errore può essere visualizzato durante l'esecuzione del debugger del codice di Visual Studio. Per impostazione predefinita, l'estensione VS Code usa `src` come directory di lavoro per il progetto nel contenitore. Se è stato aggiornato il `Dockerfile` per specificare una directory di lavoro diversa, è possibile che venga visualizzato questo errore.

Per risolvere questo problema, aggiornare il `launch.json` file nella `.vscode` sottodirectory della cartella del progetto. Modificare la direttiva `configurations->cwd` in modo da puntare alla stessa directory `WORKDIR` definita nel `Dockerfile` del progetto. Potrebbe inoltre essere necessario aggiornare anche la direttiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Errore "Il programma pipe 'azds' è terminato in modo imprevisto con codice 126."

Questo errore può essere visualizzato durante l'esecuzione del debugger del codice di Visual Studio.

Per risolvere questo problema, chiudere e riaprire Visual Studio Code. Riavviare il debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Errore "Controllo interno non riuscito: orologio ENOSPC" quando si collega il debug a un'applicazione Node.js

Questo errore si verifica quando il nodo che esegue il pod con l'applicazione Node.js a cui si sta tentando di connettersi con un debugger ha superato il valore *fs.inotify.max_user_watches.* In alcuni casi, [il valore predefinito di *fs.inotify.max_user_watches* potrebbe essere troppo piccolo per essere gestito il collegamento di un debugger direttamente a un pod](https://github.com/Azure/AKS/issues/772).

Una soluzione temporanea per questo problema consiste nell'aumentare il valore di *fs.inotify.max_user_watches* in ogni nodo del cluster e riavviare tale nodo per rendere effettive le modifiche.

## <a name="other-common-issues"></a>Altri problemi comuni

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Errore "azds" non è riconosciuto come un comando interno o esterno, programma utilizzabile o file batch

Questo errore può `azds.exe` verificarsi se non è installato o configurato correttamente.

Per risolvere il problema:

1. Controllare il percorso %ProgramFiles%/Microsoft SDKs Azure Azure `azds.exe`Dev Spaces CLI per . Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se `azds.exe` non è installato, eseguire il comando seguente:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Errore di autorizzazione "Microsoft.DevSpaces/register/action"

Per gestire Azure Dev Spaces, è necessario l'accesso *Proprietario* o *Collaboratore* nella sottoscrizione di Azure. Se si sta tentando di gestire Dev Spaces e non si dispone dell'accesso *Proprietario* o *Collaboratore* alla sottoscrizione di Azure associata, è possibile che venga visualizzato un errore di autorizzazione. Ad esempio:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Per risolvere questo problema, usando un account con accesso Proprietario `Microsoft.DevSpaces` o *Collaboratore* alla sottoscrizione di Azure, registrare manualmente lo spazio dei nomi:To fix this issue, using an account with *Owner* or Contributor access to the Azure subscription, manually register the namespace:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nuovi contenitori non vengono avviati

L'inizializzatore Kubernetes non può applicare PodSpec per i nuovi pod a causa delle modifiche dell'autorizzazione RBAC al ruolo di amministratore del *cluster* nel cluster. Il nuovo pod potrebbe anche avere un PodSpec non valido, ad esempio l'account di servizio associato al pod non esiste più. Per visualizzare i pod che si trovano nello stato *In* `kubectl get pods` sospeso a causa del problema dell'inizializzatore, utilizzare il comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Questo problema può influire sui pod in *tutti gli spazi dei nomi* del cluster, inclusi gli spazi dei nomi in cui Azure Dev Spaces non è abilitato.

Per risolvere questo problema, [aggiornare l'interfaccia della riga](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) di comando Dev Spaces alla versione più recente e quindi eliminare la *InitializerConfiguration azds* dal controller Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Dopo aver rimosso *azds InitializerConfiguration* dal controller Azure `kubectl delete` Dev Spaces, usare per rimuovere eventuali pod in stato *In sospeso.* Dopo che tutti i pod in sospeso sono stati rimossi, ridistribuite i pod.

Se i nuovi pod sono ancora bloccati nello stato `kubectl delete` In *sospeso* dopo una ridistribuzione, utilizzare per rimuovere tutti i pod in stato In *sospeso.* Dopo aver rimosso tutti i pod in sospeso, eliminare il controller dal cluster e reinstallarlo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Dopo la reinstallazione del controller, ridistribuite i pod.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorizzazioni RBAC non corrette per chiamare API e controller Dev Spaces

L'utente che accede al controller Azure Dev Spaces deve avere accesso per leggere il *kubeconfig* amministratore nel cluster AKS. Ad esempio, questa autorizzazione è disponibile nel ruolo di amministrazione del cluster di [servizi di Azure Kubernetes incorporato.](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) L'utente che accede al controller Azure Dev Spaces deve inoltre disporre del ruolo di controllo degli accessi in base al ruolo *Collaboratore* o *Proprietario* per il controller. Ulteriori dettagli sull'aggiornamento delle autorizzazioni di un utente per un cluster AKS sono disponibili [qui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Per aggiornare il ruolo RBAC dell'utente per il controller:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare al gruppo di risorse contenente il controller, che in genere corrisponde al cluster AKS.
1. Attivare la casella di controllo *Mostra tipi nascosti.*
1. Fare clic sul controller.
1. Aprire il riquadro *Controllo di accesso (IAM).*
1. Fare clic sulla scheda *Assegnazioni ruolo.*
1. Fare clic su *Aggiungi,* quindi *su Aggiungi assegnazione ruolo*.
    * Per *Ruolo*, selezionare *Collaboratore* o *Proprietario*.
    * In *Assegna accesso a* selezionare *Utente, gruppo o entità servizio di Azure AD*.
    * In *Seleziona*, cercare l'utente a cui si desidera concedere le autorizzazioni.
1. Fare clic su *Salva*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

È possibile configurare un endpoint URL pubblico per il servizio specificando l'opzione `--enable-ingress` per il comando `azds prep` o selezionando la casella di controllo `Publicly Accessible` in Visual Studio. Il nome DNS pubblico viene registrato automaticamente quando si esegue il servizio in Dev Spaces. Se questo nome DNS non viene registrato, viene visualizzato un errore simile a *Non è possibile visualizzare la pagina* o *Non è possibile raggiungere il sito* nel Web browser quando ci si connette all'URL pubblico.

Per risolvere il problema:

* Controlla lo stato di tutti gli URL associati ai servizi Dev Spaces:

  ```console
  azds list-uris
  ```

* Se un URL è nello stato *In sospeso,* Dev Spaces è ancora in attesa del completamento della registrazione DNS. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.
* Se un URL rimane nello stato *In sospeso* per più di 5 minuti, ciò potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico o con il pod del controller in ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. Utilizzate i seguenti comandi per eliminare questi contenitori e consentire ad AKS di ricrearle automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Errore "errore di connessione upstream o disconnessione/ripristino prima delle intestazioni"

Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. Questo errore indica che la porta contenitore non è disponibile. Questo può per i seguenti motivi:

* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente in _Dockerfile_, Helm Chart e qualsiasi codice server che apre una porta.

Per risolvere il problema:

1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Controllare la configurazione della porta nelle risorse seguenti:
    * **[Grafico helm](https://docs.helm.sh):** Specificato da `service.port` e `deployment.containerPort` in values.yaml sottoposto a scaffolding dal `azds prep` comando.
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Impossibile trovare il nome del tipo o dello spazio dei nomi "MyLibrary"

Impossibile trovare un progetto di libreria in uso. Con Dev Spaces, il contesto di compilazione è a livello di progetto/servizio per impostazione predefinita.  

Per risolvere il problema:

1. Modificare `azds.yaml` il file per impostare il contesto di compilazione a livello di soluzione.
2. Modificare `Dockerfile` i `Dockerfile.develop` file e in modo che `.csproj`facciano riferimento ai file di progetto, ad esempio , correttamente relativi al nuovo contesto di compilazione.
3. Aggiungere `.dockerignore` un nella stessa `.sln` directory del file.
4. Aggiornare `.dockerignore` con le voci aggiuntive in base alle esigenze.

È possibile trovare un esempio [qui](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>La scalabilità automatica del pod orizzontale non funziona in uno spazio di sviluppoHorizontal pod autoscaling not working in a dev space

Quando si esegue un servizio in uno spazio di sviluppo, il pod del servizio viene [inserito con contenitori aggiuntivi per](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) la strumentazione e tutti i contenitori in un pod devono avere limiti di risorse e richieste impostate per la scalabilità automatica del pod orizzontale.

Per risolvere questo problema, applicare una richiesta di risorsa e limitare ai contenitori Dev Spaces inseriti. Le richieste e i limiti delle risorse possono essere applicati per `azds.io/proxy-resources` il contenitore inserito (devspaces-proxy) aggiungendo l'annotazione alla specifica pod. Il valore deve essere impostato su un oggetto JSON che rappresenta la sezione delle risorse della specifica del contenitore per il proxy.

Di seguito è riportato un esempio di annotazione proxy-resources che deve essere applicata alla specifica pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Abilitare Spazi di sviluppo di Azure in uno spazio dei nomi esistente con pod in esecuzioneEnable Azure Dev Spaces on an existing namespace with running pods

You may have an existing AKS cluster and namespace with running pods where you want to enable Azure Dev Spaces.

Per abilitare spazi di sviluppo di Azure in `use-dev-spaces` uno `kubectl` spazio dei nomi esistente in un cluster AKS, eseguire e usare per riavviare tutti i pod in tale spazio dei nomi.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Dopo aver riavviato i pod, è possibile iniziare a usare lo spazio dei nomi esistente con Azure Dev Spaces.After your pods have restarted, you can begin using your existing namespace with Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Abilitare gli spazi di sviluppo di Azure nel cluster AKS con traffico in uscita limitato per i nodi del clusterEnable Azure Dev Spaces on AKS cluster with restricted egress traffic for cluster nodes

Per abilitare gli spazi di sviluppo di Azure in un cluster AKS per il quale il traffico in uscita dai nodi del cluster è limitato, è necessario consentire i seguenti FQDN:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Per estrarre linux immagini alpine e altre immagini di Azure Dev SpacesTo pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 (informazioni in due) | Per estrarre le immagini del timone/tiller|
| storage.googleapis.com | HTTP:443 (informazioni in due) | Per estrarre le immagini del timone/tiller|
| azds-<guid>. <location>File azds.io | HTTPS:443 | Per comunicare con i servizi back-end di Azure Dev Spaces per il controller. L'FQDN esatto è disponibile in "dataplaneFqdn"\.in %USERPROFILE% azds-settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Errore "Impossibile trovare \<\> il \<cluster\>cluster in subscriptionId "

Questo errore può essere visualizzato se il file kubeconfig ha come destinazione un cluster o una sottoscrizione diverso da quello che si sta tentando di usare con gli strumenti lato client Azure Dev Spaces.You may see this error if your kubeconfig file is targeting a different cluster or subscription than you are trying to use with the Azure Dev Spaces client side tooling. Gli strumenti lato client Azure Dev Spaces replicano il comportamento di *kubectl*, che usa [uno o più file kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) per selezionare e comunicare con il cluster.

Per risolvere il problema:

* Utilizzare `az aks use-dev-spaces -g <resource group name> -n <cluster name>` per aggiornare il contesto corrente. Questo comando abilita anche gli spazi di sviluppo di Azure nel cluster AKS se non è già abilitato. In alternativa, è `kubectl config use-context <cluster name>` possibile utilizzare per aggiornare il contesto corrente.
* Usare `az account show` per visualizzare la sottoscrizione di Azure corrente di destinazione e verificare che sia corretta. È possibile modificare la sottoscrizione `az account set`di destinazione utilizzando .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Errore durante l'utilizzo dei vani di sviluppo dopo la rotazione dei certificati AKS

Dopo [aver ruotato i certificati nel cluster AKS,](../aks/certificate-rotation.md)alcune operazioni, ad `azds space list` esempio e `azds up` avranno esito negativo. È inoltre necessario aggiornare i certificati nel controller Spazi di sviluppo di Azure dopo aver ruotato i certificati nel cluster.

Per risolvere questo problema, verificare che il *kubeconfig* disponga dei certificati aggiornati utilizzando `az aks get-credentials` quindi eseguire il `azds controller refresh-credentials` comando. Ad esempio:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
