---
title: Gestire un cluster Azure Kubernetes con l'interfaccia utente Web
description: Uso del dashboard Kubernetes nel servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
<<<<<<< HEAD
ms.openlocfilehash: 47608993320d987822422ea103e4e2043de2d2e8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
=======
<<<<<<< HEAD
ms.openlocfilehash: 47608993320d987822422ea103e4e2043de2d2e8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
=======
ms.openlocfilehash: c830400ca1ccd7273ba3acfbd4f17eb2081add87
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
>>>>>>> ef264d3823deace652e9de26708b3ff46548277e
>>>>>>> 3cdcfb9758822206261533a254367aa0a136c59e
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Dashboard di Kubernetes con il servizio contenitore di Azure

Per avviare il dashboard di Kubernetes, è possibile usare l'interfaccia della riga di comando di Azure. Questo documento illustra la procedura per avviare il dashboard di Kubernetes con l'interfaccia della riga di comando di Azure, oltre ad alcune operazioni di base con il dashboard. Per altre informazioni sul dashboard di Kubernetes, vedere [Web UI (Dashboard) (Interfaccia utente Web - Dashboard)][kubernetes-dashboard].

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio contenitore di Azure e che sia stata stabilita una connessione kubectl al cluster. Se sono necessari questi elementi, vedere la [guida introduttiva al servizio contenitore di Azure][aks-quickstart].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.27 o successiva. Eseguire az --version per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Avviare il dashboard di Kubernetes

Usare il comando `az aks browse` per avviare il dashboard di Kubernetes. Quando si esegue questo comando, sostituire il nome del gruppo di risorse e del cluster.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Questo comando crea un proxy tra il sistema di sviluppo e l'API Kubernetes e apre il dashboard di Kubernetes in un Web browser.

## <a name="run-an-application"></a>Eseguire un'applicazione

Nel dashboard di Kubernetes fare clic sul pulsante **Create** (Crea) nella finestra in alto a destra. Assegnare il nome `nginx` alla distribuzione e immettere `nginx:latest` per il nome delle immagini. In **Service** (Servizio) selezionare **External** (External) e immettere `80` sia per la porta che per la porta di destinazione.

Quando si è pronti, fare clic su **Deploy** (Distribuisci) per creare la distribuzione.

![Finestra di dialogo Kubernetes Service Create (Crea servizio Kubernetes)](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Visualizzare l'applicazione

Lo stato dell'applicazione può essere visualizzato nel dashboard di Kubernetes. Quando l'applicazione è in esecuzione, accanto a ogni componente compare una casella di controllo verde.

![Podcast Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Per altre informazioni sui pod delle applicazioni, fare clic su **Pods** (Pod) nel menu a sinistra e quindi selezionare il pod **NGINX**. Qui è possibile visualizzare informazioni specifiche del pod, come il consumo di risorse.

![Risorse Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Per trovare l'indirizzo IP dell'applicazione, fare clic su **Services** (Servizi) nel menu a sinistra e quindi selezionare il servizio **NGINX**.

![Visualizzazione di nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Modificare l'applicazione

Oltre che per la creazione e la visualizzazione di applicazioni, è possibile usare il dashboard di Kubernetes per modificare e aggiornare le distribuzioni delle applicazioni.

Per modificare una distribuzione, fare clic su **Deployments** (Distribuzioni) nel menu a sinistra e quindi selezionare la distribuzione **NGINX**. Infine, selezionare **Modifica** nella barra di spostamento in alto a destra.

![Modifica di Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Individuare il valore `spec.replica`, che dovrebbe essere 1, e modificarlo in 3. In questo modo, il conteggio di repliche della distribuzione NGINX viene aumentato da 1 a 3.

Selezionare **Update** (Aggiorna) al termine.

![Modifica di Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul dashboard di Kubernetes, vedere la documentazione di Kubernetes.

> [!div class="nextstepaction"]
> [Dashboard dell'interfaccia utente Web di Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli