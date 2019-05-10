---
title: Introduzione ad Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni sulle funzionalità e sui vantaggi di Microsoft Azure Red Hat OpenShift per la distribuzione e la gestione di applicazioni basate su contenitori.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 6121c0f654a61a147e84f0697f3ddb06b7c5db92
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079949"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Il servizio Microsoft *Azure Red Hat OpenShift* consente di distribuire cluster [OpenShift](https://www.openshift.com/) completamente gestiti.

Azure Red Hat OpenShift estende [Kubernetes](https://kubernetes.io/). L'esecuzione di contenitori in produzione con Kubernetes richiede risorse e strumenti aggiuntivi, ad esempio un registro immagini, funzionalità di gestione dell'archiviazione, soluzioni di networking e strumenti di registrazione e monitoraggio, tutti da sottoporre a controllo delle versioni e test. La creazione di applicazioni basate su contenitori richiede ancora più integrazione con il middleware, i framework, i database e gli strumenti di CI/CD. Azure Red Hat OpenShift riunisce tutto questo in un'unica piattaforma, semplificando le operazioni per i team IT e fornendo ai team delle applicazioni tutti gli elementi da eseguire.

Azure Red Hat OpenShift viene progettato, gestito e supportato congiuntamente da Red Hat e Microsoft per garantire un'esperienza di supporto integrata. Non sono presenti macchine virtuali da gestire e non è necessaria l'applicazione di patch. I nodi master, infrastruttura e applicazione vengono aggiornati e monitorati per conto dell'utente da Red Hat e Microsoft, che provvedono anche ad applicare le patch. I cluster Azure Red Hat OpenShift vengono distribuiti nella sottoscrizione di Azure e inclusi nella fattura di Azure.

È possibile scegliere il proprio registro e le proprie soluzioni di networking, archiviazione e CI/CD oppure usare le soluzioni predefinite per la gestione automatizzata del codice sorgente, la creazione di contenitori e applicazioni, le distribuzioni, la scalabilità, la gestione dell'integrità e altro ancora. Azure Red Hat OpenShift offre un'esperienza di accesso integrata con Azure Active Directory.

Per iniziare, completare l'esercitazione per la [creazione di un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Accesso, sicurezza e monitoraggio

Per migliorare la sicurezza e la gestione, Azure Red Hat OpenShift consente l'integrazione con Azure Active Directory (Azure AD) e l'uso del controllo degli accessi in base al ruolo di Kubernetes. È anche possibile monitorare l'integrità del cluster e delle risorse.

## <a name="cluster-and-node"></a>Cluster e nodo

I nodi Azure Red Hat OpenShift vengono eseguiti in macchine virtuali di Azure. È possibile connettere una risorsa di archiviazione a nodi e pod, aggiornare i componenti cluster e usare GPU.

## <a name="virtual-networks-and-ingress"></a>Reti virtuali e ingresso

È possibile distribuire un cluster Azure Red Hat OpenShift in una rete virtuale esistente. In questa configurazione ogni pod del cluster ottiene un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod nel cluster e con altri nodi della rete virtuale. I pod possono inoltre connettersi ad altri servizi in una rete virtuale con peering e a reti locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) o connessioni VPN da sito a sito (S2S).

Per altre informazioni, vedere [Creare un cluster Microsoft Red Hat OpenShift in Azure](tutorial-create-cluster.md).

## <a name="kubernetes-certification"></a>Certificazione Kubernetes

Il servizio Azure Red Hat OpenShift ha ottenuto la certificazione CNCF per la conformità a Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni relative ai prerequisiti per Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurare l'ambiente di sviluppo](howto-setup-environment.md)