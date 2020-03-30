---
title: Configurare il cluster Azure Dev Spaces per l'uso di Helm 3 (anteprima)Configure your Azure Dev Spaces cluster to use Helm 3 (preview)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Informazioni su come configurare il cluster Dev Spaces per l'utilizzo di Helm 3
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454296"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configurare il cluster Azure Dev Spaces per l'uso di Helm 3 (anteprima)Configure your Azure Dev Spaces cluster to use Helm 3 (preview)

Azure Dev Spaces usa Helm 2 per impostazione predefinita per installare i servizi utente negli spazi di sviluppo nel cluster AKS. È possibile abilitare Azure Dev Spaces per usare Helm 3 anziché Helm 2 per installare i servizi utente negli spazi di sviluppo. Indipendentemente dalla versione usata da Helm Azure Dev Spaces per installare i servizi utente, è possibile continuare a usare il client Helm 2 o 3 per gestire le proprie versioni nello stesso cluster.

Quando si abilita Helm 3, Azure Dev Spaces si comporta in modo diverso quando si installano i servizi utente negli spazi di sviluppo nei modi seguenti:When you enable Helm 3, Azure Dev Spaces behaves differently when installing user services in dev spaces in the following ways:

* Tiller non viene più distribuito nel cluster nello spazio dei nomi *azds.*
* Helm archivia le informazioni sulla versione nello spazio dei nomi in cui è installato un servizio anziché nello spazio dei nomi *azds.*
* Le informazioni sulla versione di Helm 3 rimangono nello spazio dei nomi in cui viene installato un servizio dopo l'eliminazione di un controller.
* È possibile interagire direttamente con qualsiasi versione gestita da Azure Dev Spaces nel cluster usando il client Helm 3.You can directly interact with any release managed by Azure Dev Spaces on your cluster using the Helm 3 client.

In questa guida verrà illustrato come abilitare Helm 3 per Azure Dev Spaces per installare i servizi utente negli spazi di sviluppo.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Registrare la funzionalità di anteprima di Helm3Preview

Per abilitare Azure Dev Spaces per usare Helm 3 per l'installazione dei servizi utente negli spazi di sviluppo, abilitare innanzitutto il flag della funzionalità *Helm3Preview* nella sottoscrizione usando il comando *az feature register:*

> [!WARNING]
> Qualsiasi cluster AKS abilitato Azure Dev Spaces con il flag di funzionalità *Helm3Preview* userà questa esperienza di anteprima. Per continuare ad abilitare spazi di sviluppo di Azure completamente supportati nei cluster AKS, non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione di Azure di test o sviluppo separata per testare le funzionalità di anteprima.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Il completamento della registrazione richiede alcuni minuti. Controllare lo stato della registrazione utilizzando il comando *az feature show:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quando *lo stato* è *Registered*, aggiornare la registrazione di *Microsoft.DevSpaces* utilizzando *az provider register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limitazioni

Le seguenti limitazioni si applicano mentre questa funzione è in anteprima:

* Non è possibile usare questa funzionalità nei cluster AKS con i carichi di lavoro esistenti. È necessario creare un nuovo cluster AKS.

## <a name="create-your-cluster"></a>Creazione del cluster

Creare un nuovo cluster AKS in un'area con questa funzionalità di anteprima. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un nuovo cluster AKS denominato *MyAKS:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Abilitare gli spazi di sviluppo di AzureEnable Azure Dev Spaces

Utilizzare il comando *use-dev-spaces* per abilitare gli spazi di sviluppo nel cluster AKS e seguire le istruzioni visualizzate. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio predefinito.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Verificare che Dev Spaces sia in esecuzione helm 3

Verificare che il tiller non sia in esecuzione elencando le distribuzioni nello spazio dei nomi *azds:*

```cmd
kubectl get deployment -n azds
```

Verificare che *tiller-deploy* non sia in esecuzione nello spazio dei nomi azds. Ad esempio:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md