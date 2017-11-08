---
title: Guida introduttiva - Creare un registro Docker privato in Azure con il portale di Azure
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con il portale di Azure.
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 514fa3490e480647f0923c99bd9606a3726d4d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Creare un registro contenitori con il portale di Azure

Un Registro contenitori di Azure è un registro Docker privato in Azure nel quale è possibile archiviare e gestire le immagini del contenitore Docker privato. In questa guida introduttiva viene creato un Registro contenitori di Azure con il portale di Azure.

Per completare questa Guida introduttiva, è necessario che Docker sia installato in locale. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Selezionare **Nuovo** > **Contenitori** > **Registro contenitori di Azure**.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-01]

Immettere i valori nei campi **Nome registro** e **Gruppo di risorse**. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Creare un nuovo gruppo di risorse denominato `myResourceGroup` e selezionare 'Basic' per **SKU**. Selezionare **Crea** per distribuire l'istanza del record di controllo di accesso.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-03]

In questa guida introduttiva viene creato un registro contenitori di *base*. Registro contenitori di Azure è disponibile in diversi SKU, descritti brevemente nella tabella riportata di seguito. Per altri dettagli su ogni SKU, vedere [SKU di Registro contenitori](container-registry-skus.md).

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

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo usare il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Sostituire i valori di *username*, *password* e del *login server* con quelli annotati nel passaggio precedente.

```bash
docker login --username <username> --password <password> <login server>
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione. È possibile visualizzare un avviso di sicurezza in cui si consiglia l'uso del parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Vedere la Guida comandi [docker login](https://docs.docker.com/engine/reference/commandline/login/) (accesso a Docker) per altre informazioni.

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel Registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se necessario, eseguire il comando seguente per eseguire il pull di un'immagine esistente dall'Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Prima di eseguire il push dell'immagine nel registro, è necessario contrassegnare l'immagine con il nome del server di accesso al record di controllo di accesso. Contrassegnare l'immagine usando il comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Sostituire *login server* con il nome del server di accesso registrato in precedenza.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Infine, usare [docker push](https://docs.docker.com/engine/reference/commandline/push/) per eseguire il push dell'immagine nell'istanza del record di controllo di accesso. Sostituire *login server* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```
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

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse **myResourceGroup**. Verranno così eliminati il gruppo di risorse, l'istanza del record di controllo di accesso e tutte le immagini del contenitore.

![Creazione di un registro contenitori con il portale di Azure][qs-portal-08]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida è stato creato un Registro contenitori di Azure con l'interfaccia della riga di comando di Azure. Se si desidera usare il Registro contenitori di Azure con le istanze di contenitore di Azure, continuare con l'esercitazione relativa alle istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md)

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