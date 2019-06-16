---
title: Continuità aziendale e ripristino di emergenza per Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
manager: jeconnoc
ms.openlocfilehash: 69f5bdd80e4cf10db6a530ddfa08a1f26cd42ca0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754423"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Continuità aziendale e ripristino di emergenza per Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Esaminare indicazioni sul ripristino di emergenza per il servizio Azure Kubernetes

Azure Dev Spaces è una funzionalità del servizio Azure Kubernetes. È necessario tenere presenti le linee guida per il ripristino di emergenza nel servizio Azure Kubernetes e considerare se si applicano ai cluster del servizio Azure Kubernetes usato per i Dev Spaces. Per altre informazioni, fare riferimento a [Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Abilitare i Dev Spaces nei cluster del servizio Azure Kubernetes in aree diverse

L'abilitazione di Dev Spaces nei cluster del servizio Azure Kubernetes in aree diverse consente di continuare a usare i Dev Spaces immediatamente dopo un errore di area di Azure.

Per informazioni generali sulle distribuzioni in più aree del servizio Azure Kubernetes, vedere [Pianificazione per la distribuzione in più aree](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Per informazioni sulla distribuzione di un cluster del servizio Azure Kubernetes compatibile con Azure Dev Spaces, vedere [Creare un cluster Kubernetes usando Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Abilitare Dev Spaces tramite il portale di Azure

Fare clic sull'elemento di navigazione **Dev Spaces** sotto le proprietà di ogni cluster nel portale di Azure. Scegliere quindi l'opzione per abilitare Dev Spaces.

![Abilitare Dev Spaces tramite il portale di Azure](../media/common/enable-dev-spaces.jpg)

Ripetere questo processo per ogni cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Abilitare Dev Spaces tramite l'interfaccia della riga di comando di Azure

È anche possibile abilitare Dev Spaces nella riga di comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Distribuire la baseline del proprio team su ogni cluster

Quando si lavora con Dev Spaces, si distribuisce l'intera applicazione negli spazi di sviluppo padre nel cluster Kubernetes. Per impostazione predefinita, viene usato lo spazio `default`. La distribuzione iniziale include tutti i servizi, oltre alle risorse esterne da cui dipendono questi servizi, ad esempio database o le code. Questo è noto come *baseline*. Una volta configurata una baseline dello spazio di sviluppo padre, ripetere ed eseguire il debug di singoli servizi all'interno degli spazi di sviluppo figlio.

È necessario distribuire le versioni più recenti del set di baseline dei servizi per i cluster in più aree. L'aggiornamento dei servizi di baseline in questo modo assicura che è possibile continuare a usare Dev Spaces se si verifica un errore di area di Azure. Ad esempio, se si distribuisce la baseline tramite una pipeline CI/CD, modificare la pipeline in modo che venga distribuita a più cluster in aree diverse.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selezionare il cluster del servizio Azure Kubernetes corretto da usare per Dev Spaces

Dopo aver configurato correttamente un cluster di backup su cui è in esecuzione la baseline del proprio team, è possibile passare rapidamente al cluster di backup in qualsiasi momento. È possibile quindi eseguire di nuovo i singoli servizi su cui si stava lavorando in Dev Spaces.

Selezionare un altro cluster con il comando interfaccia della riga di comando seguente:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

È possibile elencare gli spazi di sviluppo disponibili nel nuovo cluster con il comando seguente:

```cmd
azds space list
```

È possibile creare un nuovo spazio di sviluppo su cui lavorare o selezionare uno spazio di sviluppo esistente, con il comando seguente:

```cmd
azds space select -n <space name>
```

Dopo aver eseguito questi comandi, il cluster selezionato e lo spazio di sviluppo verranno usati per le operazioni relative all'interfaccia della riga di comando successive e per eseguire il debug di progetti che usano l'estensione di Visual Studio Code per Azure Dev Spaces.

Se si usa Visual Studio, è possibile passare al cluster usato da un progetto esistente seguendo questa procedura:

1. Aprire il progetto in Visual Studio.
1. Fare clic con il pulsante destro del mouse sul nome progetto in Esplora soluzioni, quindi fare clic su **Proprietà**.
1. Nel riquadro sinistro fare clic su **Eseguire il debug**.
1. Nella pagina delle proprietà di Debug, fare clic sul **Profilo** elenco a discesa elenco e scegliere **Azure Dev Spaces**.
1. Fare clic sul pulsante **Modifica**.
1. Nella finestra di dialogo visualizzata, selezionare il cluster del servizio Azure Kubernetes che si desidera usare. Se si desidera, scegliere uno spazio di sviluppo su cui lavorare o creare un nuovo spazio di sviluppo, selezionando l'opzione appropriata nell'elenco a discesa **Spazio**.

Una volta selezionato il cluster corretto e lo spazio, è possibile premere F5 per eseguire il servizio negli spazi di sviluppo.

Ripetere questi passaggi per tutti i progetti configurati per usare il cluster originale.

## <a name="access-a-service-on-a-backup-cluster"></a>Accedere a un servizio in un cluster di backup

Se è stato configurato per usare un nome DNS pubblico, il servizio avrà un URL diverso se eseguito in un cluster di backup. I nomi DNS pubblici sono sempre nel formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Se si passa a un cluster diverso, verrà modificato il GUID del cluster ed eventualmente l'area.

Dev Spaces mostra sempre l'URL corretto per il servizio quando è in esecuzione`azds up`, o nella finestra di Output in Visual Studio in **Azure Dev Spaces**.

È anche possibile trovare l'URL eseguendo il comando `azds list-uris`:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Usare questo URL quando si accede al servizio.
