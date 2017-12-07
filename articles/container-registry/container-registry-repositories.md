---
title: Archivi di registri contenitori di Azure
description: Come usare gli archivi di registri contenitori di Azure per le immagini Docker
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Archivi di registri contenitori di Azure

Il registro contenitori di Azure consente di archiviare le immagini dei contenitori in repository. Archiviando le immagini nei repository, è possibile avere gruppi di immagini (o versioni delle immagini) in ambienti isolati. È possibile specificare questi repository quando si effettua il push delle immagini nel registro.


## <a name="prerequisites"></a>Prerequisiti
* **Registro di contenitori di Azure**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio, usare il [Portale di Azure](container-registry-get-started-portal.md) o l'[interfaccia della riga di comando di Azure 2.0](container-registry-get-started-azure-cli.md).
* **Interfaccia della riga di comando di Docker**: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).
* **Eseguire il pull di un'immagine**: estrarre un'immagine dal registro Docker Hub pubblico, aggiungervi un tag ed eseguirne il push nel registro. Per istruzioni su come eseguire il push e il pull delle immagini, vedere [Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Visualizzazione dei repository nel portale

Dopo aver eseguito il push delle immagini nel registro contenitori, è possibile vedere un elenco dei repository che ospitano le immagini nel portale di Azure.

Se è stata seguita la procedura descritta nell'articolo [Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md), si dovrebbe avere un'immagine Nginx nel registro contenitori. Come parte delle istruzioni sarà stato specificato uno spazio dei nomi per l'immagine. Nell'esempio seguente il comando esegue il push dell'immagine NGinx nel repository "samples":

```
docker push myregistry.azurecr.io/samples/nginx
```
 Registro contenitori di Azure supporta spazi dei nomi dei repository multilivello. Questa funzionalità consente di raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per specifici team operativi o di sviluppo. Per altre informazioni sul repository nei registri contenitori, vedere [Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](container-registry-intro.md).

Per visualizzare i repository del registro contenitori:

1. Accedere al Portale di Azure.
2. Nel pannello **Registro contenitori di Azure** selezionare il registro che si desidera esaminare
3. Nel pannello del registro fare clic su **Repository** per vedere un elenco di tutti i repository e le relative immagini
4. (Facoltativo) Selezionare un'immagine specifica per vedere i tag

![I repository nel portale](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Passaggi successivi
Una volta apprese le nozioni di base, si è pronti per iniziare a usare il proprio registro. È ad esempio possibile iniziare a distribuire immagini di contenitore in un cluster del [servizio contenitore di Azure](https://azure.microsoft.com/documentation/services/container-service/).
