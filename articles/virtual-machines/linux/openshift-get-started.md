---
title: Panoramica di OpenShift in Azure | Microsoft Docs
description: Una panoramica di OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542425"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti incentrati sugli sviluppatori e sulle operazioni che consentono di:

- Sviluppo rapido di applicazioni.
- Semplicità di distribuzione e scalabilità.
- Gestione del ciclo di vita a lungo termine per i team e le applicazioni.

Sono disponibili più versioni di OpenShift.  Queste versioni, solo due sono attualmente disponibili per i clienti possono distribuire in Azure: OpenShift Container Platform e OKD (noto in precedenza come OpenShift Origin).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform è una [versione commerciale](https://www.openshift.com) per uso aziendale di Red Hat supportata da Red Hat. Con questa versione, il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

In quanto cliente è "proprietario" dell'intera piattaforma, è possibile installarlo nel proprio data center locale o in un cloud pubblico (ad esempio Azure).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure Red Hat OpenShift è un'offerta completamente gestita di OpenShift in esecuzione in Azure. Questo servizio è congiuntamente gestito e supportato da Microsoft e Red Hat. Il cluster verrà distribuito nella sottoscrizione di Azure del cliente. Il servizio è pianificato per essere disponibili a livello generale intorno a maggio 2019. Documentazione separata per i servizi gestiti sarà disponibile quando il servizio è a livello generale.

## <a name="okd"></a>OKD

ODK è un progetto upstream [open source](https://www.okd.io/) di OpenShift supportato dalla community. È possibile installare ODK in CentOS o Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-prerequisites.md)
- [Distribuire OpenShift Container Platform in Azure](./openshift-container-platform.md)
- [Distribuire l'offerta sul Marketplace autogestiti piattaforma contenitore OpenShift](./openshift-marketplace-self-managed.md)
- [Deploy OpenShift in Azure Stack](./openshift-azure-stack.md) (Distribuire OpenShift in Azure Stack)
- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
