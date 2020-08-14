---
title: Gestire un cluster del servizio Azure Kubernetes con il dashboard Web
description: Di seguito sono riportate informazioni su come usare il dashboard dell'interfaccia utente Web Kubernetes incorporato per gestire un cluster del servizio Azure Kubernetes
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 35424c0a9e566a9dfa780c524e23945348335040
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225989"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Accesso al dashboard di Kubernetes con il servizio Azure Kubernetes

Kubernetes include un dashboard web che può essere utilizzato per le operazioni di gestione di base. Questo dashboard consente di visualizzare lo stato di integrità di base e le metriche per le applicazioni, creare e distribuire servizi e modificare applicazioni esistenti. Questo articolo spiega come accedere al dashboard di Kubernetes tramite l’interfaccia della riga di comando di Azure e quindi mostra alcune operazioni di base del dashboard.

Per altre informazioni sul dashboard di Kubernetes, vedere [Web UI (Dashboard) (Interfaccia utente Web - Dashboard)][kubernetes-dashboard]. AKS usa la versione 2,0 e successive del Dashboard Open Source.

> [!WARNING]
> **Il componente aggiuntivo del dashboard AKS è impostato per la deprecazione. Usare invece la [visualizzazione risorse Kubernetes nel portale di Azure (anteprima)][kubernetes-portal] .** 
> * Il dashboard Kubernetes è abilitato per impostazione predefinita per i cluster che eseguono una versione Kubernetes inferiore a 1,18.
> * Il componente aggiuntivo del dashboard verrà disabilitato per impostazione predefinita per tutti i nuovi cluster creati in Kubernetes 1,18 o versione successiva. 
 > * A partire da Kubernetes 1,19 in anteprima, AKS non supporterà più l'installazione dell'addon gestito di Kube-dashboard. 
 > * I cluster esistenti con il componente aggiuntivo abilitato non saranno interessati. Gli utenti continueranno a essere in grado di installare manualmente il Dashboard Open Source come software installato dall'utente.

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo documento presuppongono che sia stato creato un cluster AKS e che sia stata stabilita una `kubectl` connessione con il cluster. Se è necessario creare un cluster AKS, vedere [Guida introduttiva: distribuire un cluster del servizio Azure Kubernetes usando l'interfaccia della][aks-quickstart]riga di comando di Azure.

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.6.0 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Disabilitare il dashboard di Kubernetes

Il componente aggiuntivo Kube-dashboard è **abilitato per impostazione predefinita nei cluster precedenti a K8s 1,18**. Il componente aggiuntivo può essere disabilitato eseguendo il comando seguente.

``` azure-cli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Avviare il dashboard Kubernetes

Per avviare il dashboard di Kubernetes in un cluster, usare il comando [AZ AKS browse][az-aks-browse] . Questo comando richiede l'installazione dell'addon di Kube-dashboard nel cluster, che è incluso per impostazione predefinita nei cluster che eseguono una versione precedente a Kubernetes 1,18.

L'esempio seguente apre il dashboard per il cluster denominato *myAKSCluster* in un gruppo di risorse denominato *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Questo comando crea un proxy tra il sistema di sviluppo e l'API Kubernetes e apre il dashboard di Kubernetes in un Web browser. Se nel dashboard Kubernetes non si apre un Web browser, copiare e incollare l'indirizzo URL indicato nell'interfaccia della riga di comando di Azure, in genere `http://127.0.0.1:8001`.

> [!NOTE]
> Se il dashboard non è visibile `http://127.0.0.1:8001` , è possibile indirizzare manualmente agli indirizzi seguenti. I cluster con 1,16 o versioni successive usano HTTPS e richiedono un endpoint separato.
> * K8s 1,16 o versione successiva: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 e versioni precedenti: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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

> [!IMPORTANT]
> If your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Accedere al dashboard (kubernetes 1.16 +)

> [!IMPORTANT]
> A partire da [v 1.10.1 del dashboard di Kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) o Kubernetes v 1.16 + non è più possibile usare l'account del servizio "Kubernetes-Dashboard" per recuperare le risorse a causa di una [correzione della sicurezza in tale versione](https://github.com/kubernetes/dashboard/pull/3400). Di conseguenza, le richieste senza informazioni di autenticazione restituiscono un errore 401 non autorizzato. Un bearer token recuperato da un account del servizio può comunque essere usato come in questo [esempio di dashboard Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), ma ciò influisce sul flusso di accesso del componente aggiuntivo del dashboard rispetto alle versioni precedenti.
>
>Se si esegue ancora una versione precedente alla 1,16, è comunque possibile concedere le autorizzazioni all'account del servizio "kubernetes-Dashboard", ma questa operazione **non è consigliata**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

La schermata iniziale visualizzata richiede un kubeconfig o un token. Entrambe le opzioni richiedono le autorizzazioni per le risorse per visualizzare tali risorse nel dashboard.

![schermata di accesso](./media/kubernetes-dashboard/login.png)

**Usare un kubeconfig**

Per i cluster Azure AD abilitati e non Azure AD abilitati, è possibile passare un kubeconfig. Verificare che i token di accesso siano validi se i token sono scaduti è possibile aggiornare i token tramite kubectl.

1. Impostare il kubeconfig amministratore con `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Selezionare `Kubeconfig` e fare clic `Choose kubeconfig file` per aprire il selettore file
1. Selezionare il file kubeconfig (il valore predefinito è $HOME/.Kube/config)
1. Fare clic su`Sign In`.

**Usare un token**

1. Per i **cluster non Azure ad abilitati**, eseguire `kubectl config view` e copiare il token associato all'account utente del cluster.
1. Incollare l'opzione token all'accesso.    
1. Fare clic su`Sign In`.

Per i cluster Azure AD abilitati, recuperare il token AAD con il comando seguente. Verificare di aver sostituito il gruppo di risorse e il nome del cluster nel comando.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Una volta completata l'operazione, verrà visualizzata una pagina simile alla seguente.

![Pagina Panoramica del dashboard Web di Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Creare un'applicazione

Per la procedura seguente è necessario che l'utente disponga delle autorizzazioni per le rispettive risorse. 

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

Per altre informazioni sul dashboard di Kubernetes, vedere [Kubernetes Web UI Dashboard][kubernetes-dashboard] (Dashboard dell'interfaccia utente Web di Kubernetes).

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
[kubernetes-portal]: ./kubernetes-portal.md
