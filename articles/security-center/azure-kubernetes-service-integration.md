---
title: Azure Security Center and Azure Kubernetes Service
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i servizi di Azure Kubernetes
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436186"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Services integration with Security Center

Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni containerizzate. 

Usare AKS insieme al livello standard del Centro sicurezza di Azure (vedere [i prezzi)](security-center-pricing.md)per ottenere una visibilità più approfondita dei nodi AKS, del traffico cloud e dei controlli di sicurezza.

Il Centro sicurezza offre vantaggi in termini di sicurezza ai cluster AKS utilizzando i dati già raccolti dal nodo master AKS. 

![Panoramica di alto livello del Centro sicurezza di Azure e del servizio Azure Kubernetes (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Insieme, questi due strumenti formano la migliore offerta di sicurezza Kubernetes nativa per il cloud. 

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'utilizzo dei due servizi insieme fornisce:

* Consigli sulla **sicurezza:** il Centro sicurezza identifica le risorse AKS e le classifica: dai cluster alle singole macchine virtuali. È quindi possibile visualizzare le raccomandazioni per la sicurezza per risorsa. Per ulteriori informazioni, vedere le raccomandazioni relative ai contenitori [nell'elenco di riferimento delle raccomandazioni](recommendations-reference.md#recs-containers). 

* **Protezione avanzata** dell'ambiente: il Centro sicurezza monitora costantemente la configurazione dei cluster Kubernetes e delle configurazioni Docker. Genera quindi raccomandazioni sulla sicurezza che riflettono gli standard del settore.

* **Protezione in fase** di esecuzione: tramite l'analisi continua delle origini AKS seguenti, il Centro sicurezza avvisa l'utente in caso di minacce e attività dannose rilevate a livello di cluster host *e* AKS:
    * Eventi di sicurezza non elaborati, ad esempio dati di rete e creazione di processi
    * Il registro di controllo di Kubernetes

    Per altre informazioni, vedere [protezione dalle minacce per i contenitori](threat-protection.md#azure-containers) di AzureFor more information, see threat protection for Azure containers

    Per l'elenco dei possibili avvisi, vedere queste sezioni nella tabella di riferimento degli avvisi: Avvisi a livello di [cluster AKS](alerts-reference.md#alerts-akscluster) e Avvisi a [livello di host contenitore.](alerts-reference.md#alerts-containerhost)  

![Centro sicurezza di Azure e il servizio Azure Kubernetes (AKS) in modo più dettagliatoAzure Security Center and Azure Kubernetes Service (AKS) in more detail](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alcuni dei dati analizzati dal Centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza dei contenitori del Centro sicurezza, vedere:To learn more about security features del Security Center, see:

* [Sicurezza di Azure e sicurezza dei contenitoriAzure Security Center and container security](container-security.md)

* [Integrazione con Registro Azure Container](azure-container-registry-integration.md)

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - Descrive i criteri dei dati dei servizi Microsoft (inclusi Azure, Intune e Office 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che influiscono sui dati
