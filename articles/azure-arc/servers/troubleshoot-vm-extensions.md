---
title: Risolvere i problemi relativi all'estensione VM dei server abilitati per Azure Arc
description: Questo articolo illustra come risolvere i problemi relativi alle estensioni di VM di Azure che si verificano con i server abilitati per Azure Arc.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: ffd7db5ff7da3d7f60762117f80d7b9b5af6f646
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344612"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Risolvere i problemi relativi all'estensione della macchina virtuale per i server

Questo articolo fornisce informazioni sulla risoluzione dei problemi e sulla risoluzione dei problemi che possono verificarsi durante il tentativo di distribuire o rimuovere estensioni di VM di Azure nei server abilitati per Arc. Per informazioni generali, vedere [gestire e usare le estensioni di VM di Azure](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure.

I passaggi seguenti per la risoluzione dei problemi sono validi per tutte le estensioni macchina virtuale.

1. Per controllare il log dell'agente guest, esaminare l'attività quando è stato eseguito il provisioning dell'estensione in `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` per Windows e per Linux in `/var/lib/GuestConfig/ext_mgr_logs` .

2. Per altri dettagli in per Windows, controllare i log di estensione per l'estensione specifica `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . L'output dell'estensione viene registrato in un file per ogni estensione installata in Linux `/var/lib/GuestConfig/extension_logs` .

3. Vedere le sezioni relative alla risoluzione dei problemi della documentazione specifica dell'estensione per i codici di errore, problemi noti e così via Per ulteriori informazioni sulla risoluzione dei problemi per ogni estensione, vedere la sezione **risoluzione dei problemi e supporto** nella panoramica per l'estensione. Include la descrizione dei codici di errore scritti nel log. Gli articoli di estensione sono collegati nella [tabella Extensions](manage-vm-extensions.md#extensions).

4. Esaminare i log di sistema. Controllare la presenza di altre operazioni che potrebbero aver interferito con l'estensione, ad esempio un'installazione a esecuzione prolungata di un'altra applicazione che ha richiesto l'accesso di gestione pacchetti esclusiva.

## <a name="troubleshooting-specific-extension-scenarios"></a>Risoluzione dei problemi relativi agli scenari di estensione specifici

### <a name="vm-insights"></a>Informazioni dettagliate macchina virtuale

- Quando si Abilita VM Insights per un server abilitato per Azure Arc, viene installata la dipendenza e l'agente di Log Analytics. In un computer lento o in uno con una connessione di rete lenta, è possibile visualizzare i timeout durante il processo di installazione. Microsoft sta eseguendo alcune operazioni per risolvere questo problema nell'agente del computer connesso per contribuire a migliorare questa condizione. Nel frattempo, un nuovo tentativo di installazione potrebbe avere esito positivo.

### <a name="log-analytics-agent-for-linux"></a>Agente di Log Analytics per Linux

- La versione dell'agente Log Analytics 1.13.9 (la versione di estensione corrispondente è 1.13.15) non contrassegna correttamente i dati caricati con l'ID di risorsa del server abilitato per Azure Arc. Sebbene i log vengano inviati al servizio, quando si tenta di visualizzare i dati dal server attivato selezionato dopo aver selezionato i **log** o le informazioni **dettagliate**, non viene restituito alcun dato. È possibile visualizzare i dati eseguendo query dai log di monitoraggio di Azure o da Monitoraggio di Azure per le macchine virtuali, che hanno come ambito l'area di lavoro.

- Alcune distribuzioni non sono attualmente supportate dall'agente di Log Analytics per Linux. L'agente richiede l'installazione di dipendenze aggiuntive, incluso Python 2. Esaminare la matrice di supporto e i prerequisiti [qui](../../azure-monitor/platform/agents-overview.md#supported-operating-systems).

- Il codice di errore 52 nel messaggio di stato indica una dipendenza mancante. Per ulteriori informazioni sulla dipendenza mancante, controllare l'output e i log.

- Se un'installazione non riesce, vedere la sezione **risoluzione dei problemi e supporto** nella panoramica per l'estensione. Nella maggior parte dei casi è presente un codice di errore incluso nel messaggio di stato. Per l'agente di Log Analytics per Linux, i messaggi di stato vengono descritti [qui](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support), insieme alle informazioni generali sulla risoluzione dei problemi per questa estensione della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite [Microsoft Q&A](/answers/topics/azure-arc.html).

- Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure consente di entrare in contatto con la community di Azure e quindi di ottenere risposte, assistenza e consulenza.

- Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
