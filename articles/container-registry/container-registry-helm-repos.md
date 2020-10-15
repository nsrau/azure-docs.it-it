---
title: Archiviare i grafici Helm
description: Informazioni su come archiviare i grafici Helm per le applicazioni Kubernetes usando repository in Azure Container Registry
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537868"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Eseguire il push e il pull di grafici Helm in un registro contenitori di Azure

Per gestire e distribuire rapidamente applicazioni per Kubernetes, è possibile usare il [gestore pacchetti open source Helm][helm]. Con Helm, i pacchetti dell'applicazione vengono definiti come [grafici](https://helm.sh/docs/topics/charts/), che vengono raccolti e archiviati in un [repository del grafico Helm](https://helm.sh/docs/topics/chart_repository/).

Questo articolo illustra come ospitare i repository di grafici Helm in un registro contenitori di Azure, usando i comandi Helm 3. In molti scenari è possibile creare e caricare grafici personalizzati per le applicazioni sviluppate. Per ulteriori informazioni su come creare grafici Helm personalizzati, vedere la guida per [gli sviluppatori del modello di grafico][develop-helm-charts]. È anche possibile archiviare un grafico Helm esistente da un altro repository Helm.

> [!IMPORTANT]
> Il supporto per i grafici Helm in Azure Container Registry è attualmente in fase di anteprima. Le anteprime vengono rese disponibili per l'utente nella condizione di accettare le condizioni per l' [utilizzo][terms-of-use]aggiuntive. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="helm-3-or-helm-2"></a>Helm 3 o Helm 2?

Per archiviare, gestire e installare i grafici Helm, è possibile usare un client Helm e l'interfaccia della riga di comando di Helm. Le versioni principali del client Helm includono Helm 3 e Helm 2. Per informazioni dettagliate sulle differenze tra le versioni, vedere le [domande frequenti sulla versione](https://helm.sh/docs/faq/). 

Helm 3 deve essere usato per ospitare i grafici Helm in Azure Container Registry. Con Helm 3 è possibile:

* È possibile creare uno o più repository Helm in un registro contenitori di Azure
* Archiviare i grafici Helm 3 in un registro di sistema come [artefatti OCI](container-registry-image-formats.md#oci-artifacts). Attualmente, il supporto Helm 3 per OCI è *sperimentale*.
* Eseguire l'autenticazione con il registro usando il `helm registry login` comando.
* Usare `helm chart` i comandi nell'interfaccia della riga di comando di Helm per eseguire il push, il pull e la gestione dei grafici Helm in un registro
* Usare `helm install` per installare i grafici in un cluster Kubernetes da una cache del repository locale.
> [!NOTE]
> A partire da Helm 3, i comandi [AZ ACR Helm][az-acr-helm] per l'uso con il client Helm 2 saranno deprecati. Vedere la [Roadmap sul prodotto](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga). Se in precedenza sono stati distribuiti i grafici Helm 2, vedere la pagina relativa [alla migrazione di Helm V2 a V3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario in questo articolo sono necessarie le risorse seguenti:

- **Un registro contenitori di Azure** nella sottoscrizione di Azure. Se necessario, creare un registro usando il [portale di Azure](container-registry-get-started-portal.md) o l' [interfaccia](container-registry-get-started-azure-cli.md)della riga di comando di Azure.
- **Versione client Helm 3.1.0 o successiva** : eseguire `helm version` per trovare la versione corrente. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install] (Installazione di Helm).
- **Un cluster Kubernetes** in cui viene installato un grafico Helm. Se necessario, creare un [cluster del servizio Azure Kubernetes][aks-quickstart]. 
- **Versione 2.0.71 o successiva dell'interfaccia** della riga di comando di Azure: eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Abilita supporto OCI

Usare il `helm version` comando per verificare di aver installato Helm 3:

```console
helm version
```

Impostare la variabile di ambiente seguente per abilitare il supporto OCI nel client Helm 3. Attualmente, questo supporto è sperimentale. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Creazione di un grafico di esempio

Creare un grafico di test usando i comandi seguenti:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Come esempio di base, sostituire la directory con la `templates` cartella ed eliminare prima il contenuto:

```console
cd hello-world/templates
rm -rf *
```

Nella `templates` cartella creare un file denominato eseguendo `configmap.yaml` il comando seguente:

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

## <a name="save-chart-to-local-registry-cache"></a>Salva il grafico nella cache del registro di sistema locale

Modificare la directory nella `hello-world` sottodirectory. Eseguire quindi `helm chart save` per salvare una copia del grafico localmente e creare anche un alias con il nome completo del registro di sistema (tutti minuscoli) e il repository e il tag di destinazione. 

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

## <a name="authenticate-with-the-registry"></a>Eseguire l'autenticazione con il registro di sistema

Eseguire il `helm registry login` comando nell'interfaccia della riga di comando di Helm 3 per eseguire l' [autenticazione con il registro](container-registry-authentication.md) di sistema usando le credenziali appropriate per lo scenario.

Ad esempio, creare un' [entità servizio Azure Active Directory con le autorizzazioni pull e push](container-registry-auth-service-principal.md#create-a-service-principal) (ruolo AcrPush) nel registro di sistema. Fornire quindi le credenziali dell'entità servizio a `helm registry login` . Nell'esempio seguente viene fornita la password tramite una variabile di ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Esegui il push del grafico nel registro di sistema

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

## <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

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

L'output, abbreviato in questo esempio, Mostra il valore `configMediaType` di `application/vnd.cncf.helm.config.v1+json` :

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

## <a name="pull-chart-to-local-cache"></a>Estrai il grafico nella cache locale

Per installare un grafico Helm in Kubernetes, il grafico deve trovarsi nella cache locale. In questo esempio, eseguire prima `helm chart remove` di tutto per rimuovere il grafico locale esistente denominato `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart pull` per scaricare il grafico dal registro contenitori di Azure nella cache locale:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Esporta grafico Helm

Per lavorare ulteriormente con il grafico, esportarlo in una directory locale usando `helm chart export` . Ad esempio, esportare il grafico di cui è stato effettuato il pull nella `install` Directory:

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

## <a name="install-helm-chart"></a>Installare il grafico Helm

Eseguire `helm install` per installare il grafico Helm di cui è stato effettuato il pull nella cache locale ed esportato. Specificare un nome di versione, ad esempio *myhelmtest*, oppure passare il `--generate-name` parametro. Ad esempio:

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

Per verificare l'installazione, eseguire il `helm get manifest` comando. 

```console
helm get manifest myhelmtest
```

Il comando restituisce i dati YAML nel `configmap.yaml` file modello.

Eseguire `helm uninstall` per disinstallare la versione del grafico nel cluster:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Elimina grafico dal registro di sistema

Per eliminare un grafico dal registro contenitori, usare il comando [AZ ACR repository Delete][az-acr-repository-delete] . Eseguire il comando seguente e confermare l'operazione quando richiesto:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come creare e distribuire grafici Helm, vedere [Developing Helm charts][develop-helm-charts] (Distribuzione di grafici Helm).
* Altre informazioni sull'installazione di applicazioni con Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* I grafici Helm possono essere usati come parte del processo di compilazione del contenitore. Per altre informazioni, vedere [usare le attività container Registry di Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
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
[acr-tasks]: container-registry-tasks-overview.md
