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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800179"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrazione dei servizi Kubernetes di Azure con il Centro sicurezza

Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori. 

Se si usa il livello standard del Centro sicurezza di Azure, è possibile aggiungere il bundle AKS (vedere i [prezzi](security-center-pricing.md)) per ottenere una visibilità più approfondita per i nodi AKS, il traffico cloud e i controlli di sicurezza.

Insieme, il Centro sicurezza e AKS formano la migliore offerta di sicurezza Kubernetes nativa del cloud.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quali sono i componenti della protezione Kubernetes del Centro sicurezza?

Le protezioni del Centro sicurezza per Kubernetes sono fornite da una combinazione di due elementi:

- **Protezione dalle minacce del Centro sicurezza di Azure per le macchine virtuali** : con lo stesso agente log Analytics usato dal centro sicurezza in altre macchine virtuali, il Centro sicurezza può visualizzare i problemi di sicurezza che si verificano nei nodi AKS. L'agente monitora anche le analisi specifiche del contenitore.

- **Bundle Kubernetes facoltativo del Centro sicurezza di Azure** : il bundle Kubernetes riceve i log e le informazioni dal sottosistema Kubernetes tramite il servizio AKS. Questi log sono già disponibili in Azure tramite il servizio AKS. Quando si Abilita il bundle Kubernetes del Centro sicurezza, si concede l'accesso al centro sicurezza ai log. Il Centro sicurezza offre quindi vantaggi di sicurezza ai cluster AKS usando i dati già raccolti dal nodo master AKS. Alcuni dei dati analizzati dal centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.

    ![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Quali protezioni sono disponibili?

L'utilizzo combinato dei due servizi fornisce:

* **Raccomandazioni sulla sicurezza** : il Centro sicurezza identifica le risorse AKS e le categorizza: da cluster a singole macchine virtuali. È quindi possibile visualizzare le raccomandazioni di sicurezza per ogni risorsa. Per ulteriori informazioni, vedere i suggerimenti sui contenitori nell' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-containers). 

* Protezione **avanzata dell'ambiente** : il Centro sicurezza monitora costantemente la configurazione dei cluster Kubernetes e delle configurazioni di Docker. Genera quindi raccomandazioni sulla sicurezza che riflettono gli standard di settore.

* **Protezione** in fase di esecuzione: grazie all'analisi continua delle fonti AKS seguenti, il Centro sicurezza segnala le minacce e le attività dannose rilevate a livello di host *e* del cluster AKS. [Altre informazioni sulla protezione dalle minacce per i contenitori](threat-protection.md#azure-containers).


     

![Centro sicurezza di Azure e Azure Kubernetes Service (AKS) in modo più dettagliato](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>Domande frequenti su AKS con Centro sicurezza

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Posso comunque ottenere le protezioni AKS senza l'agente di Log Analytics?

Come indicato in precedenza, il bundle Kubernetes facoltativo fornisce protezioni a livello di cluster, l'agente Log Analytics del livello standard del Centro sicurezza di Azure protegge i nodi. 

È consigliabile distribuire entrambi, per la protezione più completa possibile.

Se si sceglie di non installare l'agente negli host, si riceverà solo un subset dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con Registro Azure Container](azure-container-registry-integration.md)

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : descrive i criteri per i dati dei servizi Microsoft (inclusi Azure, Intune e Microsoft 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che interessano i dati
