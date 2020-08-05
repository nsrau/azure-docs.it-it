---
title: "Avvio rapido: Distribuire un contenitore Docker in un'istanza di contenitore - Interfaccia della riga di comando di Docker"
description: In questo argomento di avvio rapido viene usata l'interfaccia della riga di comando di Docker per distribuire rapidamente un'app Web che viene eseguita in un'istanza di contenitore di Azure isolata
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408085"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Avvio rapido: Distribuire un'istanza di contenitore in Azure con l'interfaccia della riga di comando di Docker

Istanze di Azure Container consente di eseguire i contenitori Docker serverless in Azure in modo semplice e rapido. Distribuire un'istanza di contenitore su richiesta quando si sviluppano app native del cloud e si vuole passare facilmente dallo sviluppo locale alla distribuzione cloud.

In questo argomento di avvio rapido si usano i comandi nativi dell'interfaccia della riga di comando di Docker per distribuire un contenitore Docker e rendere la relativa applicazione disponibile in Istanze di Azure Container. Questa funzionalità è abilitata dall'[integrazione tra Docker e Azure](https://docs.docker.com/engine/context/aci-integration/) (versione beta). Pochi secondi dopo aver eseguito un comando `docker run`, è possibile passare all'applicazione in esecuzione nel contenitore:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-account] prima di iniziare.

Per questo argomento di avvio rapido è necessario installare Docker Desktop Edge versione 2.3.2.0 o successiva, disponibile per [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) o [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Oppure installare l'[interfaccia della riga di comando di integrazione fra Docker e Istanze di Azure Container per Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (beta). 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e richiede funzionalità beta (anteprima) in Docker. Vedere altre informazioni sulle [versioni Stable ed Edge di Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions). Non sono supportate tutte le funzionalità di Istanze di Azure Container. È possibile fornire feedback sull'integrazione fra Docker e Azure creando un problema nel repository di GitHub [aci-integration-beta](https://github.com/docker/aci-integration-beta).

## <a name="create-azure-context"></a>Creare un contesto di Azure

Per usare i comandi di Docker per l'esecuzione di contenitori in Istanze di Azure Container, accedere prima di tutto ad Azure:

```bash
docker login azure
```

Quando richiesto, immettere o selezionare le credenziali di Azure.


Eseguire `docker context create aci` per creare un contesto di Istanze di Azure Container. Questo contesto associa Docker alla sottoscrizione di Azure per consentire la creazione di istanze di contenitore. Ad esempio, per creare un contesto denominato *myacicontext*:

```
docker context create aci myacicontext
```

Quando richiesto, selezionare l'ID sottoscrizione di Azure, quindi selezionare un gruppo di risorse esistente o **crearne un nuovo**. Se si sceglie un nuovo gruppo di risorse, questo viene creato con un nome generato dal sistema. Le Istanze di Azure Container, analogamente a tutte le risorse di Azure, devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.


Eseguire `docker context ls` per confermare che il contesto di Istanze di Azure Container è stato aggiunto ai contesti Docker:

```
docker context ls
```

## <a name="create-a-container"></a>Creare un contenitore

Dopo aver creato un contesto Docker, è possibile creare un contenitore in Azure. In questo argomento di avvio rapido viene usata l'immagine `mcr.microsoft.com/azuredocs/aci-helloworld` pubblica. In questa immagine è inclusa una piccola app Web scritta in Node.js che distribuisce una pagina HTML statica.

Per prima cosa, passare al contesto di Istanze di Azure Container. Tutti i comandi Docker successivi vengono eseguiti in questo contesto.

```
docker context use myacicontext
```

Eseguire il comando `docker run` seguente per creare l'istanza di contenitore di Azure con la porta 80 esposta a Internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Output di esempio per una distribuzione riuscita:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Eseguire `docker ps` per ottenere informazioni dettagliate sul contenitore in esecuzione, incluso l'indirizzo IP pubblico:

```
docker ps
```


L'output di esempio mostra un indirizzo IP pubblico, in questo caso *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Passare ora all'indirizzo IP nel browser. Se viene visualizzata una pagina Web simile all'immagine seguente, congratulazioni! È stata completata la distribuzione di un'applicazione in esecuzione in un contenitore Docker in Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App distribuita usando Istanze di Azure Container visualizzata nel browser":::

## <a name="pull-the-container-logs"></a>Effettuare il pull dei log del contenitore

Quando è necessario risolvere i problemi di un contenitore o dell'applicazione in esecuzione (o semplicemente visualizzarne l'output), iniziare visualizzando i log dell'istanza del contenitore.

Ad esempio, eseguire il comando `docker logs` per visualizzare i log del contenitore *hungry-kirch* nel contesto di Istanze di Azure Container:

```azurecli-interactive
docker logs hungry-kirch
```

L'output visualizza i log per il contenitore e dovrebbe mostrare le richieste HTTP GET generate quando l'applicazione è stata visualizzata nel browser.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Pulire le risorse

Quando il contenitore non è più necessario, eseguire `docker rm` per rimuoverlo. Questo comando arresta ed elimina l'istanza di contenitore di Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di contenitore di Azure da un'immagine pubblica usando l'integrazione fra Docker ed Azure. Per altre informazioni sugli scenari di integrazione, vedere la [documentazione di Docker](https://docs.docker.com/engine/context/aci-integration/). 

È anche possibile usare l'[estensione Docker](https://aka.ms/VSCodeDocker) per Visual Studio Code per un'esperienza integrata di sviluppo, esecuzione e gestione di contenitori, immagini e contesti.

Per usare gli strumenti di Azure per creare e gestire istanze di contenitore, vedere altri argomenti di avvio rapido che usano l'[interfaccia della riga di comando di Azure](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), il [portale di Azure](container-instances-quickstart-portal.md) e il [modello di Azure Resource Manager](container-instances-quickstart-template.md).

Per provare a creare un'immagine del contenitore e a distribuirla da un Registro Azure Container privato, passare all'esercitazione su Istanze di Azure Container.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di Azure Container](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

