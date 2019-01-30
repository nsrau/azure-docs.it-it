---
title: Distribuzione di Kubernetes in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire Kubernetes in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 9d542f7c2cdeb20806a2db98005ffc03cf72144c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238017"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Distribuzione di Kubernetes in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

È possibile seguire i passaggi descritti in questo articolo per distribuire e configurare le risorse di Kubernetes in un'unica operazione coordinata. La procedura Usa un modello di soluzione di Azure Resource Manager. Sarà necessario per raccogliere le informazioni necessarie relative all'installazione di Azure Stack, generare il modello e quindi distribuire il cloud. Il modello di Azure Stack non usa lo stesso servizio AKS gestito disponibile in Azure globale.

## <a name="kubernetes-and-containers"></a>Contenitori e Kubernetes

È possibile installare Kubernetes usando i modelli di Azure Resource Manager generati dal motore del servizio contenitore di AZURE in Azure Stack. [Kubernetes](https://kubernetes.io) è un sistema open source per automatizzare la distribuzione, ridimensionamento e la gestione delle applicazioni nei contenitori. Oggetto [contenitore](https://www.docker.com/what-container) in un'immagine. L'immagine del contenitore è simile a una macchina virtuale, tuttavia, a differenza di una macchina virtuale, il contenitore include solo le risorse che necessarie per eseguire un'applicazione, ad esempio il codice, eseguire il codice, librerie specifiche e le impostazioni di runtime.

È possibile usare Kubernetes per:

- Sviluppare applicazioni con architettura altamente scalabile, aggiornabile, che possono essere distribuite in pochi secondi. 
- Semplificare la progettazione dell'applicazione e migliorare l'affidabilità da applicazioni diverse di Helm. [Helm](https://github.com/kubernetes/helm) è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes.
- Facilmente monitorare e diagnosticare l'integrità delle applicazioni con scalabilità e funzionalità di aggiornamento.

Ti verrà addebitata solo per l'utilizzo di calcolo necessario per i nodi che supportano il cluster. Per altre informazioni, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes"></a>Distribuzione di Kubernetes

I passaggi per distribuire un cluster Kubernetes in Azure Stack varia in base al servizio di gestione di identità. Verificare la soluzione di gestione di identità usata per l'installazione di Azure Stack. Contattare l'amministratore di Azure Stack per verificare il servizio di gestione di identità.

- **Azure Active Directory (Azure AD)**  
Per istruzioni sull'installazione del cluster quando si usa Azure AD, vedere [distribuzione di Kubernetes in Azure Stack tramite Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory Federated Services (ADFS)**  
Per istruzioni sull'installazione del cluster quando si usa AD FS, vedere [distribuzione di Kubernetes in Azure Stack usando Active Directory Federated Services (ADFS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Connetti al cluster

Ora possibile connettersi al cluster. Il master è reperibile nel gruppo di risorse cluster e viene denominato `k8s-master-<sequence-of-numbers>`. Usare un client SSH per connettersi al master. Sul master, è possibile usare **kubectl**, il client della riga di comando di Kubernetes per gestire il cluster. Per istruzioni, vedere [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

È anche possibile trovare il **Helm** utili per l'installazione e distribuzione di App nel cluster di gestione pacchetti. Per istruzioni sull'installazione e usare Helm con il cluster, vedere [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un Kubernetes nel Marketplace (per l'operatore di Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Distribuzione di Kubernetes in Azure Stack tramite Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Distribuzione di Kubernetes in Azure Stack tramite Active Directory Federated Services (ADFS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
