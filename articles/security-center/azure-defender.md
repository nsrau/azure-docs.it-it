---
title: Panoramica di Azure Defender e dei piani disponibili
description: Leggere informazioni su piani, misure di protezione e avvisi di Azure Defender. Quindi procedere ad abilitare Azure Defender nelle sottoscrizioni.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448423"
---
# <a name="introduction-to-azure-defender"></a>Introduzione ad Azure Defender

Le funzionalità del Centro sicurezza di Azure riguardano due concetti fondamentali della sicurezza del cloud:

- **Cloud Security Posture Management (CSPM)**
- **Cloud Workload Protection (CWP)**

Le funzionalità CSPM del Centro sicurezza, ad esempio il punteggio di sicurezza e il rilevamento di configurazioni errate della sicurezza nei computer Windows e Linux di Azure, fanno tutte parte dell'esperienza gratuita del Centro sicurezza disponibile per tutti gli utenti di Azure. Usare queste funzionalità CSPM per rafforzare la postura e garantire la conformità alle normative.

**Azure Defender** è la soluzione CWPP (Cloud Workload Protection Platform) integrata nel Centro sicurezza per offrire una protezione avanzata e intelligente dei carichi di lavoro di Azure e ibridi.

Questo è il dashboard di Azure Defender nel Centro sicurezza di Azure:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Esempio del dashboard di Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Quali tipi di risorsa possono essere protetti da Azure Defender?

Azure Defender offre avvisi di sicurezza e protezione avanzata dalle minacce per macchine virtuali, database SQL, contenitori, applicazioni Web, rete e altro ancora.

Quando si abilita Azure Defender nell'area **Prezzi e impostazioni** del Centro sicurezza di Azure, vengono abilitati contemporaneamente tutti i piani seguenti di Defender e vengono fornite difese complete per i livelli di calcolo, dati e servizio dell'ambiente:

- [Azure Defender per server](defender-for-servers-introduction.md)
- [Azure Defender per il servizio app](defender-for-app-service-introduction.md)
- [Azure Defender per Archiviazione](defender-for-storage-introduction.md)
- [Azure Defender per SQL](defender-for-sql-introduction.md)
- [Azure Defender per IoT](defender-for-iot-introduction.md)
- [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)
- [Azure Defender per Key Vault](defender-for-key-vault-introduction.md)

Ogni piano è illustrato separatamente nella documentazione del Centro sicurezza.


## <a name="hybrid-cloud-protection"></a>Protezione del cloud ibrido

Oltre a proteggere l'ambiente di Azure, è possibile aggiungere le funzionalità di Azure Defender all'ambiente cloud ibrido:

- Proteggere i server non Azure
- Proteggere le macchine virtuali in altri cloud (ad esempio AWS e GCP)
- Proteggere i dispositivi IoT

Si otterranno funzionalità personalizzate di intelligence sulle minacce e avvisi in ordine di priorità in base all'ambiente specifico, in modo da potersi concentrare sulle questioni più importanti

Distribuire [Azure Arc](https://azure.microsoft.com/services/azure-arc/) e abilitare Azure Defender per estendere la protezione a macchine virtuali e database SQL locali e multi-cloud. Azure Arc per server è un servizio gratuito, ma i servizi usati nei server con abilitazione di Arc, ad esempio Azure Defender, verranno addebitati in base ai prezzi corrispondenti.

[Altre informazioni su Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Avvisi di Azure Defender 

Quando Azure Defender rileva una minaccia in qualsiasi area dell'ambiente, genera un avviso. Questi avvisi descrivono i dettagli sulle risorse interessate, le procedure di correzione consigliate e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta.

È possibile esportare tutti gli avvisi, sia quelli generati dal Centro sicurezza che quelli inviati al Centro sicurezza da un prodotto di sicurezza integrato. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).

> [!NOTE]
> Gli avvisi provenienti da origini diverse potrebbero comparire in tempi diversi. Ad esempio, la visualizzazione degli avvisi che richiedono l'analisi del traffico di rete potrebbe richiedere più tempo rispetto agli avvisi correlati a processi sospetti in esecuzione sulle macchine virtuali.


## <a name="azure-defender-advanced-protection-capabilities"></a>Funzionalità avanzate di protezione di Azure Defender

Azure Defender usa l'analisi avanzata per offrire raccomandazioni personalizzate correlate alle risorse. 

Le funzionalità includono la protezione delle porte di gestione delle VM con accesso JIT e controlli applicazioni adattivi per creare elenchi di elementi consentiti per le app che possono e non possono essere eseguite nei computer. 

Usare i riquadri di protezione avanzata nel dashboard di Azure Defender per monitorare e configurare ognuna di queste protezioni. 

## <a name="vulnerability-assessment-and-management"></a>Valutazione e gestione delle vulnerabilità

Azure Defender include l'analisi delle vulnerabilità per le macchine virtuali e i registri contenitori senza costi aggiuntivi. Gli strumenti di analisi sono basati su tecnologia Qualys, ma non è necessario avere una licenza o un account Qualys: tutto viene gestito senza problemi all'interno del Centro sicurezza. 

Esaminare i risultati di queste analisi delle vulnerabilità e rispondere dal Centro sicurezza. Il Centro sicurezza diventa quindi una soluzione centralizzata per la gestione di tutte le attività di sicurezza del cloud.

Per altre informazioni, vedere le pagine seguenti:

- [Soluzione integrata di valutazione delle vulnerabilità del Centro sicurezza per macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md)
- [Identificare le vulnerabilità nelle immagini dei registri contenitori di Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su i vantaggi di Azure Defender. 

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](security-center-pricing.md)