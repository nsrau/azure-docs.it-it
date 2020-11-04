---
title: Gestire il contenuto pubblico nel registro contenitori privato
description: Procedure e flussi di lavoro in Azure Container Registry per gestire le dipendenze da immagini pubbliche dall'hub Docker e da altri contenuti pubblici
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 10/29/2020
ms.openlocfilehash: bb185e7d5803219135fddf421b7d6a89edd296b0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315811"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Gestire il contenuto pubblico con Azure Container Registry

Questo articolo è una panoramica delle procedure e dei flussi di lavoro per l'uso di un registro di sistema locale, ad esempio un [Registro contenitori di Azure](container-registry-intro.md) , per gestire copie del contenuto pubblico, ad esempio le immagini del contenitore nell'hub docker. 


## <a name="risks-with-public-content"></a>Rischi con contenuto pubblico

L'ambiente può avere dipendenze da contenuto pubblico come le immagini del contenitore pubblico, i [grafici Helm](https://helm.sh/), i criteri di [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) o altri artefatti. Ad esempio, è possibile eseguire [nginx](https://hub.docker.com/_/nginx) per il routing del servizio o `docker build FROM alpine` estraendo immagini direttamente dall'hub Docker o da un altro registro di sistema pubblico. 

Senza controlli appropriati, le dipendenze dal contenuto del registro di sistema pubblico possono causare rischi per i flussi di lavoro di sviluppo e distribuzione delle immagini. Per attenuare i rischi, quando possibile, conserva le copie locali del contenuto pubblico. Per informazioni dettagliate, vedere il [Blog di Open Container Initiative](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Eseguire l'autenticazione con Docker Hub

Come primo passaggio, se si esegue il pull di immagini pubbliche dall'hub Docker come parte di un flusso di lavoro di compilazione o distribuzione, si consiglia di [eseguire l'autenticazione usando un account Docker Hub](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) anziché una richiesta pull anonima.

> [!NOTE]
> A partire dal 2 novembre 2020, i [limiti di velocità di download](https://docs.docker.com/docker-hub/download-rate-limit) si applicano alle richieste anonime e autenticate all'hub Docker dagli account dei piani gratuiti Docker e vengono applicati dall'indirizzo IP e dall'ID Docker, respecitively. 
>
> Quando si stima il numero di richieste pull, tenere presente che quando si usano i servizi del provider di servizi cloud o si lavora dietro una NAT aziendale, più utenti verranno presentati all'hub Docker in modo aggregato come subset di indirizzi IP. L'aggiunta dell'autenticazione dell'account a pagamento di Docker alle richieste effettuate all'hub Docker eviterà le potenziali rotture del servizio dovute alla limitazione della velocità di limitazione.
>
> Per informazioni dettagliate, vedere [prezzi e sottoscrizioni Docker](https://www.docker.com/pricing) e le condizioni per il [servizio di Docker](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Token di accesso dell'hub Docker

Docker Hub supporta i [token di accesso personali](https://docs.docker.com/docker-hub/access-tokens/) come alternative a una password Docker durante l'autenticazione nell'hub docker. I token sono consigliati per i servizi automatizzati che eseguono il pull di immagini dall'hub docker. È possibile generare più token per utenti o servizi diversi e revocare i token quando non sono più necessari.

Per eseguire l'autenticazione con `docker login` usando un token, omettere la password nella riga di comando. Quando viene richiesta una password, immettere invece il token. Se è stata abilitata l'autenticazione a due fattori per l'account dell'hub Docker, è necessario usare un token di accesso personale per l'accesso dall'interfaccia della riga di comando di Docker.

### <a name="authenticate-from-azure-services"></a>Eseguire l'autenticazione da servizi di Azure

Diversi servizi di Azure, tra cui il servizio app e le istanze di contenitore di Azure, supportano il pull di immagini da registri pubblici, come l'hub Docker per le distribuzioni di contenitori Se è necessario distribuire un'immagine dall'hub Docker, è consigliabile configurare le impostazioni per l'autenticazione con un account Docker Hub. Esempi:

**Servizio app**

* **Origine immagine** : Hub Docker
* **Accesso al repository** : privato
* **Account di accesso** : \<Docker Hub username>
* **Password** : \<Docker Hub token>

Per informazioni dettagliate, vedere [pull di hub con autenticazione Docker nel servizio app](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Istanze di Azure Container**

* **Origine immagine** : Docker Hub o altro registro di sistema
* **Tipo di immagine** : privato
* **Server di accesso del registro immagini** : docker.io
* **Nome utente del registro immagini** : \<Docker Hub username>
* **Password del registro di sistema Image** : \<Docker Hub token>
* **Immagine** : docker.io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importare immagini in un registro contenitori di Azure
 
Per iniziare a gestire le copie delle immagini pubbliche, è possibile creare un registro contenitori di Azure se non ne è già presente uno. Creare un registro usando l' [interfaccia](container-registry-get-started-azure-cli.md)della riga di comando di Azure, [portale di Azure](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)o altri strumenti. 

Come passaggio unico consigliato, [importare](container-registry-import-images.md) immagini di base e altri contenuti pubblici nel registro contenitori di Azure. Il comando [AZ ACR Import](/cli/azure/acr#az_acr_import) nell'interfaccia della riga di comando di Azure supporta l'importazione di immagini da registri pubblici come l'hub Docker e Microsoft container Registry e da altri registri di contenitori privati. 

`az acr import` non richiede un'installazione locale di Docker. È possibile eseguirla con un'installazione locale dell'interfaccia della riga di comando di Azure o direttamente in Azure Cloud Shell. Supporta immagini di qualsiasi tipo di sistema operativo, immagini multiarchitettura o elementi OCI, ad esempio i grafici Helm.

Esempio:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

A seconda delle esigenze dell'organizzazione, è possibile importare in un registro dedicato o in un repository in un registro condiviso.

## <a name="automate-application-image-updates"></a>Automatizzare gli aggiornamenti delle immagini dell'applicazione

Gli sviluppatori di immagini dell'applicazione devono garantire che il codice faccia riferimento al contenuto locale sotto il controllo. Un' `Docker FROM` istruzione in un Dockerfile, ad esempio, deve fare riferimento a un'immagine in un registro di immagini di base privato anziché in un registro pubblico. 

Espansione nell'importazione di immagini, configurare un' [attività container Registry di Azure](container-registry-tasks-overview.md) per automatizzare le compilazioni di immagini dell'applicazione quando vengono aggiornate le immagini di base. Un'attività di compilazione automatica può rilevare sia [gli aggiornamenti delle immagini di base](container-registry-tasks-base-images.md) sia [gli aggiornamenti del codice sorgente](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Per un esempio dettagliato, vedere [la pagina relativa alla modalità di utilizzo e gestione del contenuto pubblico con le attività di container Registry di Azure](tasks-consume-public-content.md). 

> [!NOTE]
> Una singola attività preconfigurata consente di ricompilare automaticamente ogni immagine dell'applicazione che fa riferimento a un'immagine di base dipendente. 
 
## <a name="next-steps"></a>Passaggi successivi
 
* Altre informazioni sulle [attività ACR](container-registry-tasks-overview.md) per compilare, eseguire, effettuare il push e applicare patch alle immagini del contenitore in Azure.
* Vedere [come usare e gestire il contenuto pubblico con le attività di container Registry di Azure](tasks-consume-public-content.md) per un flusso di lavoro di controllo automatizzato per aggiornare le immagini di base all'ambiente. 
* Per ulteriori esempi, vedere le [esercitazioni sulle attività ACR](container-registry-tutorial-quick-task.md) per automatizzare le compilazioni di immagini e gli aggiornamenti.
