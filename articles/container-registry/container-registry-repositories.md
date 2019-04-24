---
title: Repository di registri contenitori di Azure nel portale di Azure
description: Come visualizzare repository di registri contenitori di Azure nel portale di Azure.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427099"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visualizzare repository di registri contenitori di Azure nel portale di Azure

Il Registro Azure Container consente di archiviare immagini di contenitore Docker all'interno di repository. Archiviando le immagini in repository, è possibile archiviare gruppi di immagini (o versioni di immagini) in ambienti isolati. È possibile specificare questi repository quando si effettua il push delle immagini nel registro e visualizzarne il contenuto nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

* **Registro contenitori di**: Creare un registro contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Installare [Docker] [ docker-install] nel computer locale, che fornisce l'interfaccia della riga di comando di Docker.
* **Immagine del contenitore**: Push di un'immagine nel registro contenitori. Per istruzioni su come eseguire il push e il pull delle immagini, vedere [Effettuare il push e il pull di un'immagine](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visualizzare i repository nel portale di Azure

È possibile visualizzare l'elenco dei repository che ospitano le immagini e i tag di immagine nel portale di Azure.

Se è stata seguita la procedura descritta in [Effettuare il push e il pull di un'immagine](container-registry-get-started-docker-cli.md) (e non è stata eliminata l'immagine), nel registro contenitori dovrebbe essere presente un'immagine di Nginx. Le istruzioni dell'articolo indicavano di contrassegnare l'immagine con uno spazio dei nomi: lo spazio "samples" in `/samples/nginx`. Il comando [docker push][docker-push] specificato nell'articolo era:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Il Registro Azure Container supporta spazi dei nomi dei repository multilivello ed è quindi possibile raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per specifici team operativi o di sviluppo. Per altre informazioni sul repository nei registri contenitori, vedere [Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](container-registry-intro.md).

Per visualizzare un repository:

1. Accedere al [portale di Azure][portal]
1. Selezionare il **Registro Azure Container** in cui è stato effettuato il push dell'immagine di Nginx
1. Selezionare **Repository** per visualizzare un elenco dei repository contenenti le immagini disponibili nel registro
1. Selezionare un repository per visualizzare i tag di immagine presenti all'interno del repository

Se, ad esempio, non è stato effettuato il push dell'immagine di Nginx come illustrato in [Effettuare il push e il pull di un'immagine](container-registry-get-started-docker-cli.md), verrà visualizzata una schermata simile alla seguente:

![I repository nel portale](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base sulla visualizzazione e l'uso di repository nel portale, provare a usare Registro Azure Container con un cluster del [servizio Azure Kubernetes](../aks/tutorial-kubernetes-prepare-app.md).

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
