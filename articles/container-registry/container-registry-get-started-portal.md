---
title: Guida introduttiva - Creare un registro Docker privato in Azure con il portale di Azure
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con il portale di Azure.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: db112f7f8f486093509a86f9781c30133925c25f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Creare un registro contenitori con il portale di Azure

Un Registro contenitori di Azure è un registro Docker privato in Azure nel quale è possibile archiviare e gestire le immagini del contenitore Docker privato. In questa guida introduttiva si crea un registro contenitori con il portale di Azure, si esegue il push di un'immagine del contenitore nel registro e infine si distribuisce il contenitore dal registro a Istanze di contenitore di Azure.

Per completare questa Guida introduttiva, è necessario che Docker sia installato in locale. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Selezionare **Crea una risorsa** > **Contenitori** > **Registro contenitori di Azure**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-01]

Immettere i valori nei campi **Nome registro** e **Gruppo di risorse**. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Creare un nuovo gruppo di risorse denominato `myResourceGroup` e selezionare 'Basic' per **SKU**. Selezionare **Crea** per distribuire l'istanza del record di controllo di accesso.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-03]

In questa guida introduttiva viene creato un registro contenitori di *base*. Registro contenitori di Azure è disponibile in diversi SKU, descritti brevemente nella tabella riportata di seguito. Per altri dettagli su ogni SKU, vedere [SKU di Registro contenitori][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, selezionare il registro contenitori nel portale, quindi selezionare le **chiavi di accesso**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-05]

In **Utente amministratore**selezionare **Abilita**. Annotare i valori seguenti:

* Server di accesso
* Username
* password

Questi valori vengono usati nei passaggi seguenti quando si usa il registro con l'interfaccia della riga di comando Docker.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Accedere al record di controllo di accesso

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo usare il comando [docker login][docker-login]. Sostituire i valori di *username*, *password* e del *login server* con quelli annotati nel passaggio precedente.

```bash
docker login --username <username> --password <password> <login server>
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. È possibile visualizzare un avviso di sicurezza in cui si consiglia l'uso del parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altri dettagli, vedere le informazioni di riferimento sul comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel Registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se necessario, eseguire il comando seguente per eseguire il pull di un'immagine esistente dall'Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Prima di eseguire il push dell'immagine nel registro, è necessario contrassegnare l'immagine con il nome del server di accesso al record di controllo di accesso. Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire *login server* con il nome del server di accesso registrato in precedenza.

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Infine, usare [docker push][docker-push] per eseguire il push dell'immagine nell'istanza del record di controllo di accesso. Sostituire *login server* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker push <login server>/aci-helloworld:v1
```

L'output di comando `docker push` corretto è simile al seguente:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Elencare le immagini del contenitore

Per elencare le immagini nell'istanza del record di controllo di accesso, passare al registro nel portale e scegliere **Repository**, quindi selezionare il repository creato con `docker push`.

In questo esempio è stato selezionano il repository **aci-helloworld** ed è possibile vedere l'immagine `v1` contrassegnata in **TAGS**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Distribuire l'immagine in Istanze di contenitore di Azure

Per distribuire in un'istanza dal registro è necessario passare al repository (aci-helloworld) e quindi fare clic sui puntini di sospensione accanto a v1.

![Avvio di un'istanza di contenitore di Azure dal portale][qs-portal-10]

Verrà visualizzato un menu di scelta rapida. Scegliere **Esegui istanza**:

![Menu di scelta rapida di avvio dell'istanza di contenitore di Azure][qs-portal-11]

Compilare il campo **Nome contenitore**, verificare che sia selezionata la sottoscrizione corretta, selezionare il **gruppo di risorse** "myResourceGroup" esistente e quindi fare clic su **OK** per avviare l'istanza di contenitore di Azure.

![Opzioni di distribuzione per l'avvio dell'istanza di contenitore di Azure][qs-portal-12]

All'avvio della distribuzione, nel dashboard del portale viene inserito un riquadro che indica lo stato della distribuzione. Dopo aver completato la distribuzione, il riquadro viene aggiornato per mostrare il nuovo gruppo di contenitori **mycontainer**.

![Stato della distribuzione dell'istanza di contenitore di Azure][qs-portal-13]

Selezionare il gruppo di contenitori mycontainer per visualizzarne le proprietà. Prendere nota dell'**indirizzo IP** del gruppo di contenitori e dello **STATO** del contenitore.

![Dettagli del contenitore dell'istanza di contenitore di Azure][qs-portal-14]

## <a name="view-the-application"></a>Visualizzare l'applicazione

Quando il contenitore si trova nello stato **In esecuzione**, usare un browser a scelta per passare all'indirizzo IP annotato nel passaggio precedente per visualizzare l'applicazione.

![App Hello World nel browser][qs-portal-15]

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere le risorse passare al gruppo di risorse **myResourceGroup** nel portale. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse, il registro contenitori di Azure e tutte le istanze di contenitore di Azure.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-08]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un registro contenitori di Azure con l'interfaccia della riga di comando di Azure e ne è stata avviata un'istanza tramite Istanze di contenitore di Azure. Passare all'esercitazione su Istanze di contenitore di Azure per maggiori informazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
