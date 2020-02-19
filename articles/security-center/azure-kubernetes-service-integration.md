---
title: Centro sicurezza di Azure e servizio Azure Kubernetes
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
ms.openlocfilehash: 9d8c5f98cfd8b4b3831bcbd7e65285f93e6c323f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441953"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integrazione dei servizi Kubernetes di Azure con il Centro sicurezza (anteprima)
Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori. 

USA AKS insieme al livello standard del Centro sicurezza di Azure (Vedi i prezzi) per ottenere una maggiore visibilità [sui](security-center-pricing.md)nodi AKS, sul traffico cloud e sui controlli di sicurezza.

Il Centro sicurezza offre vantaggi di sicurezza ai cluster AKS usando i dati già raccolti dal nodo master AKS. 

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Insieme, questi due strumenti costituiscono la migliore offerta di sicurezza Kubernetes nativa del cloud. 

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'utilizzo combinato dei due servizi fornisce:

* **Raccomandazioni sulla sicurezza** : il Centro sicurezza identifica le risorse AKS e le categorizza: da cluster a singole macchine virtuali. È quindi possibile visualizzare le raccomandazioni di sicurezza per ogni risorsa. Per ulteriori informazioni, vedere i suggerimenti sui contenitori nell' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-computeapp). 

    > [!NOTE]
    > Se il nome di una raccomandazione del Centro sicurezza termina con un tag "(anteprima)", si riferisce alla natura dell'anteprima della raccomandazione, non alla funzionalità.

* Protezione **avanzata dell'ambiente** : il Centro sicurezza monitora costantemente la configurazione dei cluster Kubernetes e delle configurazioni di Docker. Genera quindi raccomandazioni sulla sicurezza che riflettono gli standard di settore.

* **Protezione** in fase di esecuzione: tramite l'analisi continua delle fonti AKS seguenti, il Centro sicurezza avvisa l'utente di minacce e attività dannose rilevate a livello di host *e* cluster AKS:
    * Eventi di sicurezza non elaborati, ad esempio i dati di rete e la creazione di processi
    * Log di controllo di Kubernetes

    Per altre informazioni, vedere [rilevamento delle minacce per i contenitori di Azure](security-center-alerts-compute.md#azure-containers-)

    Per l'elenco dei possibili avvisi, vedere le sezioni seguenti nella tabella di riferimento degli avvisi: [avvisi a livello di cluster AKS](alerts-reference.md#alerts-akscluster) e [avvisi a livello di host del contenitore](alerts-reference.md#alerts-containerhost).  

![Centro sicurezza di Azure e Azure Kubernetes Service (AKS) in modo più dettagliato](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alcuni dei dati analizzati dal centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con Container Registry di Azure](azure-container-registry-integration.md)

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : descrive i criteri per i dati dei servizi Microsoft (inclusi Azure, Intune e Office 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che interessano i dati