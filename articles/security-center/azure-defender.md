---
title: Panoramica di Azure Defender e dei piani disponibili
description: Informazioni sui piani, sulle protezioni e sugli avvisi di Azure Defender. Quindi, procedere con l'abilitazione di Azure Defender nelle sottoscrizioni.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977301"
---
# <a name="introduction-to-azure-defender"></a>Introduzione ad Azure Defender

Le funzionalità del Centro sicurezza di Azure coprono i due ampi pilastri della sicurezza del cloud:

- **Gestione del comportamento di sicurezza del cloud (CSPM)**
- **Protezione del carico di lavoro cloud (CWP)**

Le funzionalità di CSPM del Centro sicurezza, ad esempio il Punteggio sicuro, il rilevamento di problemi di configurazione della sicurezza nei computer Windows e Linux di Azure, fanno parte dell'esperienza del Centro sicurezza gratuita disponibile per tutti gli utenti di Azure. Usare queste funzionalità CSPM per rafforzare la postura e garantire la conformità alle normative.

**Azure Defender** è la piattaforma di protezione del carico di lavoro cloud (CWPP) integrata nel centro sicurezza per la protezione avanzata e intelligente dei carichi di lavoro ibridi e Azure.

Questo è il dashboard di Azure Defender nel centro sicurezza di Azure:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Esempio di dashboard di Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Quali tipi di risorse possono essere protetti da Azure Defender?

Azure Defender fornisce avvisi di sicurezza e protezione avanzata dalle minacce per macchine virtuali, database SQL, contenitori, applicazioni Web, rete e altro ancora.

Quando si Abilita Azure Defender dall'area **prezzi e impostazioni** del Centro sicurezza di Azure, i piani Defender seguenti sono tutti abilitati simultaneamente e forniscono difese complete per i livelli di calcolo, dati e servizio dell'ambiente:

- [Azure Defender per server](defender-for-servers-introduction.md)
- [Azure Defender per il servizio app](defender-for-app-service-introduction.md)
- [Azure Defender per Archiviazione](defender-for-storage-introduction.md)
- [Azure Defender per SQL](defender-for-sql-introduction.md)
- [Azure Defender per IoT](defender-for-iot-introduction.md)
- [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)
- [Azure Defender per Key Vault](defender-for-key-vault-introduction.md)

Ognuno di questi piani viene illustrato separatamente nella documentazione del Centro sicurezza.


## <a name="hybrid-cloud-protection"></a>Protezione cloud ibrida

Oltre a difendere l'ambiente Azure, è possibile aggiungere funzionalità di Azure Defender all'ambiente cloud ibrido:

- Proteggi i tuoi server non Azure
- Proteggi le tue macchine virtuali in altri cloud, ad esempio AWS e GCP
- Proteggi i tuoi dispositivi

Si otterrà l'Intelligence per le minacce personalizzata e gli avvisi in ordine di priorità in base all'ambiente specifico, in modo da potersi concentrare sul più importante

Distribuisci [Azure Arc](https://azure.microsoft.com/services/azure-arc/) e Abilita Azure Defender per estendere la protezione alle macchine virtuali locali e a più cloud e ai database SQL. Azure Arc per i server è un servizio gratuito, ma i servizi usati nei server abilitati per Arc, ad esempio Azure Defender, verranno addebitati in base ai prezzi del servizio.

[Scopri di più su Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Avvisi di Azure Defender 

Quando Azure Defender rileva una minaccia in qualsiasi area dell'ambiente, viene generato un avviso. Questi avvisi descrivono i dettagli sulle risorse interessate, le procedure di correzione consigliate e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta.

Se un avviso viene generato dal centro sicurezza o ricevuto dal centro sicurezza da un prodotto di sicurezza integrato, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).

> [!NOTE]
> Gli avvisi provenienti da origini diverse potrebbero comparire in tempi diversi. Ad esempio, la visualizzazione degli avvisi che richiedono l'analisi del traffico di rete potrebbe richiedere più tempo rispetto agli avvisi correlati a processi sospetti in esecuzione sulle macchine virtuali.


## <a name="azure-defender-advanced-protection-capabilities"></a>Funzionalità di protezione avanzata di Azure Defender

Azure Defender usa l'analisi avanzata per le raccomandazioni personalizzate correlate alle risorse. 

Le protezioni includono la protezione delle porte di gestione delle macchine virtuali con l'accesso JIT e i controlli delle applicazioni adattivi per creare elenchi consentiti per le app che dovrebbero e non devono essere eseguite nei computer. 

Usare i riquadri di protezione avanzata nel dashboard di Azure Defender per monitorare e configurare ognuna di queste protezioni. 

## <a name="vulnerability-assessment-and-management"></a>Valutazione della vulnerabilità e gestione

Azure Defender include l'analisi delle vulnerabilità per le macchine virtuali e i registri contenitori senza costi aggiuntivi. Gli scanner sono basati su Qualys, ma non è necessaria una licenza Qualys o anche un account Qualys. tutto viene gestito senza interruzioni all'interno del Centro sicurezza. 

Esaminare i risultati di questi scanner di vulnerabilità e rispondere a tutti dal centro sicurezza. In questo modo il Centro sicurezza è più vicino a essere il singolo riquadro di vetro per tutte le attività di sicurezza del cloud.

Per ulteriori informazioni, vedere le pagine seguenti:

- [Soluzione di valutazione della vulnerabilità integrata del Centro sicurezza per le macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md)
- [Identificare le vulnerabilità nelle immagini nei registri contenitori di Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato i vantaggi di Azure Defender. 

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](security-center-pricing.md)