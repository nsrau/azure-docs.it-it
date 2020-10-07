---
title: Introduzione ad Azure Red Hat OpenShift
description: Informazioni sulle funzionalità e sui vantaggi di Microsoft Azure Red Hat OpenShift per la distribuzione e la gestione di applicazioni basate su contenitori.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "82628521"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Il servizio Microsoft *Azure Red Hat OpenShift* consente di distribuire cluster [OpenShift](https://www.openshift.com/) completamente gestiti.

Azure Red Hat OpenShift estende [Kubernetes](https://kubernetes.io/). Per l'esecuzione di contenitori nell'ambiente di produzione con Kubernetes sono necessari strumenti e risorse aggiuntivi per la gestione dei registri delle immagini, la gestione delle risorse di archiviazione, nonché soluzioni di rete e strumenti di registrazione e monitoraggio, che devono essere sottoposti a controllo delle versioni e test tutti insieme. La creazione di applicazioni basate su contenitori richiede ancora più integrazione con il middleware, i framework, i database e gli strumenti di CI/CD. Azure Red Hat OpenShift riunisce tutto questo in un'unica piattaforma, semplificando le operazioni per i team IT e fornendo ai team delle applicazioni tutti gli elementi da eseguire.

Azure Red Hat OpenShift viene progettato, gestito e supportato congiuntamente da Red Hat e Microsoft per garantire un'esperienza di supporto integrata. Non sono presenti macchine virtuali da gestire e non è necessaria l'applicazione di patch. I nodi master, infrastruttura e applicazione vengono aggiornati e monitorati per conto dell'utente da Red Hat e Microsoft, che provvedono anche ad applicare le patch. I cluster Azure Red Hat OpenShift vengono distribuiti nella sottoscrizione di Azure e inclusi nella fattura di Azure.

È possibile scegliere il proprio registro e le proprie soluzioni di networking, archiviazione e CI/CD oppure usare le soluzioni predefinite per la gestione automatizzata del codice sorgente, la creazione di contenitori e applicazioni, le distribuzioni, la scalabilità, la gestione dell'integrità e altro ancora. Azure Red Hat OpenShift offre un'esperienza di accesso integrata con Azure Active Directory.

Per iniziare, completare l'esercitazione per la [creazione di un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Accesso, sicurezza e monitoraggio

Per migliorare la sicurezza e la gestione, Azure Red Hat OpenShift consente l'integrazione con Azure Active Directory (Azure AD) e l'uso del controllo degli accessi in base al ruolo di Kubernetes. È anche possibile monitorare l'integrità del cluster e delle risorse.

## <a name="cluster-and-node"></a>Cluster e nodo

I nodi Azure Red Hat OpenShift vengono eseguiti in macchine virtuali di Azure. È possibile connettere una risorsa di archiviazione a nodi e pod e aggiornare i componenti cluster.

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni relative ai prerequisiti per Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurare l'ambiente di sviluppo](tutorial-create-cluster.md)
