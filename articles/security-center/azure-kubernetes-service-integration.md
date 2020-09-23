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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977271"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrazione dei servizi Kubernetes di Azure con il Centro sicurezza

Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori. 

Abilita Azure **Defender per Kubernetes** per ottenere una visibilità più approfondita per i nodi AKS, il traffico cloud e i controlli di sicurezza.

Insieme, il Centro sicurezza e AKS formano la migliore offerta di sicurezza Kubernetes nativa del cloud.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quali sono i componenti della protezione Kubernetes del Centro sicurezza?

Le protezioni del Centro sicurezza per Kubernetes sono fornite da una combinazione di due elementi:

- **Protezione dalle minacce del Centro sicurezza di Azure per le macchine virtuali** : con lo stesso agente log Analytics usato dal centro sicurezza in altre macchine virtuali, il Centro sicurezza può visualizzare i problemi di sicurezza che si verificano nei nodi AKS. L'agente monitora anche le analisi specifiche del contenitore.

- **Piano facoltativo di Azure Defender per Kubernetes del Centro sicurezza di Azure** : il piano Kubernetes riceve log e informazioni dal sottosistema Kubernetes tramite il servizio AKS. Questi log sono già disponibili in Azure tramite il servizio AKS. Quando si Abilita Azure Defender per Kubernetes, si concede l'accesso al centro sicurezza ai log. Il Centro sicurezza offre quindi vantaggi di sicurezza ai cluster AKS usando i dati già raccolti dal nodo master AKS. Alcuni dei dati analizzati dal centro sicurezza di Azure dall'ambiente Kubernetes possono contenere informazioni riservate.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con Registro Azure Container](azure-container-registry-integration.md)

* [Gestione dei dati in Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : descrive i criteri per i dati dei servizi Microsoft (inclusi Azure, Intune e Microsoft 365), i dettagli della gestione dei dati di Microsoft e i criteri di conservazione che interessano i dati
