---
title: Panoramica dei server con abilitazione di Azure Arc
description: Informazioni su come usare i server con abilitazione di Azure Arc per gestire server ospitati all'esterno di Azure come una risorsa di Azure.
keywords: automazione di azure, DSC, powershell, configurazione dello stato desiderato, gestione aggiornamenti, rilevamento modifiche, inventario, runbook, python, grafico, ibrido
ms.date: 09/16/2020
ms.topic: overview
ms.openlocfilehash: 9fcac35f943eefb15a200e9b148b22ce67cc3941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90887531"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Che cosa sono i server con abilitazione di Azure Arc?

I server con abilitazione di Azure Arc consentono di gestire le macchine virtuali Windows e Linux ospitate all'esterno di Azure, nella rete aziendale o in un altro provider di servizi cloud, in modo analogo a come si gestiscono le macchine virtuali native di Azure. Quando una macchina virtuale ibrida viene connessa ad Azure, diventa una macchina virtuale connessa e viene considerata come una risorsa in Azure. Ogni macchina virtuale connessa dispone di un ID risorsa, viene gestita come parte di un gruppo di risorse all'interno di una sottoscrizione e usufruisce dei vantaggi derivanti dai costrutti di Azure standard, ad esempio Criteri di Azure e assegnazione di tag. I provider di servizi che gestiscono l'infrastruttura locale di un cliente possono gestire le proprie macchine virtuali ibride in più ambienti di clienti, proprio come avviene oggi con le risorse native di Azure, usando [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) con Azure Arc.

Per offrire questa esperienza con le macchine virtuali ibride ospitate all'esterno di Azure, è necessario installare l'agente Azure Connected Machine in ogni macchina virtuale che si prevede di connettere ad Azure. Questo agente non fornisce altre funzionalità e non sostituisce l'[agente di Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).

## <a name="supported-scenarios"></a>Scenari supportati

Quando si connette la macchina virtuale a server con abilitazione di Azure Arc, è possibile eseguire le attività di gestione della configurazione e di monitoraggio seguenti:

- Assegnare [configurazioni guest di Criteri di Azure](../../governance/policy/concepts/guest-configuration.md) usando la stessa esperienza dell'assegnazione dei criteri per le macchine virtuali di Azure.

- Creare report sulle modifiche di configurazione relative al software installato, ai servizi Microsoft, al Registro di sistema e ai file di Windows, nonché ai daemon Linux nei server monitorati usando [Rilevamento modifiche e inventario](../../automation/change-tracking.md) di Automazione di Azure.

- Monitorare le prestazioni del sistema operativo guest della macchina virtuale connessa e individuare i componenti dell'applicazione per monitorarne i processi e le dipendenze con altre risorse che l'applicazione comunica tramite [Monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md).

- Semplificare la distribuzione con altri servizi di Azure, ad esempio [State Configuration](../../automation/automation-dsc-overview.md) di Automazione di Azure e l'area di lavoro Log Analytics di Monitoraggio di Azure, usando le [estensioni macchina virtuale di Azure](manage-vm-extensions.md) supportate per le macchine virtuali Windows o Linux non di Azure. È inclusa l'esecuzione della configurazione post-distribuzione o l'installazione del software tramite l'estensione Script personalizzato.

- È possibile usare [Gestione aggiornamenti](../../automation/update-management/update-mgmt-overview.md) di Automazione di Azure per gestire gli aggiornamenti del sistema operativo per i server Windows e Linux. Distribuire innanzitutto il [Ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) e quindi seguire la procedura per [abilitare Gestione aggiornamenti](../../automation/update-management/update-mgmt-enable-portal.md) nella macchina virtuale Windows o Linux non di Azure.

- Includere i server non di Azure per il rilevamento delle minacce e monitorare in modo proattivo le potenziali minacce per la sicurezza usando [Centro sicurezza di Azure](../../security-center/security-center-intro.md).

I dati di log raccolti e archiviati in un'area di lavoro Log Analytics dalla macchina virtuale ibrida includono ora proprietà specifiche della macchina virtuale, ad esempio un ID risorsa, che possono essere usate per supportare l'accesso ai log di tipo [risorsa-contesto](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Aree supportate

Per un elenco definitivo delle aree supportate con i server con abilitazione di Azure Arc, vedere la pagina [Prodotti Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

Nella maggior parte dei casi, la posizione selezionata durante la creazione dello script di installazione deve essere l'area di Azure geograficamente più vicina alla posizione del computer. I dati inattivi verranno archiviati all'interno dell'area geografica di Azure contenente l'area specificata, il che potrebbe influire anche sulla scelta dell'area in caso di requisiti di residenza dei dati. Se l'area di Azure a cui è connesso il computer è interessata da un'interruzione del servizio, il computer connesso non sarà interessato, ma le operazioni di gestione che usano Azure potrebbero non venire completate. In caso di interruzione di servizio a livello di area, se sono presenti più località che supportano un servizio con ridondanza geografica, è consigliabile connettere le macchine virtuali in ogni località a un'area di Azure diversa.

### <a name="agent-status"></a>Stato dell'agente

L'agente Connected Machine invia un messaggio regolare di tipo heartbeat al servizio ogni 5 minuti. Se il servizio smette di ricevere questi messaggi di tipo heartbeat da un computer, quest'ultimo viene considerato offline e lo stato verrà automaticamente modificato in **Disconnesso** nel portale in un arco di tempo compreso tra 15 e 30 minuti. Alla ricezione di un messaggio successivo di tipo heartbeat dall'agente Connected Machine, il relativo stato verrà automaticamente modificato in **Connesso**.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare i server con abilitazione di Arc tra più macchine virtuali ibride, vedere [Panoramica dell'agente Connected Machine](agent-overview.md) per informazioni sui requisiti, i dettagli tecnici sull'agente e i metodi di distribuzione.
