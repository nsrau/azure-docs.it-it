---
title: Gestire un cluster Azure Kubernetes con l'interfaccia utente Web
description: Di seguito sono riportate informazioni su come usare il dashboard dell'interfaccia utente web Kubernetes incorporato nel servizio Kubernetes di Azure (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307926"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Accesso al dashboard di Kubernetes con il servizio Kubernetes di Azure (AKS)

Kubernetes include un dashboard web che può essere utilizzato per le operazioni di gestione di base. Questo articolo spiega come accedere al dashboard di Kubernetes tramite l’interfaccia della riga di comando di Azure e quindi mostra alcune operazioni di base del dashboard. Per altre informazioni sul dashboard di Kubernetes, vedere [Web UI (Dashboard) (Interfaccia utente Web - Dashboard)][kubernetes-dashboard].

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio Kubernetes di Azure e che sia stata stabilita una connessione `kubectl` al cluster. Se è necessario creare un cluster del servizio Kubernetes di Azure, vedere la [Guida introduttiva al servizio Kubernetes di Azure][aks-quickstart].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.27 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Avviare il dashboard di Kubernetes

Usare il comando [az aks browse][az-aks-browse] per avviare il dashboard di Kubernetes. L'esempio seguente apre il dashboard per il cluster denominato *myAKSCluster* in un gruppo di risorse denominato *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Questo comando crea un proxy tra il sistema di sviluppo e l'API Kubernetes e apre il dashboard di Kubernetes in un Web browser.

### <a name="for-rbac-enabled-clusters"></a>Per i cluster che dispongono dell’abilitazione RBAC

Se il cluster del servizio contenitore di Azure utilizza RBAC, è necessario creare un *ClusterRoleBinding* prima di poter accedere correttamente al dashboard. Per impostazione predefinita, il dashboard Kubernetes viene distribuito con accesso in lettura minimo e visualizza errori di accesso di Controllo degli accessi in base al ruolo. Il dashboard Kubernetes attualmente non supporta credenziali specificate dall'utente per determinare il livello di accesso, ma usa i ruoli concessi all'account del servizio. Un amministratore del cluster può scegliere di concedere accesso aggiuntivo all'account del servizio *kubernetes-dashboard*. Questo tuttavia può costituire un vettore per l'escalation dei privilegi. È anche possibile integrare l'autenticazione di Azure Active Directory per fornire un livello di accesso più granulare.

Per creare un'associazione, usare il comando [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] come mostrato nell'esempio seguente. 

> [!WARNING]
> Questo esempio di associazione non si applica a tutti i componenti di autenticazione aggiuntivi e può condurre a un uso non sicuro. Il dashboard di Kubernetes è aperto a tutti gli utenti con accesso all'URL. Non esporre pubblicamente il dashboard di Kubernetes.
>
> Per ulteriori informazioni sull'uso dei diversi metodi di autenticazione, consultare il wiki del dashboard di Kubernetes sui [controlli di accesso][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

È ora possibile accedere al dashboard Kubernetes nel cluster con abilitazione RBAC. Usare il comando [az aks browse][az-aks-browse] per avviare il dashboard di Kubernetes come descritto nel passaggio precedente.

## <a name="run-an-application"></a>Eseguire un'applicazione

Nel dashboard di Kubernetes fare clic sul pulsante **Create** (Crea) nella finestra in alto a destra. Assegnare il nome `nginx` alla distribuzione e immettere `nginx:latest` per il nome dell'immagine contenitore. In **Service** (Servizio) selezionare **External** (External) e immettere `80` sia per la porta che per la porta di destinazione.

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
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
