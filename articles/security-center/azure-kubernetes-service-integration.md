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
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436186"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrazione dei servizi Kubernetes di Azure con il Centro sicurezza

Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori. 

USA AKS insieme al livello standard del Centro sicurezza di Azure (Vedi i prezzi) per ottenere una maggiore visibilità [sui](security-center-pricing.md)nodi AKS, sul traffico cloud e sui controlli di sicurezza.

Il Centro sicurezza offre vantaggi di sicurezza ai cluster AKS usando i dati già raccolti dal nodo master AKS. 

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Insieme, questi due strumenti costituiscono la migliore offerta di sicurezza Kubernetes nativa del cloud. 

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'utilizzo combinato dei due servizi fornisce:

* **Raccomandazioni sulla sicurezza** : il Centro sicurezza identifica le risorse AKS e le categorizza: da cluster a singole macchine virtuali. È quindi possibile visualizzare le raccomandazioni di sicurezza per ogni risorsa. Per ulteriori informazioni, vedere i suggerimenti sui contenitori nell' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-containers). 

* Protezione **avanzata dell'ambiente** : il Centro sicurezza monitora costantemente la configurazione dei cluster Kubernetes e delle configurazioni di Docker. Genera quindi raccomandazioni sulla sicurezza che riflettono gli standard di settore.

* **Protezione** in fase di esecuzione: tramite l'analisi continua delle fonti AKS seguenti, il Centro sicurezza avvisa l'utente di minacce e attività dannose rilevate a livello di host *e* cluster AKS:
    * Eventi di sicurezza non elaborati, ad esempio i dati di rete e la creazione di processi
    * Log di controllo di Kubernetes

    Per altre informazioni, vedere [protezione dalle minacce per i contenitori di Azure](threat-protection.md#azure-containers)

    Per l'elenco dei possibili avvisi, vedere le sezioni seguenti nella tabella di riferimento degli avvisi: [avvisi a livello di cluster AKS](alerts-reference.md#alerts-akscluster) e [avvisi a livello di host del contenitore](alerts-reference.md#alerts-containerhost).  

![Centro sicurezza di Azure e Azure Kubernetes Service (AKS) in modo più dettagliato](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alcuni dei dati analizzati dal centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con Registro Azure Container](azure-container-registry-integration.md)

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : descrive i criteri per i dati dei servizi Microsoft (inclusi Azure, Intune e Office 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che interessano i dati
