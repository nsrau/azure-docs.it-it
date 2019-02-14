---
title: Raccomandazioni per i contenitori nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni visualizzate nel Centro sicurezza di Azure per proteggere i contenitori.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112581"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure

Quando si esegue la migrazione di applicazioni monolitiche per eseguire applicazioni native del cloud, incluse in contenitori, di importanza cruciale nell'ambiente di produzione, è possibile sfruttare le funzionalità dei contenitori, come quelle per l'aggiornamento e la distribuzione facili e veloci. Poiché il numero di contenitori distribuiti è in continua crescita, sono necessarie soluzioni di sicurezza per offrire visibilità sullo stato di sicurezza dei contenitori e aiutare a proteggerli dalle minacce.

Il Centro sicurezza di Azure offre le funzionalità seguenti per consentire di proteggere i contenitori:

- **Visibilità dei contenitori ospitati in macchine virtuali Linux IaaS**<br>Nel Centro sicurezza di Azure la scheda dei contenitori visualizza tutte le macchine virtuali distribuite con Docker. Quando si esaminano i problemi di sicurezza in una macchina virtuale, il Centro sicurezza visualizza informazioni aggiuntive correlate ai contenitori nella macchina, ad esempio la versione di Docker e il numero di immagini in esecuzione sull'host.

    ![Scheda dei contenitori](./media/security-center-container-recommendations/docker-recommendation.png)


- **Raccomandazioni per la sicurezza basate sul benchmark CIS per Docker**<br>Il Centro sicurezza analizza le configurazioni di Docker e mostra le configurazioni errate, fornendo un elenco di tutte le regole non riuscite che sono state valutate. Visualizza inoltre linee guida per consentire di risolvere rapidamente questi problemi e risparmiare tempo. Il Centro sicurezza valuta costantemente le configurazioni di Docker e ne visualizza lo stato più recente.

    ![Scheda dei contenitori](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Rilevamento in tempo reale delle minacce per i contenitori**<br> Il Centro sicurezza offre una funzionalità di rilevamento in tempo reale delle minacce per i contenitori su macchine virtuali Linux con il componente AuditD. Gli avvisi identificano diverse attività sospette di Docker, ad esempio la creazione di un contenitore con privilegi nell'host, un'indicazione della presenza di un server SSH (Secure Shell) in esecuzione all'interno di un contenitore Docker o l'uso di cryptominer. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

    ![Scheda dei contenitori](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Consigli
Usare le tabelle seguenti come riferimento per conoscere i contenitori disponibili ospitati in computer IaaS Linux e la valutazione della sicurezza delle loro configurazioni Docker.

| Recommendation | DESCRIZIONE | Correzione |
| --- | --- | --- |
|Correggere le vulnerabilità nelle configurazioni di sicurezza dei contenitori |Correggere le vulnerabilità nelle configurazioni di sicurezza dei contenitori basandosi sulle procedure consigliate per la configurazione.| Per correggere le vulnerabilità nelle configurazioni di sicurezza dei contenitori:<br>1. Esaminare l'elenco delle regole non riuscite.<br>2. Correggere ogni regola secondo le istruzioni specificate.|


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.

