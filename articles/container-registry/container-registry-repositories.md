---
title: Repository di contenitore del Registro di sistema Azure nel portale di Azure
description: Come visualizzare repository del Registro di sistema di Azure contenitore nel portale di Azure.
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 593972e972207a27d1232fcb0c1bf220ac3a8def
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visualizzazione contenitore del Registro di sistema repository nel portale di Azure

Registro di sistema di contenitore di Azure consente di archiviare Docker immagini contenitore nel repository. Tramite l'archiviazione di immagini nei repository, è possibile archiviare i gruppi di immagini, o le versioni delle immagini, in ambienti isolati. Quando si inserisce le immagini al Registro di sistema e visualizzarne il contenuto nel portale di Azure, è possibile specificare questi archivi.

## <a name="prerequisites"></a>Prerequisiti

* **Registro di sistema contenitore**: creare un registro di sistema di contenitore nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI**: installare [Docker] [ docker-install] sul computer locale, che fornisce l'interfaccia della riga di comando di Docker.
* **Immagine contenitore**: Push un'immagine nel Registro di sistema contenitore. Per istruzioni su come le immagini di push e pull, vedere [Push e pull un'immagine](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Repository di visualizzazione nel portale di Azure

È possibile visualizzare un elenco degli archivi di ospitare le immagini, nonché i tag di immagine, nel portale di Azure.

Se è stata seguita la procedura descritta in [Push e pull un'immagine](container-registry-get-started-docker-cli.md) (e non è stata successivamente eliminare l'immagine), è necessario avere un'immagine di Nginx nel Registro di sistema contenitore. Le istruzioni dell'articolo specificato contrassegnare l'immagine con uno spazio dei nomi "esempi" nei `/samples/nginx`. Come un aggiornamento, il [push di docker] [ docker-push] comando specificato in tale articolo è stato:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Poiché Azure contenitore Registro di sistema supporta tali spazi dei nomi del repository a più livelli, è possibile definire l'ambito di raccolte di immagini correlate a un'app specifica, o una raccolta di App, i team operativi o di sviluppo diversi. Per altre informazioni sul repository nei registri contenitori, vedere [Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](container-registry-intro.md).

Per visualizzare un repository:

1. Accedi al [portale di Azure][portal]
1. Selezionare il **Registro di sistema di Azure contenitore** a cui è stato inserito l'immagine Nginx
1. Selezionare **repository** per visualizzare un elenco dei repository che contengono le immagini nel Registro di sistema
1. Selezionare un repository per visualizzare i tag di immagine all'interno di tale repository

Ad esempio, se non si è inserito l'immagine di Nginx come indicato nel [Push e pull un'immagine](container-registry-get-started-docker-cli.md), dovrebbe essere simile a:

![I repository nel portale](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i concetti di base di visualizzare e lavorare con i repository nel portale, provare a usare Azure contenitore del Registro di sistema con un [servizio contenitore di Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md) cluster.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
