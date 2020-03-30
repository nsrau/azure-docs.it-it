---
title: Memorizzare i grafici Helm
description: Informazioni su come archiviare i grafici Helm per le applicazioni Kubernetes usando i repository nel Registro di sistema dei contenitori di Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 6304486ac493e235ed74f26ab4be4f843ef52513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131488"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Eseguire il push e il pull dei grafici Helm in un registro dei contenitori di AzurePush and pull Helm charts to an Azure container registry

Per gestire e distribuire rapidamente applicazioni per Kubernetes, è possibile usare il [gestore pacchetti open source Helm][helm]. Con Helm, i pacchetti di applicazioni sono definiti come [grafici](https://helm.sh/docs/topics/charts/), che vengono raccolti e memorizzati in un [repository di grafico Helm](https://helm.sh/docs/topics/chart_repository/).

Questo articolo illustra come ospitare i repository di gruppi Di Helm in un registro contenitori di Azure usando un'installazione di Helm 3 o Helm 2.This article shows you how to host Helm charts repositories in an Azure container registry, using either a Helm 3 or Helm 2 installation. In molti scenari, è necessario compilare e caricare i propri grafici per le applicazioni sviluppate. Per ulteriori informazioni su come creare i propri grafici Helm, vedere la [Guida per gli sviluppatori di modelli][develop-helm-charts]di grafico . È inoltre possibile memorizzare un grafico Helm esistente da un altro repository Helm.

> [!IMPORTANT]
> Il supporto per i grafici Helm nel Registro di sistema del contenitore di Azure è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le condizioni supplementari [per l'utilizzo.][terms-of-use] Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="helm-3-or-helm-2"></a>Helm 3 o Helm 2?

Per archiviare, gestire e installare i grafici Helm, si utilizza un client Helm e l'interfaccia della riga di comando Helm. Le principali versioni del client Helm includono Helm 3 e Helm 2. Helm 3 supporta un nuovo formato grafico e non installa più il componente lato server Tiller. Per informazioni dettagliate sulle differenze di versione, vedere la [versione Domande frequenti](https://helm.sh/docs/faq/). Se in precedenza sono stati distribuiti grafici Helm 2, vedere Migrazione di [Helm v2 a v3.](https://helm.sh/docs/topics/v2_v3_migration/)

È possibile usare Helm 3 o Helm 2 per ospitare i grafici Helm nel Registro di sistema del contenitore di Azure, con flussi di lavoro specifici per ogni versione:You can use either Helm 3 or Helm 2 to host Helm charts in Azure Container Registry, with workflows specific to each version:

* [Client Helm](#use-the-helm-3-client) 3 `helm chart` - utilizzare i comandi nell'interfaccia della riga di comando di Helm per gestire i grafici nel Registro di sistema come [artefatti OCI](container-registry-image-formats.md#oci-artifacts)
* [Client Helm 2-](#use-the-helm-2-client) usare i comandi [az acr helm][az-acr-helm] nell'interfaccia della riga di comando di Azure per aggiungere e gestire il registro contenitori come repository di un grafico Helm

### <a name="additional-information"></a>Informazioni aggiuntive

* Per la maggior parte degli scenari, `helm chart` è consigliabile usare il flusso di lavoro Helm 3 con comandi nativi per gestire i grafici come elementi OCI.
* È possibile usare i comandi e il flusso di lavoro di [Azure con][az-acr-helm] il client e i grafici di Helm 3. Tuttavia, alcuni `az acr helm list` comandi come non sono compatibili con i grafici Helm 3.
* A partire da Helm 3, i comandi [az acr helm][az-acr-helm] sono supportati principalmente per la compatibilità con il formato del client e del grafico Helm 2. Lo sviluppo futuro di questi comandi non è attualmente pianificato.

## <a name="use-the-helm-3-client"></a>Utilizzare il client Helm 3

### <a name="prerequisites"></a>Prerequisiti

- **Un registro contenitore** di Azure nella sottoscrizione di Azure.An Azure container registry in your Azure subscription. Se necessario, creare un Registro di sistema usando il portale di Azure o l'interfaccia della riga di comando di [Azure.If](container-registry-get-started-azure-cli.md)needed, create a registry using the [Azure portal](container-registry-get-started-portal.md) or the Azure CLI .
- **Helm client versione 3.1.0** `helm version` o successiva - Esegui per trovare la versione corrente. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install] (Installazione di Helm).
- **Un cluster Kubernetes** in cui si installerà un grafico Helm. Se necessario, creare un cluster di servizi [Azure Kubernetes][aks-quickstart]. 
- Interfaccia della riga di comando di **Azure versione 2.0.71 o successiva** - Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

### <a name="high-level-workflow"></a>Flusso di lavoro di alto livello

Con **Helm 3** si:

* È possibile creare uno o più repository Helm in un registro contenitori di AzureCan create one or more Helm repositories in an Azure container registry
* Archiviare i grafici Helm 3 in un Registro di sistema come [elementi OCI](container-registry-image-formats.md#oci-artifacts). Attualmente, il supporto Di Helm 3 per OCI è *sperimentale.*
* Eseguire l'autenticazione `helm registry login` con il Registro di sistema utilizzando il comando .
* Usare `helm chart` i comandi nell'interfaccia della riga di comando di Helm per eseguire il push, estrarre e gestire i grafici Helm in un Registro di sistema
* Utilizzare `helm install` per installare grafici in un cluster Kubernetes da una cache del repository locale.

Per alcuni esempi, vedere le sezioni seguenti.

### <a name="enable-oci-support"></a>Abilitare il supporto OCI

Impostare la seguente variabile di ambiente per abilitare il supporto OCI nel client Helm 3. Attualmente, questo supporto è sperimentale. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Creare un grafico di esempioCreate a sample chart

Creare un grafico di test utilizzando i comandi seguenti:Create a test chart using the following commands:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Come esempio di base, `templates` passare alla cartella ed eliminare prima il contenuto:

```console
rm -rf *
```

Nella `templates` cartella, creare un `configmap.yaml` file chiamato con il seguente contenuto:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
```

Per altre informazioni sulla creazione e l'esecuzione di questo esempio, vedere [Guida introduttiva](https://helm.sh/docs/chart_template_guide/getting_started/) in Helm Docs.For more about creating and running this example, see Getting Started in the Helm Docs.

### <a name="save-chart-to-local-registry-cache"></a>Salva grafico nella cache del Registro di sistema locale

Passare alla `hello-world` sottodirectory. Quindi, `helm chart save` eseguire per salvare una copia del grafico in locale e creare anche un alias con il nome completo del Registro di sistema (tutte minuscole) e il repository di destinazione e tag. 

Nell'esempio seguente, il nome del Registro di sistema è *mycontainerregistry*, il repository di destinazione è *hello-world*e il tag del grafico di destinazione è *v1*, ma sostituisci i valori per l'ambiente in uso:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart list` per confermare di aver salvato i grafici nella cache del Registro di sistema locale. L'output è simile a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Eseguire l'autenticazione con il Registro di sistemaAuthenticate with the

Eseguire `helm registry login` il comando nell'interfaccia della riga di comando Helm 3 per [eseguire l'autenticazione con il Registro](container-registry-authentication.md) di sistema utilizzando le credenziali appropriate per lo scenario.

Ad esempio, creare un'entità servizio di Azure Active Directory [con autorizzazioni pull e push](container-registry-auth-service-principal.md#create-a-service-principal) (ruolo AcrPush) nel Registro di sistema. Fornire quindi le credenziali `helm registry login`dell'entità servizio a . L'esempio seguente fornisce la password utilizzando una variabile di ambiente:The following example supplies the password using an environment variable:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Push chart to Azure Container Registry

Eseguire `helm chart push` il comando nell'interfaccia della riga di comando Helm 3 per eseguire il push del grafico nel repository di destinazione completo:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Dopo un push riuscito, l'output è simile al:After a successful push, output is similar to:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

Come per le immagini archiviate in un registro dei contenitori di Azure, è possibile usare i comandi del [repository az acr][az-acr-repository] per mostrare i repository che ospitano i grafici e i tag grafici e i manifesti. 

Ad esempio, eseguire [az acr repository show][az-acr-repository-show] per visualizzare le proprietà del repository creato nel passaggio precedente:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

L'output è simile a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Eseguire il comando [az acr repository show-manifests][az-acr-repository-show-manifests] per visualizzare i dettagli del grafico archiviato nel repository. Ad esempio:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

L'output, abbreviato in `configMediaType` questo `application/vnd.cncf.helm.config.v1+json`esempio, mostra un di :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Grafico pull nella cache locale

Per installare un grafico Helm in Kubernetes, il grafico deve trovarsi nella cache locale. In questo esempio, `helm chart remove` prima esecuzione per `mycontainerregistry.azurecr.io/helm/hello-world:v1`rimuovere il grafico locale esistente denominato :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart pull` il download del grafico dal registro contenitori di Azure nella cache locale:Run to download the chart from the Azure container registry to your local cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Esporta grafico Helm

Per lavorare ulteriormente con il grafico, `helm chart export`esportarlo in una directory locale utilizzando . Ad esempio, esportare il `install` grafico estratto nella directory:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Per visualizzare le informazioni relative al grafico esportato nel repository, eseguire il `helm show chart` comando nella directory in cui è stato esportato il grafico.

```console
cd install
helm show chart hello-world
```

Helm restituisce informazioni dettagliate sulla versione più recente del grafico, come illustrato nell'output di esempio seguente:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Installare Il grafico Helm

Esegui `helm install` per installare il grafico Helm che hai tirato nella cache locale ed esportato. Specificare un nome di versione, ad `--generate-name` esempio *myhelmtest,* oppure passare il parametro . Ad esempio:

```console
helm install myhelmtest ./hello-world
```

L'output dopo la corretta installazione del grafico è simile al seguente:Output after successful chart installation is similar to:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Per verificare l'installazione, eseguire il `helm get manifest` comando. Il comando restituisce i dati `configmap.yaml` YAML nel file modello.

Per `helm uninstall` disinstallare la versione del grafico nel cluster, eseguire questa procedura:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminare un grafico Helm dal repository

Per eliminare un grafico dal repository, utilizzare il comando [az acr repository delete.][az-acr-repository-delete] Eseguire il comando seguente e confermare l'operazione quando richiesto:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Utilizzare il client Helm 2

### <a name="prerequisites"></a>Prerequisiti

- **Un registro contenitore** di Azure nella sottoscrizione di Azure.An Azure container registry in your Azure subscription. Se necessario, creare un Registro di sistema usando il portale di Azure o l'interfaccia della riga di comando di [Azure.If](container-registry-get-started-azure-cli.md)needed, create a registry using the [Azure portal](container-registry-get-started-portal.md) or the Azure CLI .
- **Versione del client Helm 2.11.0 (non una versione finale candidata) o versione successiva**: eseguire `helm version` per trovare la versione corrente. È anche necessario un server Helm (Tiller) inizializzato in un cluster Kubernetes. Se necessario, creare un cluster di servizi [Azure Kubernetes][aks-quickstart]. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install-v2] (Installazione di Helm).
- **Interfaccia della riga di comando di Azure versione 2.0.46 o successiva** - Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

### <a name="high-level-workflow"></a>Flusso di lavoro di alto livello

Con **Helm 2** tu:

* Configurare il registro contenitori di Azure come *un singolo* repository di Helm chart.Configure your Azure container registry as a single Helm chart repository. Il Registro di sistema contenitori di Azure gestisce la definizione dell'indice quando si aggiungono e rimuovono grafici nel repository.
* Eseguire l'autenticazione con il Registro di sistema del contenitore di Azure tramite l'interfaccia della riga di comando di Azure, che quindi aggiorna automaticamente il client Helm con l'URI del Registro di sistema e le credenziali. Non è necessario specificare manualmente queste informazioni del Registro di sistema, in modo che le credenziali non vengono esposte nella cronologia dei comandi.
* Usare i comandi [az acr helm][az-acr-helm] nell'interfaccia della riga di comando di Azure per aggiungere il registro dei contenitori di Azure come repository di grafico Helm e per eseguire il push e la gestione dei grafici. Questi comandi dell'interfaccia della riga di comando di Azure eseguono il wrapping dei comandi client Helm 2.These Azure CLI commands wrap Helm 2 client commands.
* Aggiungere il repository dei grafici nel registro dei contenitori di Azure all'indice del repository Helm locale, supportando la ricerca nei grafici.
* Utilizzare `helm install` per installare grafici in un cluster Kubernetes da una cache del repository locale.

Per alcuni esempi, vedere le sezioni seguenti.

### <a name="add-repository-to-helm-client"></a>Aggiungi repository al client Helm

Aggiungere il repository del grafico Helm del Registro di sistema del contenitore di Azure al client Helm usando il comando [az acr helm repo add.][az-acr-helm-repo-add] Tale comando ottiene un tipo di token di autenticazione per il Registro Azure Container che viene usato dal client Helm. Il token di autenticazione è valido per 3 ore. In modo analogo a `docker login`, è possibile eseguire questo comando nelle sessioni future dell'interfaccia della riga di comando per autenticare il client Helm con il repository di grafici di Registro Azure Container:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Aggiungere un grafico di esempio al repositoryAdd a sample chart to the repository

In primo luogo, creare una directory locale *al*seguente, quindi scaricare la *tabella stable/wordpress* esistente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digitare `ls` per elencare il grafico scaricato e prendere nota della versione di Wordpress inclusa nel nome del file. Il comando `helm fetch stable/wordpress` non ha specificato una versione particolare e pertanto è stata recuperata la versione *più recente*. Nell'output di esempio seguente, il grafico di Wordpress è la versione *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Eseguire il push del grafico nel repository del grafico Helm nel Registro di sistema del contenitore di Azure usando il comando [az acr helm push][az-acr-helm-push] nell'interfaccia della riga di comando di Azure.Push the chart to your Helm chart repository in Azure Container Registry using the az acr helm push command in the Azure CLI. Specificare il nome del grafico Helm scaricato nel passaggio precedente, ad esempio *wordpress-8.1.0.tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Dopo alcuni istanti, l'interfaccia della riga di comando di Azure segnala che il grafico viene salvato, come illustrato nell'output di esempio seguente:After a few moments, the Azure CLI reports that your chart is saved, as shown in the following example output:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

Per usare il grafico caricato nel passaggio precedente, l'indice del repository Helm locale deve essere aggiornato. È possibile reindicizzare i repository nel client Helm o usare l'interfaccia della riga di comando di Azure per aggiornare l'indice del repository. Ogni volta che si aggiunge un grafico al repository, è necessario completare questo passaggio:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Con un grafico archiviato nel repository e l'indice aggiornato disponibile in locale, è possibile usare i normali comandi del client Helm per eseguire una ricerca o un'installazione. Per visualizzare tutti i grafici `helm search` nel repository, usare il comando, specificando il proprio nome del Registro di sistema del contenitore di Azure:To see all the charts in your repository, use the command, providing your own Azure Container Registry name:

```console
helm search mycontainerregistry
```

Il grafico Wordpress inserito nel passaggio precedente è elencato, come illustrato nell'output di esempio seguente:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

È anche possibile elencare i grafici tramite l'interfaccia della riga di comando di Azure usando [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Visualizzare le informazioni per un grafico Helm

Per visualizzare le informazioni per un grafico specifico `helm inspect` nel repository, è possibile utilizzare il comando.

```console
helm inspect mycontainerregistry/wordpress
```

Quando non viene specificato alcun numero di versione, viene usata la versione *più recente*. Helm restituisce informazioni dettagliate relative al grafico, come illustrato nell'output di esempio sintetico seguente:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

È anche possibile visualizzare le informazioni per un grafico con il comando [az acr helm show][az-acr-helm-show] dell'interfaccia della riga di comando di Azure. Anche in questo caso, per impostazione predefinita viene restituita la versione del grafico *più recente*. È possibile `--version` aggiungere all'elenco una versione specifica di un grafico, ad esempio *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Installare un grafico Helm dal repository

Il grafico Helm nel repository viene installato specificando il nome del repository e il nome del grafico. Usare il client Helm per installare il grafico Wordpress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Se si esegue il push nel repository di grafici Helm di Registro Azure Container e in seguito si torna in una nuova sessione dell'interfaccia della riga di comando,per il client Helm locale è necessario un token di autenticazione aggiornato. Per ottenere un nuovo token di autenticazione, usare il comando [az acr helm repo add][az-acr-helm-repo-add].

Durante il processo di installazione vengono completati i passaggi seguenti:

- Il client Helm cerca l'indice del repository locale.
- Il grafico corrispondente viene scaricato dal repository di Registro Azure Container.
- Il grafico viene distribuito usando Tiller nel cluster Kubernetes.

Mentre l'installazione procede, seguire le istruzioni nell'output del comando per visualizzare gli URL e le credenziali WorPress. È inoltre possibile `kubectl get pods` eseguire il comando per visualizzare le risorse Kubernetes distribuite tramite il grafico Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminare un grafico Helm dal repository

Per eliminare un grafico dal repository, usare il comando [az acr helm delete][az-acr-helm-delete]. Specificare il nome del grafico, ad esempio *wordpress*, e la versione da eliminare, ad esempio *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se si intende eliminare tutte le versioni del grafico denominato, omettere il parametro `--version`.

Il grafico continua a essere `helm search`restituito quando si esegue . Anche in questo caso il client Helm non aggiorna automaticamente l'elenco dei grafici disponibili in un repository. Per aggiornare l'indice del repository del client Helm, usare di nuovo il comando [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come creare e distribuire grafici Helm, vedere [Developing Helm charts][develop-helm-charts] (Distribuzione di grafici Helm).
* Altre informazioni sull'installazione di applicazioni con Helm nel [servizio Azure Kubernetes (AKS)](../aks/kubernetes-helm.md).
* I grafici Helm possono essere usati come parte del processo di compilazione del contenitore. Per altre informazioni, vedere Usare le attività del Registro di sistema del contenitore di [Azure.For more][acr-tasks]information, see Use Azure Container Registry Tasks .

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
