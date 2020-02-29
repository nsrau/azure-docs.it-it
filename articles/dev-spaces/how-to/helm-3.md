---
title: Configurare il cluster di Azure Dev Spaces per l'uso di Helm 3 (Preview)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Informazioni su come configurare il cluster di spazi di sviluppo per l'uso di Helm 3
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202260"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configurare il cluster di Azure Dev Spaces per l'uso di Helm 3 (Preview)

Azure Dev Spaces USA Helm 2 per impostazione predefinita per installare i servizi utente in spazi di sviluppo nel cluster AKS. È possibile abilitare Azure Dev Spaces per usare Helm 3 anziché Helm 2 installando i servizi utente in spazi di sviluppo. Indipendentemente dalla versione di Helm Azure Dev Spaces USA per installare i servizi utente, è possibile continuare a usare il client Helm 2 o 3 per gestire le versioni personali nello stesso cluster.

Quando si Abilita Helm 3, Azure Dev Spaces si comporta in modo diverso quando si installano i servizi utente in spazi di sviluppo nei modi seguenti:

* Il timone non viene più distribuito nel cluster nello spazio dei nomi *azds* .
* Helm archivia le informazioni sulla versione nello spazio dei nomi in cui viene installato un servizio anziché lo spazio dei nomi *azds* .
* Le informazioni sulla versione di Helm 3 rimangono nello spazio dei nomi in cui viene installato un servizio dopo l'eliminazione di un controller.
* È possibile interagire direttamente con qualsiasi versione gestita da Azure Dev Spaces nel cluster usando il client Helm 3.

In questa guida si apprenderà come abilitare Helm 3 per Azure Dev Spaces installare i servizi utente in spazi di sviluppo.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisites

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Registrare la funzionalità di anteprima di Helm3Preview

Per consentire Azure Dev Spaces di usare Helm 3 per l'installazione dei servizi utente in spazi di sviluppo, abilitare prima il flag della funzionalità *Helm3Preview* nella sottoscrizione usando il comando *AZ feature Register* :

> [!WARNING]
> Qualsiasi cluster AKS abilitato Azure Dev Spaces on con il flag della funzionalità *Helm3Preview* utilizzerà questa anteprima. Per continuare ad abilitare Azure Dev Spaces completamente supportati nei cluster AKS, non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione di Azure di test o sviluppo separata per testare le funzionalità in anteprima.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Sono necessari alcuni minuti per il completamento della registrazione. Verificare lo stato della registrazione usando il comando *AZ feature Show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quando lo *stato* è *registrato*, aggiornare la registrazione di *Microsoft. DevSpaces* usando *AZ provider Register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limitazioni

Quando questa funzionalità è in anteprima, si applicano le limitazioni seguenti:

* Non è possibile usare questa funzionalità nei cluster AKS con i carichi di lavoro esistenti. È necessario creare un nuovo cluster AKS.

## <a name="create-your-cluster"></a>Creazione del cluster

Creare un nuovo cluster AKS in un'area con questa funzionalità in anteprima. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un nuovo cluster AKS denominato *MyAKS* nell'area *Stati Uniti centro-meridionali* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Abilita Azure Dev Spaces

Usare il comando *use-dev-Spaces* per abilitare gli spazi di sviluppo nel cluster AKS e seguire le istruzioni. Il comando seguente abilita gli spazi di sviluppo nel cluster *MyAKS* nel gruppo *MyResourceGroup* e crea uno spazio di sviluppo predefinito.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Verificare che gli spazi di sviluppo eseguano Helm 3

Verificare che il timone non sia in esecuzione elencando le distribuzioni nello spazio dei nomi *azds* :

```cmd
kubectl get deployment -n azds
```

Confermare che la *distribuzione* non è in esecuzione nello spazio dei nomi azds. Ad esempio:

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