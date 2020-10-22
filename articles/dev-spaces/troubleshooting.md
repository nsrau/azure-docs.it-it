---
title: Risoluzione dei problemi
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Informazioni su come individuare e risolvere i problemi comuni relativi all'abilitazione e all'uso di Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 42551443fb5af1bd3f783c33f708b231eea68907
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364168"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Risoluzione dei problemi di Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

Se si verifica un problema durante l'uso di Azure Dev Spaces, inviare una [segnalazione nel repository GitHub di Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Prima di iniziare

Per risolvere i problemi in modo più efficace, può essere utile creare log maggiormente dettagliati per la revisione.

Per Visual Studio, impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati vengono scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i log più dettagliati in `%TEMP%\Azure Dev Spaces`. In un Mac la directory *TEMP* è reperibile eseguendo `echo $TMPDIR` in una finestra del terminale. In un computer Linux la directory *TEMP* è solitamente `/tmp`. Verificare inoltre che la registrazione sia abilitata nel [file di configurazione dell'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure Dev Spaces inoltre funziona meglio con il debug di una singola istanza, o pod. Il file `azds.yaml` contiene un'impostazione, *replicaCount*, che indica il numero di pod eseguiti da Kubernetes per il servizio. Se si cambia l'impostazione *replicaCount* per configurare l'app in modo che esegua più pod per un determinato servizio, il debugger si collega al primo pod, se l'elenco è in ordine alfabetico. Il debugger si collega a un pod diverso quando il pod originale viene riciclato, determinando un comportamento imprevisto.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemi comuni relativi all'abilitazione di Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Messaggio di errore analogo a "Non è stato possibile creare il controller di Azure Dev Spaces"

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, per correggerlo, eliminare e ricreare il controller.

È anche possibile provare a eliminare il controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Per eliminare il controller, usare l'interfaccia della riga di comando di Azure Dev Spaces. Non è possibile eliminare un controller da Visual Studio. Non è inoltre possibile installare l'interfaccia della riga di comando di Azure Dev Spaces in Azure Cloud Shell, quindi non è possibile eliminare un controller da Azure Cloud Shell.

Se l'interfaccia della riga di comando di Azure Dev Spaces non è installata, è possibile prima installarla usando il comando seguente e poi eliminare il controller:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>La creazione del controller non riesce a causa della lunghezza del relativo nome

Il nome di un controller di Azure Dev Spaces non può contenere più di 31 caratteri. Se il nome del controller supera i 31 caratteri quando si abilita Dev Spaces in un cluster del servizio Azure Kubernetes, si riceverà un errore. Ad esempio:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Per risolvere questo problema, creare un controller con un nome alternativo. Ad esempio:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>L'abilitazione di Dev Spaces non riesce quando vengono aggiunti pool di nodi Windows a un cluster del servizio Azure Kubernetes

Attualmente, Azure Dev Spaces è destinato all'esecuzione solo in pod e nodi Linux. Nel caso di un cluster del servizio Azure Kubernetes con un pool di nodi Windows, è necessario assicurarsi che i pod di Azure Dev Spaces vengano pianificati solo in nodi Linux. Se l'esecuzione di un pod di Azure Dev Spaces è pianificata in un nodo Windows, tale pod non verrà avviato e non sarà possibile abilitare Dev Spaces.

Per risolvere questo problema, [aggiungere una contaminazione](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) al cluster del servizio Azure Kubernetes per assicurarsi che non venga pianificata l'esecuzione di pod Linux in un nodo Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Messaggio di errore analogo a "Non sono stati trovati nodi Linux non contaminati con stato Pronto nel cluster. Per distribuire pod nello spazio dei nomi 'azds', è necessario che sia presente almeno un nodo Linux non contaminato con lo stato Pronto".

Azure Dev Spaces non è stato in grado di creare un controller nel cluster del servizio Azure Kubernetes perché non è stato trovato un nodo non contaminato con lo stato *Pronto* in cui pianificare i pod. Azure Dev Spaces richiede almeno un nodo Linux con lo stato *Pronto* che consenta la pianificazione di pod senza specificare tolleranze.

Per risolvere questo problema, [aggiornare la configurazione delle contaminazioni](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) nel cluster del servizio Azure Kubernetes per assicurare che almeno un nodo Linux consenta la pianificazione di pod senza specificare tolleranze. Assicurarsi inoltre che almeno un nodo Linux che consente la pianificazione di pod senza specificare tolleranze abbia lo stato *Pronto*. Se il nodo richiede molto tempo per raggiungere lo stato *Pronto*, provare a riavviarlo.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Messaggio di errore analogo a "L'interfaccia della riga di comando di Azure Dev Spaces non è stata installata correttamente" visualizzato quando si esegue az aks use-dev-spaces

Un aggiornamento apportato all'interfaccia della riga di comando di Azure Dev Spaces ne ha cambiato il percorso di installazione. Questo messaggio di errore può essere visualizzato se si usa una versione dell'interfaccia della riga di comando di Azure precedente a 2.0.63. Per visualizzare la versione corrente dell'interfaccia della riga di comando di Azure, usare `az --version`.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Nonostante venga visualizzato questo messaggio di errore quando si esegue `az aks use-dev-spaces` con una versione dell'interfaccia della riga di comando di Azure precedente a 2.0.63, l'installazione viene completata. È possibile continuare a usare `azds` senza problemi.

Per risolvere il problema, aggiornare l'installazione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) alla versione 2.0.63 o successiva. Questo aggiornamento consentirà di risolvere il messaggio di errore che si riceve quando si esegue `az aks use-dev-spaces`. In alternativa, è possibile continuare a usare la versione corrente dell'interfaccia della riga di comando di Azure e dell'interfaccia della riga di comando di Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Messaggio di errore analogo a "Non è possibile raggiungere kube-apiserver"

È possibile che venga visualizzato questo messaggio di errore quando Azure Dev Spaces non è in grado di connettersi al server API del cluster del servizio Azure Kubernetes.

Se l'accesso al server API del cluster AKS è bloccato o se sono stati abilitati gli [intervalli di indirizzi IP autorizzati](../aks/api-server-authorized-ip-ranges.md) per il cluster AKS, è necessario [creare](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) o [aggiornare](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) anche il cluster per [consentire intervalli aggiuntivi in base all'area geografica](configure-networking.md#aks-cluster-network-requirements)

Assicurarsi che il server API sia disponibile eseguendo i comandi kubectl. Se il server API non è disponibile, contattare il supporto del servizio Azure Kubernetes e riprovare quando il server API funziona.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemi comuni durante la preparazione del progetto per Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Messaggio di avviso analogo a "Non è stato possibile generare il Dockerfile perché il linguaggio non è supportato"
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. Se si esegue `azds prep` in una directory con codice scritto in uno di questi linguaggi, Azure Dev Spaces crea automaticamente un Dockerfile appropriato.

È comunque possibile usare Azure Dev Spaces con codice scritto in altri linguaggi, ma è necessario creare manualmente il Dockerfile prima di eseguire `azds up` per la prima volta.

Se l'applicazione è scritta in un linguaggio non supportato da Azure Dev Spaces in modalità nativa, è necessario fornire un Dockerfile appropriato per creare un'immagine del contenitore che esegua il codice. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e [informazioni di riferimento su Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un documento Dockerfile in base alle proprie esigenze.

Quando è disponibile un Dockerfile appropriato, eseguire `azds up` per avviare l'applicazione in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemi comuni durante l'avvio o l'arresto di servizi con Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Messaggio di errore analogo a "Il file di configurazione non è stato trovato:"

Questo messaggio di errore può essere visualizzato durante l'esecuzione di `azds up`. È necessario eseguire sia `azds up` che `azds prep` dalla directory radice del progetto in cui eseguire lo spazio di Dev Spaces.

Per risolvere il problema:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. Se la cartella del codice non include un file _azds.yaml_, eseguire `azds prep` per generare gli asset Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Si verifica un timeout durante l'attesa della compilazione dell'immagine del contenitore con i nodi virtuali del servizio Azure Kubernetes

Questo timeout si verifica quando si prova a usare Dev Spaces per eseguire un servizio configurato per l'esecuzione in un [nodo virtuale del servizio Azure Kubernetes](../aks/virtual-nodes-portal.md). Attualmente Dev Spaces non supporta i servizi di compilazione o debug nei nodi virtuali.

Se si esegue `azds up` con l'opzione `--verbose` o si abilita la registrazione dettagliata in Visual Studio, vengono visualizzati altri dettagli:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Il comando precedente indica che il pod del servizio è stato assegnato a *virtual-node-aci-linux*, che è un nodo virtuale.

Per risolvere questo problema, aggiornare il grafico Helm per il servizio in modo da rimuovere i valori *nodeSelector* o *tolerations* che consentono l'esecuzione del servizio in un nodo virtuale. Questi valori sono in genere definiti nel file `values.yaml` del grafico.

È comunque possibile usare un cluster del servizio Azure Kubernetes con la funzionalità dei nodi virtuali abilitata, se il servizio di cui eseguire la compilazione o il debug tramite Dev Spaces viene eseguito in un nodo di macchina virtuale. L'esecuzione di un servizio con Dev Spaces in un nodo di macchina virtuale è la configurazione predefinita.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Messaggio di errore analogo a "Non è stato possibile trovare un pod Tiller pronto" visualizzato quando si avvia Dev Spaces

Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

Per risolvere questo problema, riavviare i nodi dell'agente nel cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Errore "versione azds- \<identifier\> - \<spacename\> - \<servicename\> non riuscita: i servizi ' \<servicename\> ' esistono già" o "accesso negato per \<servicename\> , il repository non esiste o potrebbe richiedere ' Docker login"

Questi errori possono verificarsi se si combina l'esecuzione di comandi di Helm diretti, ad esempio `helm install`, `helm upgrade`o `helm delete`, con comandi di Dev Spaces, come `azds up` e `azds down`, all'interno dello stesso spazio di Dev Spaces. Si verificano perché Dev Spaces include una propria istanza di Tiller, che entra in conflitto con l'istanza di Tiller dell'utente in esecuzione nello stesso spazio di Dev Spaces.

È possibile usare sia i comandi di Helm che quelli di Dev Spaces nello stesso cluster del servizio Azure Kubernetes, ma ogni spazio dei nomi abilitato per Dev Spaces deve usare l'uno o l'altro.

Si supponga, ad esempio, di usare un comando di Helm per eseguire l'intera applicazione in uno spazio di Dev Spaces padre. È possibile creare spazi di Dev Spaces figlio da tale spazio padre, usare Dev Spaces per eseguire i singoli servizi all'interno degli spazi figlio e testare i servizi insieme. Quando si è pronti ad archiviare le modifiche, usare un comando di Helm per distribuire il codice aggiornato nello spazio Dev Spaces padre. Non usare `azds up` per eseguire il servizio aggiornato nello spazio Dev Spaces padre, perché entrerà in conflitto con il servizio eseguito inizialmente con Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile esistente non usato per compilare un contenitore

Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non usi il documento _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito ad Azure Dev Spaces quale Dockerfile usare. 

Per risolvere questo problema, aprire il file _azds.yaml_ generato da Azure Dev Spaces nel progetto. Aggiornare *configurations: develop: build: dockerfile* in modo da puntare al Dockerfile da usare. Ad esempio:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Messaggio di errore analogo a "Operazione non autorizzata: autenticazione necessaria" visualizzato quando si prova a usare un'immagine Docker di un registro privato

Si usa un'immagine Docker di un registro privato che richiede l'autenticazione.

Per risolvere questo problema, è possibile consentire a Dev Spaces di eseguire l'autenticazione e il pull di immagini da questo registro privato usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Per usare imagePullSecrets, [creare un segreto di Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) nello spazio dei nomi in cui si usa l'immagine. Specificare quindi il segreto come imagePullSecret in `azds.yaml`.

Di seguito è riportato un esempio di come specificare imagePullSecrets in `azds.yaml`.

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
> L'impostazione di imagePullSecrets in `azds.yaml` eseguirà l'override di imagePullSecrets specificato in `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Messaggio di errore analogo a "Non è possibile avviare il servizio".

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. Per recuperare altre informazioni di diagnostica, abilitare la registrazione più dettagliata all'avvio del servizio.

Dalla riga di comando usare `--verbose` per abilitare la registrazione più dettagliata. È anche possibile specificare un formato di output usando `--output`. Ad esempio:

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

Questo errore si verifica perché la rimozione del controller di Dev Spaces non comporta la rimozione dei servizi che ha installato in precedenza. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Messaggio di errore analogo a "Non è possibile avviare il servizio" visualizzato durante l'uso di Dockerfile a più fasi

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

Questo errore si verifica perché Azure Dev Spaces attualmente non supporta le compilazioni a più fasi. Per evitare compilazioni in più fasi, riscrivere il documento Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Il traffico di rete non viene inoltrato al cluster del servizio Azure Kubernetes durante la connessione al computer di sviluppo

Quando si usa [Azure Dev Spaces per connettere il cluster del servizio Azure Kubernetes al computer di sviluppo](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes), è possibile che si verifichi un problema per cui il traffico di rete non viene inoltrato tra il computer di sviluppo e il cluster del servizio Azure Kubernetes.

Quando si connette il computer di sviluppo al cluster del servizio Azure Kubernetes, Azure Dev Spaces inoltra il traffico di rete tra il cluster del servizio Azure Kubernetes e il computer di sviluppo modificando il file `hosts` del computer di sviluppo. Azure Dev Spaces crea una voce in `hosts` con l'indirizzo del servizio Kubernetes che si prova a sostituire come nome host. Questa voce viene usata con il port forwarding per indirizzare il traffico di rete tra il computer di sviluppo e il cluster del servizio Azure Kubernetes. Se un servizio del computer di sviluppo è in conflitto con la porta del servizio Kubernetes che si prova a sostituire, Azure Dev Spaces non può inoltrare il traffico di rete per il servizio Kubernetes. Ad esempio, il servizio *Windows BranchCache* è in genere associato a *0.0.0.0:80*, che genera un conflitto per la porta 80 in tutti gli indirizzi IP locali.

Per risolvere questo problema, è necessario arrestare i servizi o i processi in conflitto con la porta del servizio Kubernetes che prova a sostituire. È possibile usare strumenti come *netstat* per scoprire quali servizi o processi sono in conflitto nel computer di sviluppo.

Ad esempio, per arrestare e disabilitare il servizio *Windows BranchCache*:
* Eseguire `services.msc` dal prompt dei comandi.
* Fare clic con il pulsante destro del mouse su *BranchCache* e scegliere *Proprietà*.
* Fare clic su *Arresta*.
* Facoltativamente, è possibile disabilitarlo impostando *Tipo di avvio* su *Disabilitato*.
* Fare clic su *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Errore "non sono stati trovati AzureAssignedIdentity per pod: azds/azds-webhook-Deployment- \<id\> in stato assegnato"

Quando si esegue un servizio con Azure Dev Spaces in un cluster AKS con un' [identità gestita](../aks/use-managed-identity.md) e le [identità gestite del Pod](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) installate, il processo potrebbe smettere di rispondere dopo il passaggio di *installazione del grafico* . Se si esamina *azds-injector-webhook* nello spazio dei nomi *azds*, è possibile che venga visualizzato questo errore.

I servizi che Azure Dev Spaces esegue nel cluster utilizzano l'identità gestita del cluster per comunicare con i servizi back-end di Azure Dev Spaces all'esterno del cluster. Quando l'identità gestita del pod è installata, nei nodi del cluster vengono configurate regole di rete per reindirizzare tutte le richieste di credenziali di identità gestite a un [DaemonSet NMI (identità gestita del nodo) installato nel cluster](https://github.com/Azure/aad-pod-identity#node-managed-identity). Questo DaemonSet NMI identifica il pod chiamante e assicura che sia stato etichettato correttamente per l'accesso all'identità gestita richiesta. Azure Dev Spaces non può rilevare se in un cluster è installata un'identità gestita del pod e non può eseguire la configurazione necessaria per consentire ai servizi di Azure Dev Spaces di accedere all'identità gestita del cluster. Poiché i servizi di Azure Dev Spaces non sono stati configurati per accedere all'identità gestita del cluster, DaemonSet non consentirà loro di ottenere un token Azure AD per l'identità gestita e non comunicherà con Azure Dev Spaces servizi back-end.

Per risolvere questo problema, applicare [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) per *azds-injector-webhook* e aggiornare i pod instrumentati da Azure Dev Spaces per accedere all'identità gestita.

Creare un file denominato *webhookexception.yaml* e copiare la definizione YAML seguente:

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

Il file precedente crea un oggetto *AzurePodIdentityException* per *azds-inietto-webhook*. Per distribuire questo oggetto, usare `kubectl`:

```cmd
kubectl apply -f webhookException.yaml
```

Per aggiornare i pod instrumentati da Azure Dev Spaces per accedere all'identità gestita, aggiornare lo *spazio dei nomi* nella definizione YAML seguente e usare `kubectl` per applicarlo per ogni spazio di Dev Spaces.

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

In alternativa, è possibile creare gli oggetti *AzureIdentity* e *AzureIdentityBinding* e aggiornare le etichette dei pod per i carichi di lavoro in esecuzione in spazi instrumentati da Azure Dev Spaces per accedere all'identità gestita creata dal cluster del servizio Azure Kubernetes.

Per elencare i dettagli dell'identità gestita, eseguire il comando seguente per il cluster del servizio Azure Kubernetes:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Il comando precedente restituisce i valori *clientId* e *resourceId* per l'identità gestita. Ad esempio:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Per creare un oggetto *AzureIdentity*, creare un file denominato *clusteridentity.yaml* e usare la definizione YAML seguente aggiornata con i dettagli dell'identità gestita dal comando precedente:

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

Per creare un oggetto *AzureIdentityBinding*, creare un file denominato *clusteridentitybinding.yaml* e usare la definizione YAML seguente:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Per distribuire gli oggetti *AzureIdentity* e *AzureIdentityBinding*, usare `kubectl`:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Dopo aver distribuito gli oggetti *AzureIdentity* e *AzureIdentityBinding*, qualsiasi carico di lavoro con l'etichetta *aadpodidbinding: my-label-value* potrà accedere all'identità gestita del cluster. Aggiungere questa etichetta e ridistribuire tutti i carichi di lavoro in esecuzione in qualsiasi spazio di Dev Spaces. Ad esempio:

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

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemi comuni relativi all'uso di Visual Studio e Visual Studio Code con Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Messaggio di errore analogo a "Mancano configurazioni e strumenti necessari"

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

Provare ad avviare VS Code da un prompt dei comandi in cui la variabile di ambiente PATH sia impostata correttamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Errore "Required tools to build and debug 'projectname' are out of date" (Gli strumenti necessari per la compilazione e il debug di 'nome progetto' non sono aggiornati)

Questo errore viene visualizzato in Visual Studio Code se sono presenti una versione dell'estensione VS Code per Azure Dev Spaces più recente e una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

Provare a scaricare e installare l'ultima versione dell'interfaccia della riga di comando di Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Errore: "Non è stato possibile trovare l'estensione del debugger per tipo: coreclr"

Questo errore può essere visualizzato durante l'esecuzione del debugger di Visual Studio Code. È possibile che nel computer di sviluppo non sia installata un'estensione di VS Code per C#. L'estensione per C# include il supporto per il debug di .NET Core (CoreCLR).

Per risolvere questo problema, installare l'[estensione di Visual Studio Code per C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Messaggio di errore analogo a "Il tipo di debug 'coreclr' configurato non è supportato"

Questo errore può essere visualizzato durante l'esecuzione del debugger di Visual Studio Code. È possibile che nel computer di sviluppo non sia installata l'estensione di VS Code per Azure Dev Spaces.

Per risolvere questo problema, installare l'estensione di VS Code per Azure Dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Messaggio di errore analogo a "Valore '/src' di 'cwd' non valido. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato) o "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[percorso file binario di progetto]' non esiste)

Questo errore può essere visualizzato durante l'esecuzione del debugger di Visual Studio Code. Per impostazione predefinita, l'estensione VS Code usa `src` come directory di lavoro per il progetto nel contenitore. Se è stato aggiornato il `Dockerfile` per specificare una directory di lavoro diversa, è possibile che venga visualizzato questo errore.

Per risolvere questo problema, aggiornare il file `launch.json` nella sottodirectory `.vscode` della cartella del progetto. Modificare la direttiva `configurations->cwd` in modo da puntare alla stessa directory `WORKDIR` definita nel `Dockerfile` del progetto. Potrebbe inoltre essere necessario aggiornare anche la direttiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Messaggio di errore analogo a "Il programma della pipe 'azds' è stato chiuso in modo imprevisto con codice 126"

Questo errore può essere visualizzato durante l'esecuzione del debugger di Visual Studio Code.

Per risolvere questo problema, chiudere e riaprire Visual Studio Code. Riavviare il debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Messaggio di errore analogo a "L'espressione di controllo interna non è riuscita: ENOSPC" visualizzato quando si collega il debug a un'applicazione Node.js

Questo errore si verifica quando il nodo che esegue il pod con l'applicazione Node.js a cui si sta provando a collegarsi con un debugger ha superato il valore *fs.inotify.max_user_watches*. In alcuni casi, [il valore predefinito di *fs.inotify.max_user_watches* può essere troppo piccolo per gestire il collegamento di un debugger direttamente a un pod](https://github.com/Azure/AKS/issues/772).

Una soluzione alternativa temporanea per questo problema consiste nell'aumentare il valore di *fs.inotify.max_user_watches* in ogni nodo del cluster e riavviare il nodo per rendere effettive le modifiche.

## <a name="other-common-issues"></a>Altri problemi comuni

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Messaggio di errore relativo a "azds" non riconosciuto come comando interno o esterno, programma eseguibile o file batch

Questo errore può verificarsi se `azds.exe` non è stato installato o configurato correttamente.

Per risolvere il problema:

1. Controllare il percorso %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI per individuare `azds.exe`. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. Se `azds.exe` non è installato, eseguire il comando seguente:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Errore di autorizzazione "Microsoft.DevSpaces/register/action"

Per gestire Azure Dev Spaces, è necessario l'accesso *Proprietario* o *Collaboratore* nella sottoscrizione di Azure. Se si prova a gestire Dev Spaces senza l'accesso come *Proprietario* o *Collaboratore* della sottoscrizione di Azure associata, è possibile che venga visualizzato un errore di autorizzazione. Ad esempio:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Per risolvere questo problema, usare un account con accesso come *Proprietario* o *Collaboratore* per la sottoscrizione di Azure e registrare manualmente lo spazio dei nomi `Microsoft.DevSpaces`:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>I nuovi pod non vengono avviati

L'inizializzatore di Kubernetes non può applicare PodSpec per i nuovi pod a causa delle modifiche delle autorizzazioni di Controllo degli accessi in base al ruolo apportate al ruolo *cluster-admin* nel cluster. È anche possibile che PodSpec non sia valido per il nuovo pod, ad esempio se l'account del servizio associato al pod non esiste più. Per vedere i pod con lo stato *In sospeso* a causa del problema dell'inizializzatore, usare il comando `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Questo problema può influire sui pod in *tutti gli spazi dei nomi* nel cluster, inclusi quelli in cui Azure Dev Spaces non è abilitato.

Per risolvere questo problema, [aggiornare l'interfaccia della riga di comando di Dev Spaces alla versione più recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e quindi eliminare *azds InitializerConfiguration* dal controller di Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Una volta rimosso *azds InitializerConfiguration* dal controller di Azure Dev Spaces, usare `kubectl delete` per rimuovere tutti i pod con lo stato *In sospeso*. Dopo la rimozione di tutti i pod in sospeso, ridistribuire i pod.

Se i nuovi pod rimangono bloccati nello stato *In sospeso* dopo una ridistribuzione, usare `kubectl delete` per rimuovere tutti i pod con lo stato *In sospeso*. Una volta rimossi tutti i pod in sospeso, eliminare il controller dal cluster e reinstallarlo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Dopo aver reinstallato il controller, ridistribuire i pod.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorizzazioni di Controllo degli accessi in base al ruolo non corrette per la chiamata al controller e alle API di Dev Spaces

L'utente che accede al controller di Azure Dev Spaces deve avere accesso per leggere *kubeconfig* dell'amministratore nel cluster del servizio Azure Kubernetes. Questa autorizzazione è ad esempio disponibile nel [ruolo predefinito di amministratore del cluster del servizio Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). L'utente che accede al controller di Azure Dev Spaces deve avere anche il ruolo di *collaboratore* o *proprietario* di Azure per il controller. Altre informazioni sull'aggiornamento delle autorizzazioni di un utente per un cluster del servizio Azure Kubernetes sono disponibili [qui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Per aggiornare il ruolo di Azure dell'utente per il controller:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare al gruppo di risorse contenente il controller, che in genere è lo stesso del cluster del servizio Azure Kubernetes.
1. Abilitare la casella di controllo *Mostra tipi nascosti*.
1. Fare clic sul controller.
1. Aprire il riquadro *Controllo di accesso (IAM)* .
1. Fare clic sulla scheda *Assegnazioni di ruolo*.
1. Fare clic su *Aggiungi* e quindi su *Aggiungi un'assegnazione di ruolo*.
    * Per *Ruolo* selezionare *Collaboratore* o *Proprietario*.
    * In *Assegna accesso a* selezionare *Utente, gruppo o entità servizio di Azure AD*.
    * Per *Seleziona*, cercare l'utente a cui concedere le autorizzazioni.
1. Fare clic su *Salva*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

È possibile configurare un endpoint URL pubblico per il servizio specificando l'opzione `--enable-ingress` per il comando `azds prep` o selezionando la casella di controllo `Publicly Accessible` in Visual Studio. Il nome DNS pubblico viene registrato automaticamente quando si esegue il servizio in Dev Spaces. Se questo nome DNS non viene registrato, viene visualizzato un errore simile a *Non è possibile visualizzare la pagina* o *Non è possibile raggiungere il sito* nel Web browser quando ci si connette all'URL pubblico.

Per risolvere il problema:

* Verificare lo stato di tutti gli URL associati ai servizi di Dev Spaces:

  ```console
  azds list-uris
  ```

* Se un URL ha lo stato *In sospeso*, Dev Spaces è ancora in attesa del completamento della registrazione DNS. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.
* Se un URL rimane nello stato *In sospeso* per più di 5 minuti, ciò potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico o con il pod del controller in ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. Usare i comandi seguenti per eliminare questi pod e consentire al servizio Azure Kubernetes di ricrearli automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Messaggio di errore analogo a "Errore di connessione upstream o disconnessione/reimpostazione prima delle intestazioni"

Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. Questo errore significa che la porta del contenitore non è disponibile. Il motivo può essere uno dei seguenti:

* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il _Dockerfile_, il grafico Helm e il codice server che consente di aprire una porta.

Per risolvere il problema:

1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Controllare la configurazione della porta negli asset seguenti:
    * **[Grafico Helm](https://docs.helm.sh):** specificato da `service.port` e `deployment.containerPort` in values.yaml con scaffolding del comando `azds prep`.
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Il tipo o il nome dello spazio dei nomi "MyLibrary" non è stato trovato

Un progetto di libreria in uso non è stato trovato. Con Dev Spaces, il contesto di compilazione è a livello di progetto/servizio per impostazione predefinita.  

Per risolvere il problema:

1. Modificare il file `azds.yaml` per impostare il contesto di compilazione a livello di soluzione.
2. Modificare i file `Dockerfile` e `Dockerfile.develop` per fare riferimento ai file di progetto, ad esempio `.csproj`, correttamente rispetto al nuovo contesto di compilazione.
3. Aggiungere `.dockerignore` nella stessa directory del file `.sln`.
4. Aggiornare `.dockerignore` con altre voci se necessario.

È possibile trovare un esempio [qui](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Scalabilità automatica orizzontale dei pod non funzionante in uno spazio di Dev Spaces

Quando si esegue un servizio in uno spazio di Dev Spaces, il pod di tale servizio viene [inserito con contenitori aggiuntivi per la strumentazione](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) e tutti i contenitori di un pod devono avere limiti di risorse e richieste impostati per la scalabilità automatica orizzontale.

Per risolvere questo problema, applicare una richiesta e un limite di risorse ai contenitori di Dev Spaces inseriti. È possibile applicare le richieste e i limiti di risorse per il contenitore inserito (devspaces-proxy) aggiungendo l'annotazione `azds.io/proxy-resources` alla specifica del pod. Il valore deve essere impostato su un oggetto JSON che rappresenta la sezione di risorse della specifica del contenitore per il proxy.

Di seguito è riportato un esempio di annotazione proxy-resources da applicare alla specifica del pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Abilitare Azure Dev Spaces in uno spazio dei nomi esistente con pod in esecuzione

È possibile che siano disponibili un cluster del servizio Azure Kubernetes e uno spazio dei nomi con pod in esecuzione in cui si vuole abilitare Azure Dev Spaces.

Per abilitare Azure Dev Spaces in uno spazio dei nomi esistente in un cluster del servizio Azure Kubernetes, eseguire `use-dev-spaces` e usare `kubectl` per riavviare tutti i pod in tale spazio dei nomi.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Dopo aver riavviato i pod, è possibile iniziare a usare lo spazio dei nomi esistente con Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Abilitare Azure Dev Spaces in un cluster del servizio Azure Kubernetes con traffico in uscita limitato per i nodi del cluster

Per abilitare Azure Dev Spaces in un cluster del servizio Azure Kubernetes per cui il traffico in uscita dai nodi è limitato, è necessario consentire i nomi FQDN seguenti:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Eseguire il pull di immagini Linux Alpine e di altre immagini di Azure Dev Spaces |
| gcr.io | HTTP:443 | Eseguire il pull di immagini Helm/Tiller|
| storage.googleapis.com | HTTP:443 | Eseguire il pull di immagini Helm/Tiller|

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso tutti i nomi di dominio completi e i [servizi di infrastruttura Azure Dev Spaces](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations).

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Errore "Impossibile trovare il cluster \<cluster\> nella sottoscrizione \<subscriptionId\> "

È possibile che venga visualizzato questo messaggio di errore se il file kubeconfig è destinato a un cluster o a una sottoscrizione diversa da quella che si sta provando a usare con gli strumenti lato client di Azure Dev Spaces. Gli strumenti lato client di Azure Dev Spaces replicano il comportamento di *kubectl*, che usa [uno o più file kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) per selezionare e comunicare con il cluster.

Per risolvere il problema:

* Usare `az aks use-dev-spaces -g <resource group name> -n <cluster name>` per aggiornare il contesto corrente. Questo comando abilita anche Azure Dev Spaces nel cluster del servizio Azure Kubernetes, se non è già abilitato. In alternativa, è possibile usare `kubectl config use-context <cluster name>` per aggiornare il contesto corrente.
* Usare `az account show` per visualizzare la sottoscrizione di Azure corrente di destinazione e verificare che sia corretta. È possibile cambiare la sottoscrizione di destinazione usando `az account set`.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Errore durante l'uso di Dev Spaces dopo la rotazione dei certificati del servizio Azure Kubernetes

Dopo la [rotazione dei certificati nel cluster del servizio Azure Kubernetes](../aks/certificate-rotation.md), alcune operazioni, ad esempio `azds space list` e `azds up` non potranno essere completate. È anche necessario aggiornare i certificati sul controller di Azure Dev Spaces dopo la rotazione dei certificati nel cluster.

Per risolvere questo problema, assicurarsi che *kubeconfig* includa i certificati aggiornati usando `az aks get-credentials`, quindi eseguire il comando `azds controller refresh-credentials`. Ad esempio:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
