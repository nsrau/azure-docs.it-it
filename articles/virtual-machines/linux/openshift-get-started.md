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
ms.openlocfilehash: e254c0c87465b79a37d9a849cc966e34eed01474
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010564"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti incentrati sugli sviluppatori e sulle operazioni che consentono di:

- Sviluppo rapido di applicazioni.
- Semplicità di distribuzione e scalabilità.
- Gestione del ciclo di vita a lungo termine per i team e le applicazioni.

Sono disponibili più versioni di OpenShift.  Di queste versioni, attualmente sono disponibili solo due per i clienti per la distribuzione in Azure: OpenShift container Platform e OKD (in precedenza OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift è un'offerta completamente gestita di OpenShift in esecuzione in Azure. Questo servizio è congiuntamente gestito e supportato da Microsoft e Red Hat. Per altri dettagli, vedere la documentazione del [servizio OpenShift di Azure Red Hat](../../openshift/index.yml) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform è una [versione commerciale](https://www.openshift.com) per uso aziendale di Red Hat supportata da Red Hat. Con questa versione, il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

Poiché i clienti "possiedono" l'intera piattaforma, possono installarla nel Data Center locale o in un cloud pubblico, ad esempio Azure.

## <a name="okd"></a>OKD

ODK è un progetto upstream [open source](https://www.okd.io/) di OpenShift supportato dalla community. È possibile installare ODK in CentOS o Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-container-platform-3x-prerequisites.md)
- [Distribuire OpenShift container Platform in Azure](./openshift-container-platform-3x.md)
- [Distribuisci l'offerta del Marketplace self-Managed OpenShift container Platform](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Deploy OpenShift in Azure Stack](./openshift-azure-stack.md) (Distribuire OpenShift in Azure Stack)
- [Attività di post-distribuzione](./openshift-container-platform-3x-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-container-platform-3x-troubleshooting.md)
