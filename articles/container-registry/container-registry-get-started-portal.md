---
title: 'Avvio rapido: Creare un registro nel portale'
description: Informazioni su come apprendere rapidamente a creare un'istanza di Registro Azure Container privata con il portale di Azure.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: f4f16506a36acfe8845e85caf2d337f992f0f332
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746670"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Avvio rapido: Creare un'istanza di Registro Azure Container usando il portale di Azure

Un Registro Azure Container è un registro Docker privato in Azure nel quale è possibile archiviare e gestire le immagini del contenitore Docker privato e gli artefatti correlati. In questa guida di avvio rapido viene creato un registro contenitori con il portale di Azure. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

Per accedere al registro e usare immagini del contenitore, questa guida di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure (consigliata la versione 2.0.55 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container** .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Passare al registro contenitori nel portale":::

Nella scheda **Informazioni di base** immettere i valori per **Gruppo di risorse** e **Nome registro** . Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Per questa guida introduttiva creare un nuovo gruppo di risorse nella posizione `West US` denominata `myResourceGroup` e per **SKU** selezionare "Basic".

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Passare al registro contenitori nel portale":::

Accettare i valori predefiniti per le impostazioni rimanenti. Selezionare quindi **Rivedi e crea** . Dopo aver esaminato le impostazioni, selezionare **Crea** .

In questa guida di avvio rapido viene creato un registro *Basic* , ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio (SKU) disponibili, vedere [Livelli di servizio del registro contenitori][container-registry-skus].

Quando viene visualizzato il messaggio **La distribuzione è riuscita** , selezionare il registro contenitori nel portale. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Passare al registro contenitori nel portale":::

Prendere nota del nome del registro e del valore del **Server di accesso** . Usare questi valori nella procedura seguente quando si esegue il push e il pull delle immagini con Docker.

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza del registro. [Accedere all'interfaccia della riga di comando di Azure][get-started-with-azure-cli] nel computer locale e quindi eseguire il comando [az acr login][az-acr-login]. Quando si accede con l'interfaccia della riga di comando di Azure, specificare solo il nome del registro. Non includere il suffisso di dominio 'azurecr.io'.

```azurecli
az acr login --name <registry-name>
```

Esempio:

```azurecli
az acr login --name mycontainerregistry
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Elencare le immagini del contenitore

Per elencare le immagini presenti nel registro, passare al registro nel portale, scegliere **Repository** e quindi selezionare il repository **hello-world** creato con `docker push`.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Passare al registro contenitori nel portale":::

Se si seleziona il repository **hello-world** , l'immagine con tag `v1` viene visualizzata in **Tag** .

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse, passare al gruppo di risorse **myResourceGroup** nel portale. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate nel registro.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Passare al registro contenitori nel portale":::


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Registro Azure Container con il portale di Azure, è stato eseguito il push di un'immagine del contenitore e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Esercitazioni su Attività del Registro Azure Container][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
