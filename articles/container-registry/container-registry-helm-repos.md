---
title: Usare i repository Helm in Registro Azure Container
description: Informazioni su come usare un repository Helm con il Registro Azure Container per archiviare i grafici per le applicazioni
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551994"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Usare il Registro Azure Container come repository Helm per i grafici per le applicazioni

Per gestire e distribuire rapidamente applicazioni per Kubernetes, è possibile usare il [gestore pacchetti open source Helm][helm]. Con Helm le applicazioni sono definite come *grafici* archiviati in un repository di grafici Helm. Tali grafici definiscono le configurazioni e le dipendenze e possono essere con versione durante il ciclo di vita dell'applicazione. Il Registro Azure Container può essere usato come host per il repository di grafici Helm.

Con il Registro Azure Container è disponibile un repository di grafici Helm privato e sicuro in grado di integrarsi con le pipeline di compilazione o con altri servizi di Azure. I repository di grafici Helm in Registro Azure Container includono funzionalità di replica geografica per mantenere i grafici più vicini alle distribuzioni e per garantire la ridondanza. Viene addebitato solo il costo per l'archiviazione usata dai grafici di e sono disponibili tutti i livelli di prezzo di Registro Azure Container.

Questo articolo illustra come usare un repository di grafici Helm archiviato in Registro Azure Container.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura descritta in questo articolo, è necessario soddisfare i prerequisiti seguenti:

- **Registro Azure Container**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
- **Versione del client Helm 2.11.0 (non una versione finale candidata) o versione successiva**: eseguire `helm version` per trovare la versione corrente. È anche necessario un server Helm (Tiller) inizializzato in un cluster Kubernetes. Se necessario, è possibile [creare un cluster del servizio Azure Kubernetes][aks-quickstart]. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install] (Installazione di Helm).
- **Interfaccia della riga di comando di Azure versione 2.0.46 o successiva** - Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Aggiungere un repository al client Helm

Un repository Helm è un server HTTP in cui possono essere archiviati i grafici Helm. Il Registro Azure Container può rendere disponibile questa risorsa di archiviazione per i grafici Helm e gestire la definizione dell'indice quando si aggiungono e si rimuovono i grafici nel repository.

Per aggiungere il Registro Azure Container come un repository di grafici Helm, usare l'interfaccia della riga di comando di Azure. In questo modo il client Helm viene aggiornato con l'URI e le credenziali per il repository supportato da Registro Azure Container. Non è necessario specificare manualmente le informazioni di questo repository, pertanto le credenziali, ad esempio, non sono esposte nella cronologia dei comandi.

Se necessario, accedere all'interfaccia della riga di comando di Azure e seguire le istruzioni:

```azurecli
az login
```

Configurare le impostazioni predefinite dell'interfaccia della riga di comando di Azure con il nome dell'istanza di Registro Azure Container tramite il comando [az configure][az-configure]. Nell'esempio seguente sostituire `<acrName>` con il nome del registro:

```azurecli
az configure --defaults acr=<acrName>
```

A questo punto aggiungere il repository di grafici Helm di Registro Azure Container al client Helm usando il comando [az acr helm repo add][az-acr-helm-repo-add]. Tale comando ottiene un tipo di token di autenticazione per il Registro Azure Container che viene usato dal client Helm. Il token di autenticazione è valido per un'ora. In modo analogo a `docker login`, è possibile eseguire questo comando nelle sessioni future dell'interfaccia della riga di comando per autenticare il client Helm con il repository di grafici di Registro Azure Container:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Aggiungere un grafico al repository

In questo articolo si ottiene un grafico Helm esistente dal repository pubblico Helm *stable*. Il repository *stable* repository è un repository pubblico gestito che include i grafici di applicazione comuni. I responsabili della manutenzione dei pacchetti possono inviare i grafici al repository *stable* repository per renderli disponibili in modo analogo a come l'hub Docker offre un registro pubblico per le immagini dei contenitori comuni. Il grafico scaricato dal repository pubblico *stable* può quindi essere inserito nel repository di Registro Azure Container privato. Nella maggior parte degli scenari è necessario compilare e caricare i propri grafici per le applicazioni sviluppate. Per altre informazioni su come compilare i grafici Helm, vedere [developing Helm charts][develop-helm-charts] (Sviluppare grafici Helm).

Creare una directory in *~/acr-helm*e quindi scaricare il grafico *stable/wordpress* esistente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Elencare il grafico scaricato e prendere nota della versione di Wordpress inclusa nel nome del file. Il comando `helm fetch stable/wordpress` non ha specificato una versione particolare e pertanto è stata recuperata la versione *più recente*. Tutti i grafici Helm includono un numero di versione nel nome del file in base allo standard [SemVer 2][semver2]. Nell'output di esempio seguente la versione del grafico Helm è *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Eseguire il push del grafico nel repository di grafici Helm in Registro Azure Container tramite il comando dell'interfaccia della riga di comando di Azure [az acr helm push][az-acr-helm-push]. Specificare il nome del grafico Helm scaricato nel passaggio precedente, ad esempio *wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Dopo qualche istante, l'interfaccia della riga di comando di Azure segnala che il grafico è stato salvato, come illustrato nell'output di esempio seguente:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

Il client Helm mantiene una copia del contenuto del repository remoto memorizzata nella cache locale. Le modifiche apportate a un repository remoto non aggiornino automaticamente l'elenco dei grafici disponibili noti in locale nel client Helm. Quando si esegue una ricerca dei grafici nei repository, Helm usa l'indice locale memorizzata nella cache. Per usare il grafico caricato nel passaggio precedente, l'indice del repository Helm locale deve essere aggiornato. È possibile reindicizzare i repository nel client Helm o usare l'interfaccia della riga di comando di Azure per aggiornare l'indice del repository. Ogni volta che si aggiunge un grafico al repository, è necessario completare questo passaggio:

```azurecli
az acr helm repo add
```

Con un grafico archiviato nel repository e l'indice aggiornato disponibile in locale, è possibile usare i normali comandi del client Helm per eseguire una ricerca o un'installazione. Per visualizzare tutti i grafici in un repository, usare `helm search <acrName>`. Indicare il nome del proprio Registro Azure Container:

```console
helm search <acrName>
```

Il grafico Wordpress inserito nel passaggio precedente è elencato, come illustrato nell'output di esempio seguente:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

È anche possibile elencare i grafici tramite l'interfaccia della riga di comando di Azure usando [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Visualizzare le informazioni per un grafico Helm

Per visualizzare le informazioni per un grafico specifico nel repository, è possibile usare nuovamente il client Helm normale. Per visualizzare le informazioni per il grafico denominato *wordpress*, usare `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Quando non viene specificato alcun numero di versione, viene usata la versione *più recente*. Helm restituisce informazioni dettagliate relative al grafico, come illustrato nell'output di esempio sintetico seguente:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

È anche possibile visualizzare le informazioni per un grafico con il comando [az acr helm show][az-acr-helm-show] dell'interfaccia della riga di comando di Azure. Anche in questo caso, per impostazione predefinita viene restituita la versione del grafico *più recente*. È possibile aggiungere `--version` per elencare una versione specifica di un grafico, ad esempio *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Installare un grafico Helm dal repository

Il grafico Helm viene installato specificando il nome del repository e quindi il nome del grafico. Usare il client Helm per installare il grafico Wordpress:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Se si esegue il push nel repository di grafici Helm di Registro Azure Container e in seguito si torna in una nuova sessione dell'interfaccia della riga di comando,per il client Helm locale è necessario un token di autenticazione aggiornato. Per ottenere un nuovo token di autenticazione, usare il comando [az acr helm repo add][az-acr-helm-repo-add].

Durante il processo di installazione vengono completati i passaggi seguenti:

- Il client Helm cerca l'indice del repository locale.
- Il grafico corrispondente viene scaricato dal repository di Registro Azure Container.
- Il grafico viene distribuito usando Tiller nel cluster Kubernetes.

L'output di esempio sintetico seguente illustra le risorse di Kubernetes distribuite tramite il grafico Helm:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Eliminare un grafico Helm dal repository

Per eliminare un grafico dal repository, usare il comando [az acr helm delete][az-acr-helm-delete]. Specificare il nome del grafico, ad esempio *wordpress*, nonché la versione da eliminare, ad esempio *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Se si intende eliminare tutte le versioni del grafico denominato, omettere il parametro `--version`.

Il grafico continua a essere restituito in `helm search <acrName>`. Anche in questo caso il client Helm non aggiorna automaticamente l'elenco dei grafici disponibili in un repository. Per aggiornare l'indice del repository del client Helm, usare di nuovo il comando [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato un grafico Helm esistente nel repository pubblico *stable*. Per altre informazioni su come creare e distribuire grafici Helm, vedere [Developing Helm charts][develop-helm-charts] (Distribuzione di grafici Helm).

I grafici Helm possono essere usati come parte del processo di compilazione del contenitore. Per altre informazioni, vedere [Use Azure Registro Container Tasks][acr-tasks] (Usare le attività del Registro contenitore di Azure).

Per altre informazioni su come usare e gestire il Registro Azure Container, vedere le [procedure consigliate][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
