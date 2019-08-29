---
title: Gestire un cluster del servizio Azure Kubernetes con il dashboard Web
description: Di seguito sono riportate informazioni su come usare il dashboard dell'interfaccia utente Web Kubernetes incorporato per gestire un cluster del servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126875"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Accesso al dashboard di Kubernetes con il servizio Azure Kubernetes

Kubernetes include un dashboard web che può essere utilizzato per le operazioni di gestione di base. Questo dashboard consente di visualizzare lo stato di integrità di base e le metriche per le applicazioni, creare e distribuire servizi e modificare applicazioni esistenti. Questo articolo spiega come accedere al dashboard di Kubernetes tramite l’interfaccia della riga di comando di Azure e quindi mostra alcune operazioni di base del dashboard.

Per altre informazioni sul dashboard di Kubernetes, vedere [Dashboard dell'interfaccia utente Web di Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio Azure Kubernetes e che sia stata stabilita una connessione `kubectl` al cluster. Se è necessario creare un cluster AKS, vedere la [Guida introduttiva di AKS][aks-quickstart].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="start-the-kubernetes-dashboard"></a>Avviare il dashboard Kubernetes

Per avviare il dashboard di Kubernetes, usare il comando [AZ AKS browse][az-aks-browse] . L'esempio seguente apre il dashboard per il cluster denominato *myAKSCluster* in un gruppo di risorse denominato *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Questo comando crea un proxy tra il sistema di sviluppo e l'API Kubernetes e apre il dashboard di Kubernetes in un Web browser. Se un Web browser non si apre al dashboard di Kubernetes, copiare e incollare l'indirizzo URL indicato nell'interfaccia della riga di `http://127.0.0.1:8001`comando di Azure, in genere.

![Pagina di accesso del dashboard Web Kubernetes](./media/kubernetes-dashboard/dashboard-login.png)

Sono disponibili le opzioni seguenti per accedere al dashboard del cluster:

* [File kubeconfig][kubeconfig-file]. È possibile generare un file kubeconfig usando [AZ AKS Get-credentials][az-aks-get-credentials].
* Token, ad esempio un token dell' [account di servizio][aks-service-accounts] o un token utente. Nei [cluster abilitati per AAD][aad-cluster], questo token è un token AAD. È possibile usare `kubectl config view` per elencare i token nel file kubeconfig. Per altri dettagli sulla creazione di un token AAD da usare con un cluster AKS, vedere [integrare Azure Active Directory con il servizio Azure Kubernetes usando l'interfaccia della riga di][aad-cluster]comando di Azure.
* L'account del servizio dashboard predefinito, che viene usato se si fa clic su *Ignora*.

> [!WARNING]
> Non esporre mai il dashboard di Kubernetes pubblicamente, indipendentemente dal metodo di autenticazione usato.
> 
> Quando si configura l'autenticazione per il dashboard di Kubernetes, è consigliabile usare un token sull'account del servizio dashboard predefinito. Un token consente a ogni utente di usare le proprie autorizzazioni. L'utilizzo dell'account del servizio dashboard predefinito può consentire a un utente di ignorare le proprie autorizzazioni e utilizzare l'account del servizio.
> 
> Se si sceglie di usare l'account del servizio dashboard predefinito e il cluster AKS usa il controllo degli accessi in base al ruolo, è necessario creare un *ClusterRoleBinding* prima di poter accedere correttamente al dashboard. Per impostazione predefinita, il dashboard Kubernetes viene distribuito con accesso in lettura minimo e visualizza errori di accesso di Controllo degli accessi in base al ruolo. Un amministratore del cluster può scegliere di concedere accesso aggiuntivo all'account del servizio *kubernetes-dashboard*. Questo tuttavia può costituire un vettore per l'escalation dei privilegi. È anche possibile integrare l'autenticazione di Azure Active Directory per fornire un livello di accesso più granulare.
>
> Per creare un'associazione, usare il comando [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] come illustrato nell'esempio seguente. **Questo binding di esempio non applica alcun componente di autenticazione aggiuntivo e può causare un utilizzo non sicuro.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> È ora possibile accedere al dashboard Kubernetes nel cluster con abilitazione RBAC. Per avviare il dashboard di Kubernetes, usare il comando [AZ AKS browse][az-aks-browse] come descritto in dettaglio nel passaggio precedente.
>
> Se il cluster non usa il controllo degli accessi in base al ruolo, non è consigliabile creare un *ClusterRoleBinding*.
> 
> Per altre informazioni sull'uso dei diversi metodi di autenticazione, vedere il wiki del dashboard Kubernetes sui [controlli di accesso][dashboard-authentication].

Dopo aver scelto un metodo per l'accesso, viene visualizzato il dashboard Kubernetes. Se si sceglie di usare il *token* o *Skip*, il dashboard di Kubernetes utilizzerà le autorizzazioni dell'utente attualmente connesso per accedere al cluster.

![Pagina Panoramica del dashboard Web di Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Creare un'applicazione

Per vedere come il dashboard di Kubernetes può ridurre la complessità delle attività di gestione, è possibile creare un'applicazione. È possibile creare un'applicazione dal dashboard di Kubernetes fornendo input di testo, un file YAML, o tramite una procedura guidata per la grafica.

Per rimuovere un'applicazione, completare questi passaggi:

1. Nella finestra superiore destra scegliere il pulsante **Crea**.
1. Per usare la procedura guidata per la grafica **Creare un'app**.
1. Fornire un nome per la distribuzione, ad esempio *nginx*
1. Immettere il nome dell'immagine del contenitore da usare, ad esempio *nginx:1.15.5*
1. Per esporre la porta 80 al traffico Web, si crea un servizio Kubernetes. In **Servizio** selezionare **Esterna** e immettere **80** sia per la porta che per la porta di destinazione.
1. Quando si è pronti, selezionare **Distribuisci** per creare l'app.

![Distribuire un'app nel dashboard Web di Kubernetes](./media/kubernetes-dashboard/create-app.png)

L'assegnazione di un indirizzo IP pubblico esterno al servizio di Kubernetes richiede uno o due minuti. Sul lato sinistro, sotto **Discovery and Load Balancing** (Individuazione e bilanciamento del carico) selezionare **Servizi**. Il servizio dell'applicazione è elencato, inclusi gli *endpoint esterni*, come illustrato nell'esempio seguente:

![Visualizzare l'elenco di servizi ed endpoint](./media/kubernetes-dashboard/view-services.png)

Selezionare l'indirizzo dell'endpoint per aprire una finestra del Web browser alla pagina NGINX predefinita:

![Visualizzare la pagina NGINX predefinita dell'applicazione distribuita](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visualizzare le informazioni sul pod

Il dashboard di Kubernetes può fornire metriche di monitoraggio di base e informazioni per la risoluzione dei problemi, ad esempio, i log.

Per altre informazioni sui pod delle applicazioni, selezionare **Pod** nel menu a sinistra. Verrà visualizzato un elenco dei pod disponibili. Scegliere il pod *nginx* per visualizzare informazioni quali il consumo di risorse:

![Visualizzare le informazioni sul pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Modificare l'applicazione

Oltre che per la creazione e la visualizzazione di applicazioni, è possibile usare il dashboard di Kubernetes per modificare e aggiornare le distribuzioni delle applicazioni. Per fornire ridondanza aggiuntiva per l'applicazione, è possibile aumentare il numero di repliche NGINX.

Per modificare una distribuzione:

1. Selezionare **Distribuzioni** dal menu a sinistra, quindi scegliere la distribuzione *nginx*.
1. Selezionare **Modifica** nella barra di spostamento in alto a destra.
1. Individuare il valore `spec.replica`, vicino alla riga 20. Per aumentare il numero di repliche per l'applicazione, impostare il valore da *1* a *3*.
1. Selezionare **Update** (Aggiorna) al termine.

![Modificare la distribuzione per aggiornare il numero di repliche](./media/kubernetes-dashboard/edit-deployment.png)

La creazione di nuovi pod all'interno del set di repliche richiede qualche secondo. Nel menu a sinistra scegliere **Set di repliche**, quindi scegliere il set di repliche *nginx*. L'elenco dei pod ora riflette il numero di repliche aggiornato, come illustrato nell'output di esempio seguente:

![Visualizzare le informazioni sul set di repliche](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul dashboard di Kubernetes, vedere il [Dashboard dell'interfaccia utente Web di Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
