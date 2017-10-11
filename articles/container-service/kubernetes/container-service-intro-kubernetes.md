---
title: Introduzione al servizio contenitore di Azure per Kubernetes | Microsoft Docs
description: Il servizio contenitore di Azure per Kubernetes semplifica la distribuzione e la gestione delle applicazioni basate su contenitori in Azure.
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Kubernetes, Docker, contenitori, microservizi, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 92cdbe20e7a2974a734dfed5294c547866050290
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="introduction-to-azure-container-service-for-kubernetes"></a>Introduzione al servizio contenitore di Azure per Kubernetes
Il servizio contenitore di Azure per Kubernetes semplifica la creazione, la configurazione e la gestione di un cluster di macchine virtuali preconfigurate per eseguire le applicazioni nei contenitori. Ciò consente di usare le competenze già acquisite o di attingere da un consistente e crescente bagaglio di competenze a livello di community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzionalità di livello aziendale di Azure mantenendo al tempo stesso la portabilità delle applicazioni tramite Kubernetes e il formato immagine Docker.

## <a name="using-azure-container-service-for-kubernetes"></a>Uso del servizio contenitore di Azure per Kubernetes
L'obiettivo del servizio contenitore di Azure è fornire un ambiente host contenitore tramite tecnologie e strumenti open source, che sono attualmente diffusi fra i nostri clienti. A questo scopo vengono esposti gli endpoint API di Kubernetes standard. Usando questi endpoint standard è possibile usare qualsiasi software riesca a comunicare con un cluster Kubernetes. È ad esempio possibile scegliere [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) o [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Creazione di un cluster Kubernetes con il servizio contenitore di Azure
Per iniziare a usare il servizio contenitore di Azure, distribuire un cluster del servizio contenitore di Azure con l'[interfaccia della riga di comando di Azure 2.0](container-service-kubernetes-walkthrough.md) o tramite il portale (cercare **Servizio contenitore di Azure** nel Marketplace). Gli utenti avanzati che hanno bisogno di un maggiore controllo sui modelli di Azure Resource Manager possono usare il progetto open source [acs-engine](https://github.com/Azure/acs-engine) per compilare un cluster Kubernetes personalizzato e distribuirlo tramite l'interfaccia della riga di comando `az`.

### <a name="using-kubernetes"></a>Uso di Kubernetes
Kubernetes automatizza la distribuzione, il ridimensionamento e la gestione delle applicazioni nei contenitori. Dispone di un set completo di funzionalità tra cui:
* Bin packing automatico
* Riparazione automatica
* Scalabilità orizzontale
* Bilanciamento del carico e rilevamento del servizio
* Implementazioni e ripristini dello stato precedente automatizzati
* Gestione della configurazione e dei segreti
* Orchestrazione dell'archiviazione
* Esecuzione batch

Diagramma dell'architettura di Kubernetes distribuito tramite il servizio contenitore di Azure:

![Servizio contenitore di Azure configurato per l'uso di Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Video

Il supporto per Kubernetes nei servizi contenitore di Azure (Azure Friday, gennaio 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Strumenti per lo sviluppo e la distribuzione di applicazioni in Kubernetes (Azure OpenDev, giugno 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Passaggi successivi

Vedere la [guida introduttiva a Kubernetes](container-service-kubernetes-walkthrough.md) per iniziare a conoscere oggi stesso il servizio contenitore di Azure.