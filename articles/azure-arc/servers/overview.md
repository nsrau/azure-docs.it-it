---
title: Panoramica di Azure Arc per server (anteprima)
description: Questo articolo illustra come usare Azure Arc per server per gestire macchine virtuali ospitate all'esterno di Azure come se fossero risorse di Azure.
keywords: automazione di azure, DSC, powershell, configurazione dello stato desiderato, gestione aggiornamenti, rilevamento modifiche, inventario, runbook, python, grafico, ibrido
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: f11eedaf5f70cb24fa6c1588b7f26b2eed4734ce
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121800"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Che cos'è Azure Arc per server (anteprima)?

Azure Arc per server (anteprima) consente di gestire le macchine virtuali Windows e Linux ospitate all'esterno di Azure nella rete aziendale o in un altro provider di servizi cloud, in modo analogo a come si gestiscono le macchine virtuali native di Azure. Quando una macchina virtuale ibrida viene connessa ad Azure, diventa una macchina virtuale connessa e viene considerata come una risorsa in Azure. Ogni macchina virtuale connessa dispone di un ID risorsa, viene gestita come parte di un gruppo di risorse all'interno di una sottoscrizione e usufruisce dei vantaggi derivanti dai costrutti di Azure standard, ad esempio Criteri di Azure e assegnazione di tag.

Per offrire questa esperienza con le macchine virtuali ibride ospitate all'esterno di Azure, è necessario installare l'agente Azure Connected Machine in ogni macchina virtuale che si prevede di connettere ad Azure. Questo agente non fornisce altre funzionalità e non sostituisce l'[agente di Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Questa versione di anteprima è destinata a scopi di valutazione e non è consigliabile usarla per gestire computer di produzione critici.
>

## <a name="supported-scenarios"></a>Scenari supportati

Quando si connette la macchina virtuale ad Azure Arc per server (anteprima), è possibile eseguire le attività di gestione della configurazione seguenti:

- Assegnare [configurazioni guest di Criteri di Azure](../../governance/policy/concepts/guest-configuration.md) usando la stessa esperienza dell'assegnazione dei criteri per le macchine virtuali di Azure.

- Monitorare le prestazioni del sistema operativo guest della macchina virtuale connessa e individuare i componenti dell'applicazione per monitorarne i processi e le dipendenze con altre risorse che l'applicazione comunica tramite [Monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md).

- Semplificare la distribuzione con altri servizi di Azure, ad esempio State Configuration di Automazione di Azure e l'area di lavoro Log Analytics di Monitoraggio di Azure usando le [estensioni macchina virtuale di Azure](manage-vm-extensions.md) supportate per le macchine virtuali Windows o Linux non di Azure. È inclusa l'esecuzione della configurazione post-distribuzione o l'installazione del software tramite l'estensione Script personalizzato.

I dati di log raccolti e archiviati in un'area di lavoro Log Analytics dalla macchina virtuale ibrida includono ora proprietà specifiche della macchina virtuale, ad esempio un ID risorsa, che possono essere usate per supportare l'accesso ai log di tipo [risorsa-contesto](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Aree supportate

Con Azure Arc per server (anteprima) sono supportate solo alcune aree:

- EastUS
- Stati Uniti occidentali 2
- Europa occidentale
- SoutheastAsia

Nella maggior parte dei casi, la posizione selezionata durante la creazione dello script di installazione deve essere l'area di Azure geograficamente più vicina alla posizione del computer. I dati inattivi verranno archiviati all'interno dell'area geografica di Azure contenente l'area specificata, il che potrebbe influire anche sulla scelta dell'area in caso di requisiti di residenza dei dati. Se l'area di Azure a cui è connesso il computer è interessata da un'interruzione del servizio, il computer connesso non sarà interessato, ma le operazioni di gestione che usano Azure potrebbero non venire completate. In caso di interruzione di servizio a livello di area, se sono presenti più località che forniscono un servizio con ridondanza geografica, è consigliabile connettere le macchine virtuali in ogni località a un'area di Azure diversa.

### <a name="agent-status"></a>Stato dell'agente

L'agente Connected Machine invia un messaggio regolare di tipo heartbeat al servizio ogni 5 minuti. Se il servizio smette di ricevere questi messaggi di tipo heartbeat da un computer, quest'ultimo viene considerato offline e lo stato verrà automaticamente modificato in **Disconnesso** nel portale in un arco di tempo compreso tra 15 e 30 minuti. Alla ricezione di un messaggio successivo di tipo heartbeat dall'agente Connected Machine, il relativo stato verrà automaticamente modificato in **Connesso**.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare Arc per server (anteprima) tra più computer ibridi, vedere l'articolo [Panoramica dell’agente Connected Machine](agent-overview.md) per comprendere cosa è obbligatorio, quali sono i dettagli tecnici sull'agente e i metodi di distribuzione.
