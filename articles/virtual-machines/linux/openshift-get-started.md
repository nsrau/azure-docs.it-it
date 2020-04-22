---
title: Panoramica di OpenShift in Azure
description: Una panoramica di OpenShift in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c338e9a6a793d1c2d0557d70242996175d5a85ed
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759463"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti incentrati sugli sviluppatori e sulle operazioni che consentono di:

- Sviluppo rapido di applicazioni.
- Semplicità di distribuzione e scalabilità.
- Gestione del ciclo di vita a lungo termine per i team e le applicazioni.

Sono disponibili più versioni di OpenShift.  Di queste versioni, solo due sono disponibili oggi per i clienti per la distribuzione in Azure: OpenShift Container Platform e OKD (precedentemente OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift è un'offerta completamente gestita di OpenShift in esecuzione in Azure. Questo servizio è congiuntamente gestito e supportato da Microsoft e Red Hat. Per altre informazioni, vedere la documentazione del [servizio Azure Red Hat OpenShift.For](https://docs.microsoft.com/azure/openshift/) more details, see the Azure Red Hat OpenShift Service documentation.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform è una [versione commerciale](https://www.openshift.com) per uso aziendale di Red Hat supportata da Red Hat. Con questa versione, il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

Poiché i clienti "dirietto" l'intera piattaforma, possono installarla nel data center locale o in un cloud pubblico (ad esempio Azure).

## <a name="okd"></a>OKD

ODK è un progetto upstream [open source](https://www.okd.io/) di OpenShift supportato dalla community. È possibile installare ODK in CentOS o Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-container-platform-3x-prerequisites.md)
- [Distribuire la piattaforma contenitore OpenShift in AzureDeploy OpenShift Container Platform in Azure](./openshift-container-platform-3x.md)
- [Distribuire l'offerta OpenShift Container Platform Self-Managed Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Deploy OpenShift in Azure Stack](./openshift-azure-stack.md) (Distribuire OpenShift in Azure Stack)
- [Attività di post-distribuzione](./openshift-container-platform-3x-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-container-platform-3x-troubleshooting.md)
