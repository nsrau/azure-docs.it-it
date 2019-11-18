---
title: Centro sicurezza di Azure e servizio Azure Kubernetes | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i servizi Kubernetes di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123971"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integrazione dei servizi Kubernetes di Azure con il Centro sicurezza (anteprima)
Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori. 

USA AKS insieme al livello standard del Centro sicurezza di Azure (Vedi i prezzi) per ottenere una maggiore visibilità [sui](security-center-pricing.md)nodi AKS, sul traffico cloud e sui controlli di sicurezza.

Il Centro sicurezza offre vantaggi di sicurezza ai cluster AKS usando i dati già raccolti dal nodo master AKS. 

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Insieme, questi due strumenti costituiscono la migliore offerta di sicurezza Kubernetes nativa del cloud. 

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'utilizzo combinato dei due servizi fornisce:

* **Raccomandazioni sulla sicurezza** : il Centro sicurezza identifica le risorse AKS e le categorizza: da cluster a singole macchine virtuali. È quindi possibile visualizzare le raccomandazioni di sicurezza per ogni risorsa. Per ulteriori informazioni, vedere [come implementare le raccomandazioni sulla sicurezza](security-center-recommendations.md). 

    > [!NOTE]
    > Se il nome di un'indicazione del Centro sicurezza termina con un tag "(anteprima)", si riferisce alla natura dell'anteprima dell'indicazione; non è la funzionalità.

* Protezione **avanzata dell'ambiente** : il Centro sicurezza monitora costantemente la configurazione dei cluster Kubernetes e genera raccomandazioni sulla sicurezza che riflettono gli standard di settore.

* **Protezione** in fase di esecuzione: tramite l'analisi continua delle origini AKS seguenti, il Centro sicurezza avvisa l'utente di minacce e attività dannose rilevate a livello di cluster host *e* AKS (per altre informazioni, vedere [servizio contenitore di Azure). ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * Eventi di sicurezza non elaborati, ad esempio i dati di rete e la creazione di processi
    * Log di controllo di Kubernetes

![Centro sicurezza di Azure e Azure Kubernetes Service (AKS) in modo più dettagliato](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alcuni dei dati analizzati dal centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con Container Registry di Azure](azure-container-registry-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) : descrive le raccomandazioni del Centro sicurezza

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : descrive i criteri per i dati dei servizi Microsoft (inclusi Azure, Intune e Office 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che interessano i dati