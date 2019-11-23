---
title: Gestire un cluster del servizio Azure Kubernetes con il dashboard Web
description: Di seguito sono riportate informazioni su come usare il dashboard dell'interfaccia utente Web Kubernetes incorporato per gestire un cluster del servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: f150103c8e9534bfd1bb93d20e3d65d715767184
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996956"
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

Questo comando crea un proxy tra il sistema di sviluppo e l'API Kubernetes e apre il dashboard di Kubernetes in un Web browser. Se un Web browser non si apre al dashboard di Kubernetes, copiare e incollare l'indirizzo URL indicato nell'interfaccia della riga di comando di Azure, in genere `http://127.0.0.1:8001`.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Se il cluster del servizio Azure Container utilizza RBAC, è necessario creare un *ClusterRoleBinding* prima di poter accedere correttamente al dashboard. Per impostazione predefinita, il dashboard Kubernetes viene distribuito con accesso in lettura minimo e visualizza errori di accesso di Controllo degli accessi in base al ruolo. Il dashboard Kubernetes attualmente non supporta credenziali specificate dall'utente per determinare il livello di accesso, ma usa i ruoli concessi all'account del servizio. Un amministratore del cluster può scegliere di concedere accesso aggiuntivo all'account del servizio *kubernetes-dashboard*. Questo tuttavia può costituire un vettore per l'escalation dei privilegi. È anche possibile integrare l'autenticazione di Azure Active Directory per fornire un livello di accesso più granulare.
> 
> Per creare un'associazione, usare il comando [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] . Nell'esempio seguente viene illustrato come creare un'associazione di esempio, tuttavia questo binding di esempio non applica alcun componente di autenticazione aggiuntivo e può causare un utilizzo non sicuro. Il dashboard di Kubernetes è aperto a tutti gli utenti con accesso all'URL. Non esporre pubblicamente il dashboard di Kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Per altre informazioni sull'uso dei diversi metodi di autenticazione, vedere il wiki del dashboard Kubernetes sui [controlli di accesso][dashboard-authentication].

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
