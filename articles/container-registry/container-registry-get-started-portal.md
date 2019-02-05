---
title: 'Guida introduttiva: creare un registro Docker privato in Azure - Portale di Microsoft Azure'
description: Informazioni su come apprendere rapidamente a creare un registro contenitori Docker privato con il portale di Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 93c22475a4043d1cbf5cb0ad7f9b134e8ac717cc
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298416"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Guida introduttiva: Creare un registro contenitori privato usando il portale di Azure

Un registro contenitori di Azure è un registro Docker privato in Azure nel quale è possibile archiviare e gestire le immagini del contenitore Docker privato. In questa guida introduttiva viene creato un registro contenitori di Azure con il portale di Azure. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

Per accedere al registro e usare immagini del contenitore, questa guida introduttiva richiede l'esecuzione dell'interfaccia della riga di comando di Azure (consigliata la versione 2.0.55 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-01]

Immettere i valori nei campi **Nome registro** e **Gruppo di risorse**. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Per questa guida introduttiva creare un nuovo gruppo di risorse nella posizione `West US` denominata `myResourceGroup` e per **SKU** selezionare "Basic". Selezionare **Crea** per distribuire l'istanza del record di controllo di accesso.

![Creare il registro contenitori nel portale di Azure][qs-portal-03]

In questa guida introduttiva viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [SKU di Registro Azure Container][container-registry-skus].

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, selezionare il registro contenitori nel portale. 

![Panoramica del registro contenitori nel portale di Azure][qs-portal-05]

Prendere nota del valore indicato in **Server di accesso**. Questo valore servirà nei passaggi seguenti quando si usa il registro con l'interfaccia della riga di comando di Azure e Docker.

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro Azure Container. Aprire una shell dei comandi nel sistema operativo e usare il comando [az acr login][az-acr-login] nell'interfaccia della riga di comando di Azure.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Elencare le immagini del contenitore

Per elencare le immagini presenti nel registro, passare al registro nel portale e scegliere **Repository**, quindi selezionare il repository creato con `docker push`.

In questo esempio è stato selezionato il repository **busybox** ed è possibile vedere l'immagine contraddistinta dal tag `v1` in **TAGS**.

![Elencare le immagini del contenitore nel portale di Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse, passare al gruppo di risorse **myResourceGroup** nel portale. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate nel registro.

![Eliminare il gruppo di risorse nel portale di Azure][qs-portal-08]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di Registro Azure Container con il portale di Azure, è stato eseguito il push di un'immagine del contenitore e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
