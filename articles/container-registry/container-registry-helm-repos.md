---
title: Archiviare i grafici Helm
description: Informazioni su come archiviare i grafici Helm per le applicazioni Kubernetes usando repository in Azure Container Registry
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399381"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Eseguire il push e il pull di grafici Helm in un registro contenitori di Azure

Per gestire e distribuire rapidamente applicazioni per Kubernetes, è possibile usare il [gestore pacchetti open source Helm][helm]. Con Helm, i pacchetti dell'applicazione vengono definiti come [grafici](https://helm.sh/docs/topics/charts/), che vengono raccolti e archiviati in un [repository del grafico Helm](https://helm.sh/docs/topics/chart_repository/).

Questo articolo illustra come ospitare i repository di grafici Helm in un registro contenitori di Azure, usando un'installazione Helm 3 o Helm 2. In molti scenari è possibile creare e caricare grafici personalizzati per le applicazioni sviluppate. Per ulteriori informazioni su come creare grafici Helm personalizzati, vedere la guida per [gli sviluppatori del modello di grafico][develop-helm-charts]. È anche possibile archiviare un grafico Helm esistente da un altro repository Helm.

> [!IMPORTANT]
> Il supporto per i grafici Helm in Azure Container Registry è attualmente in fase di anteprima. Le anteprime vengono rese disponibili per l'utente nella condizione di accettare le condizioni per l' [utilizzo][terms-of-use]aggiuntive. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="helm-3-or-helm-2"></a>Helm 3 o Helm 2?

Per archiviare, gestire e installare i grafici Helm, è possibile usare un client Helm e l'interfaccia della riga di comando di Helm. Le versioni principali del client Helm includono Helm 3 e Helm 2. Helm 3 supporta un nuovo formato grafico e non installa più il componente lato server. Per informazioni dettagliate sulle differenze tra le versioni, vedere le [domande frequenti sulla versione](https://helm.sh/docs/faq/). Se in precedenza sono stati distribuiti i grafici Helm 2, vedere la pagina relativa [alla migrazione di Helm V2 a V3](https://helm.sh/docs/topics/v2_v3_migration/).

È possibile usare Helm 3 o Helm 2 per ospitare i grafici Helm in Azure Container Registry, con flussi di lavoro specifici per ogni versione:

* [Client Helm 3](#use-the-helm-3-client) : usare `helm chart` i comandi nell'interfaccia della riga di comando di Helm per gestire i grafici nel registro di sistema come [artefatti OCI](container-registry-image-formats.md#oci-artifacts)
* [Client Helm 2](#use-the-helm-2-client) : usare i comandi [AZ ACR Helm][az-acr-helm] nell'interfaccia della riga di comando di Azure per aggiungere e gestire il registro contenitori come repository del grafico Helm

### <a name="additional-information"></a>Informazioni aggiuntive

* Per la maggior parte degli scenari, è consigliabile usare il flusso di `helm chart` lavoro Helm 3 con comandi nativi per gestire i grafici come elementi OCI.
* È possibile usare i comandi dell'interfaccia della riga di comando e il flusso di lavoro di [AZ ACR Helm][az-acr-helm] Azure con il client e i grafici Helm 3. Tuttavia, alcuni comandi, ad `az acr helm list` esempio, non sono compatibili con i grafici Helm 3.
* A partire da Helm 3, i comandi [AZ ACR Helm][az-acr-helm] sono supportati principalmente per la compatibilità con il client Helm 2 e il formato grafico. Lo sviluppo futuro di questi comandi non è attualmente pianificato.

## <a name="use-the-helm-3-client"></a>Usare il client Helm 3

### <a name="prerequisites"></a>Prerequisiti

- **Un registro contenitori di Azure** nella sottoscrizione di Azure. Se necessario, creare un registro usando il [portale di Azure](container-registry-get-started-portal.md) o l' [interfaccia](container-registry-get-started-azure-cli.md)della riga di comando di Azure.
- **Versione client Helm 3.1.0 o successiva** : eseguire `helm version` per trovare la versione corrente. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install] (Installazione di Helm).
- **Un cluster Kubernetes** in cui viene installato un grafico Helm. Se necessario, creare un [cluster del servizio Azure Kubernetes][aks-quickstart]. 
- **Versione 2.0.71 o successiva dell'interfaccia** della riga `az --version` di comando di Azure: eseguire per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Flusso di lavoro di alto livello

Con **Helm 3** :

* È possibile creare uno o più repository Helm in un registro contenitori di Azure
* Archiviare i grafici Helm 3 in un registro di sistema come [artefatti OCI](container-registry-image-formats.md#oci-artifacts). Attualmente, il supporto Helm 3 per OCI è *sperimentale*.
* Eseguire l'autenticazione con il registro usando `helm registry login` il comando.
* Usare `helm chart` i comandi nell'interfaccia della riga di comando di Helm per eseguire il push, il pull e la gestione dei grafici Helm in un registro
* Usare `helm install` per installare i grafici in un cluster Kubernetes da una cache del repository locale.

Per esempi, vedere le sezioni seguenti.

### <a name="enable-oci-support"></a>Abilita supporto OCI

Impostare la variabile di ambiente seguente per abilitare il supporto OCI nel client Helm 3. Attualmente, questo supporto è sperimentale. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Creazione di un grafico di esempio

Creare un grafico di test usando i comandi seguenti:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Come esempio di base, sostituire la directory con `templates` la cartella ed eliminare prima il contenuto:

```console
cd hello-world/templates
rm -rf *
```

Nella `templates` cartella creare un file denominato `configmap.yaml` con il contenuto seguente:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Per altre informazioni sulla creazione e sull'esecuzione di questo esempio, vedere [Introduzione](https://helm.sh/docs/chart_template_guide/getting_started/) nella documentazione Helm.

### <a name="save-chart-to-local-registry-cache"></a>Salva il grafico nella cache del registro di sistema locale

Modificare la `hello-world` directory nella sottodirectory. Eseguire `helm chart save` quindi per salvare una copia del grafico localmente e creare anche un alias con il nome completo del registro di sistema (tutti minuscoli) e il repository e il tag di destinazione. 

Nell'esempio seguente il nome del registro di sistema è *mycontainerregistry*, il repository di destinazione è *Hello-World*e il tag del grafico di destinazione è *V1*, ma sostituisce i valori per l'ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart list` per confermare che i grafici sono stati salvati nella cache del registro di sistema locale. L'output è simile a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Eseguire l'autenticazione con il registro di sistema

Eseguire il `helm registry login` comando nell'interfaccia della riga di comando di Helm 3 per eseguire l' [autenticazione con il registro](container-registry-authentication.md) di sistema usando le credenziali appropriate per lo scenario.

Ad esempio, creare un' [entità servizio Azure Active Directory con le autorizzazioni pull e push](container-registry-auth-service-principal.md#create-a-service-principal) (ruolo AcrPush) nel registro di sistema. Fornire quindi le credenziali dell'entità servizio `helm registry login`a. Nell'esempio seguente viene fornita la password tramite una variabile di ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Eseguire il push del grafico in Azure Container Registry

Eseguire il `helm chart push` comando nell'interfaccia della riga di comando di Helm 3 per eseguire il push del grafico nel repository di destinazione completo:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Dopo un push riuscito, l'output è simile al seguente:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

Come per le immagini archiviate in un registro contenitori di Azure, è possibile usare i comandi [AZ ACR repository][az-acr-repository] per visualizzare i repository che ospitano i grafici e i tag e i manifesti dei grafici. 

Eseguire ad esempio [AZ ACR repository Show][az-acr-repository-show] per visualizzare le proprietà del repository creato nel passaggio precedente:

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

Eseguire il comando [AZ ACR repository Show-manifests][az-acr-repository-show-manifests] per visualizzare i dettagli del grafico archiviato nel repository. Ad esempio:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

L'output, abbreviato in questo esempio, `configMediaType` Mostra `application/vnd.cncf.helm.config.v1+json`il valore di:

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

### <a name="pull-chart-to-local-cache"></a>Estrai il grafico nella cache locale

Per installare un grafico Helm in Kubernetes, il grafico deve trovarsi nella cache locale. In questo esempio, eseguire `helm chart remove` prima di tutto per rimuovere il grafico locale `mycontainerregistry.azurecr.io/helm/hello-world:v1`esistente denominato:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart pull` per scaricare il grafico dal registro contenitori di Azure nella cache locale:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Esporta grafico Helm

Per lavorare ulteriormente con il grafico, esportarlo in una directory locale `helm chart export`usando. Ad esempio, esportare il grafico di cui è stato `install` effettuato il pull nella directory:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Per visualizzare le informazioni relative al grafico esportato nel repository, `helm show chart` eseguire il comando nella directory in cui è stato esportato il grafico.

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

### <a name="install-helm-chart"></a>Installare il grafico Helm

Eseguire `helm install` per installare il grafico Helm di cui è stato effettuato il pull nella cache locale ed esportato. Specificare un nome di versione, ad esempio *myhelmtest*, oppure `--generate-name` passare il parametro. Ad esempio:

```console
helm install myhelmtest ./hello-world
```

L'output dopo l'installazione riuscita del grafico è simile al seguente:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Per verificare l'installazione, eseguire il `helm get manifest` comando. Il comando restituisce i dati YAML nel file `configmap.yaml` modello.

Eseguire `helm uninstall` per disinstallare la versione del grafico nel cluster:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminare un grafico Helm dal repository

Per eliminare un grafico dal repository, usare il comando [AZ ACR repository Delete][az-acr-repository-delete] . Eseguire il comando seguente e confermare l'operazione quando richiesto:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Usare il client Helm 2

### <a name="prerequisites"></a>Prerequisiti

- **Un registro contenitori di Azure** nella sottoscrizione di Azure. Se necessario, creare un registro usando il [portale di Azure](container-registry-get-started-portal.md) o l' [interfaccia](container-registry-get-started-azure-cli.md)della riga di comando di Azure.
- **Versione del client Helm 2.11.0 (non una versione finale candidata) o versione successiva**: eseguire `helm version` per trovare la versione corrente. È anche necessario un server Helm (Tiller) inizializzato in un cluster Kubernetes. Se necessario, creare un [cluster del servizio Azure Kubernetes][aks-quickstart]. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install-v2] (Installazione di Helm).
- **Interfaccia della riga di comando di Azure versione 2.0.46 o successiva** - Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Flusso di lavoro di alto livello

Con **Helm 2** è possibile:

* Configurare il registro contenitori di Azure come *singolo* repository del grafico Helm. Azure Container Registry gestisce la definizione dell'indice quando si aggiungono e si rimuovono i grafici nel repository.
* Eseguire l'autenticazione con il registro contenitori di Azure tramite l'interfaccia della riga di comando di Azure, che quindi aggiorna automaticamente il client Helm con l'URI e le credenziali del registro di sistema. Non è necessario specificare manualmente le informazioni del registro di sistema, quindi le credenziali non vengono esposte nella cronologia dei comandi.
* Usare i comandi [AZ ACR Helm][az-acr-helm] nell'interfaccia della riga di comando di Azure per aggiungere il registro contenitori di Azure come repository del grafico Helm e per eseguire il push e la gestione dei grafici. Questi comandi dell'interfaccia della riga di comando di Azure avvolgono i comandi client Helm 2
* Aggiungere il repository Chart nel registro contenitori di Azure all'indice del repository Helm locale, supportando la ricerca dei grafici.
* Usare `helm install` per installare i grafici in un cluster Kubernetes da una cache del repository locale.

Per esempi, vedere le sezioni seguenti.

### <a name="add-repository-to-helm-client"></a>Aggiungere il repository al client Helm

Aggiungere il repository di Azure Container Registry Helm Chart al client Helm usando il comando [AZ ACR Helm repo add][az-acr-helm-repo-add] . Tale comando ottiene un tipo di token di autenticazione per il Registro Azure Container che viene usato dal client Helm. Il token di autenticazione è valido per 3 ore. In modo analogo a `docker login`, è possibile eseguire questo comando nelle sessioni future dell'interfaccia della riga di comando per autenticare il client Helm con il repository di grafici di Registro Azure Container:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Aggiungere un grafico di esempio al repository

Prima di tutto, creare una directory locale in *~/ACR-Helm*, quindi scaricare il grafico *stabile/Wordpress* esistente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digitare `ls` per elencare il grafico scaricato e prendere nota della versione di Wordpress inclusa nel nome file. Il comando `helm fetch stable/wordpress` non ha specificato una versione particolare e pertanto è stata recuperata la versione *più recente*. Nell'output di esempio seguente, il grafico Wordpress è la versione *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Eseguire il push del grafico nel repository del grafico Helm in Azure Container Registry usando il comando [AZ ACR Helm push][az-acr-helm-push] nell'interfaccia della riga di comando di Azure. Specificare il nome del grafico Helm scaricato nel passaggio precedente, ad esempio *Wordpress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Dopo alcuni istanti, l'interfaccia della riga di comando di Azure segnala che il grafico è salvato, come illustrato nell'output di esempio seguente:

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

Con un grafico archiviato nel repository e l'indice aggiornato disponibile in locale, è possibile usare i normali comandi del client Helm per eseguire una ricerca o un'installazione. Per visualizzare tutti i grafici nel repository, usare il `helm search` comando, specificando il proprio nome di container Registry di Azure:

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

Per visualizzare le informazioni relative a un grafico specifico nel repository, è possibile usare `helm inspect` il comando.

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

È anche possibile visualizzare le informazioni per un grafico con il comando [az acr helm show][az-acr-helm-show] dell'interfaccia della riga di comando di Azure. Anche in questo caso, per impostazione predefinita viene restituita la versione del grafico *più recente*. È possibile aggiungere `--version` per elencare una versione specifica di un grafico, ad esempio *8.1.0*:

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

Quando l'installazione continua, seguire le istruzioni nell'output del comando per visualizzare gli URL e le credenziali di WorPress. È anche possibile eseguire il `kubectl get pods` comando per visualizzare le risorse Kubernetes distribuite tramite il grafico Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminare un grafico Helm dal repository

Per eliminare un grafico dal repository, usare il comando [az acr helm delete][az-acr-helm-delete]. Specificare il nome del grafico, ad esempio *Wordpress*, e la versione da eliminare, ad esempio *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se si intende eliminare tutte le versioni del grafico denominato, omettere il parametro `--version`.

Il grafico continuerà a essere restituito quando si `helm search`esegue. Anche in questo caso il client Helm non aggiorna automaticamente l'elenco dei grafici disponibili in un repository. Per aggiornare l'indice del repository del client Helm, usare di nuovo il comando [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come creare e distribuire grafici Helm, vedere [Developing Helm charts][develop-helm-charts] (Distribuzione di grafici Helm).
* Altre informazioni sull'installazione di applicazioni con Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* I grafici Helm possono essere usati come parte del processo di compilazione del contenitore. Per altre informazioni, vedere [usare le attività container Registry di Azure][acr-tasks].

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
