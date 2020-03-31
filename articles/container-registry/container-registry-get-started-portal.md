---
title: 'Avvio rapido: Creare un registro nel portale'
description: Imparare rapidamente a creare un registro Docker privato in Registro Azure Container con il portale di Azure.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409264"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Guida di avvio rapido: Creare un registro contenitori privato usando il portale di Azure

Un Registro Azure Container è un registro Docker privato in Azure nel quale è possibile archiviare e gestire immagini del contenitore Docker privato e gli artefatti correlati. In questa guida di avvio rapido viene creato un registro contenitori di Azure con il portale di Azure. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

Per accedere al registro e usare immagini del contenitore, questa guida di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure (consigliata la versione 2.0.55 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-01]

Nella scheda **Informazioni di base** immettere i valori per **Gruppo di risorse** e **Nome registro**. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Per questa guida di avvio rapido creare un nuovo gruppo di risorse nella posizione `West US` denominata `myResourceGroup` e per **SKU** selezionare "Basic". 

![Creare il registro contenitori nel portale di Azure][qs-portal-03]

Accettare i valori predefiniti per le impostazioni rimanenti. Selezionare quindi **Rivedi e crea**. Dopo aver esaminato le impostazioni, selezionare **Crea**.

In questa guida di avvio rapido viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [SKU di Registro Azure Container][container-registry-skus].

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, selezionare il registro contenitori nel portale. 

![Panoramica del registro contenitori nel portale di Azure][qs-portal-05]

Prendere nota del valore indicato in **Server di accesso**. Usare questo valore nella procedura seguente quando si esegue il push e il pull delle immagini con Docker.

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro Azure Container. Aprire una shell dei comandi nel sistema operativo e usare il comando [az acr login][az-acr-login] nell'interfaccia della riga di comando di Azure. Durante l'accesso specificare solo il nome del registro di sistema. Non includere il suffisso 'azurecr.io'.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Elencare le immagini del contenitore

Per elencare le immagini presenti nel registro, passare al registro nel portale e scegliere **Repository**, quindi selezionare il repository creato con `docker push`.

In questo esempio è stato selezionato il repository **hello-world** ed è possibile vedere l'immagine `v1` contrassegnata in **Tags**.

![Elencare le immagini del contenitore nel portale di Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse, passare al gruppo di risorse **myResourceGroup** nel portale. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate nel registro.

![Eliminare il gruppo di risorse nel portale di Azure][qs-portal-08]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Registro Azure Container con il portale di Azure, è stato eseguito il push di un'immagine del contenitore e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

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
