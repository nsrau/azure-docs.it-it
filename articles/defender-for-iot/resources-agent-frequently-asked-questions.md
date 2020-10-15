---
title: Domande frequenti su Azure Defender per l'agente
description: Trovare le risposte alle domande più frequenti su Azure Defender per l'agente.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 9862519a2003eb373c43fef1b660986a8d830327
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094456"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Domande frequenti su Azure Defender per l'agente

Questo articolo fornisce un elenco di domande frequenti e risposte relative al Defender per l'agente.


## <a name="do-i-have-to-install-an-embedded-security-agent"></a>È necessario installare un agente protezione incorporato?

L'installazione dell'agente nei dispositivi Internet non è obbligatoria per consentire l'uso di Defender. È possibile scegliere tra le tre opzioni indicate di seguito, che offrono diversi livelli di funzionalità per il monitoraggio e la gestione della sicurezza in base alla selezione:

- Distribuzione passiva, non invasiva (senza agenti) che usa i sensori NTA (analisi del traffico di rete) per monitorare e fornire visibilità approfondita sul rischio per gli Internet e i rischi senza alcun effetto sulle prestazioni della rete e dei dispositivi
- Installare il Defender per l'agente di sicurezza embedded con o senza modifiche. Questa opzione offre il massimo livello di informazioni dettagliate sulla sicurezza rispetto al comportamento e all'accesso del dispositivo.

- Creare un agente personalizzato e implementare il Defender per lo schema del messaggio di sicurezza. Questa opzione consente l'uso di Defender per gli strumenti di analisi delle cose all'inizio dell'agente di sicurezza del dispositivo.

- Nessuna installazione dell'agente di sicurezza sui dispositivi IoT. Questa opzione consente il monitoraggio delle comunicazioni dell'hub IoT, con funzionalità di gestione e monitoraggio della sicurezza ridotte.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Che cosa fa il Defender per l'agente Internet?

Defender per l'agente di gestione dei dispositivi it fornisce copertura delle minacce a livello di dispositivo per la configurazione del dispositivo, il comportamento e l'accesso (tramite l'analisi della configurazione), elaborazione & connettività. Il Defender per l'agente sicurezza non esegue l'analisi dell'attività o dei dati correlati all'azienda.

Defender for Internet Security Agent è open source ed è disponibile su GitHub nelle versioni di Windows e Linux a 32 bit e a 64 bit: https://github.com/Azure/Azure-IoT-Security .


## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quali sono le dipendenze e i prerequisiti dell'agente?

Defender per Internet delle cose supporta un'ampia gamma di piattaforme. Vedere [Piattaforme per dispositivi supportate](how-to-deploy-agent.md) per verificare il supporto per i dispositivi specifici.

## <a name="which-data-is-collected-by-the-agent"></a>Quali dati vengono raccolti dall'agente?

I dati raccolti dall’agente riguardano: connettività, accesso, configurazione del firewall, elenco dei processi e baseline del sistema operativo.

## <a name="how-much-data-will-the-agent-generate"></a>Quanti dati vengono generati dall'agente?

La generazione dei dati dell'agente è determinata dal dispositivo, dall'applicazione, dal tipo di connettività e dalla configurazione dell'agente del cliente. A causa dell’elevata variabilità tra dispositivi e soluzioni IoT, è consigliabile innanzitutto distribuire l'agente in un lab o in un'impostazione di test per osservare, apprendere e impostare la configurazione specifica adatta alle proprie esigenze, misurando al contempo la quantità di dati generati. Dopo l'avvio del servizio, il Defender per l'agente di gestione delle cose fornisce consigli operativi per ottimizzare la velocità effettiva dell'agente per semplificare il processo di configurazione e personalizzazione.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>I messaggi dell’agente consumano quote dall'hub IoT?

Sì. I dati trasmessi dall'agente vengono conteggiati nella quota dell'hub IoT.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Come procedere? È stato installato un agente ma non vengono visualizzate attività o log...

1. Verificare che il [tipo di agente corrisponda alla piattaforma del sistema operativo designata del dispositivo](how-to-deploy-agent.md).

1. Verificare che l'[agente sia in esecuzione nel dispositivo](how-to-agent-configuration.md).

1. Verificare che il [servizio sia stato abilitato correttamente](quickstart-onboard-iot-hub.md) per la **sicurezza** nell'hub IoT.

1. Verificare che il dispositivo sia [configurato nell'hub Internet con il modulo Defender for](quickstart-create-security-twin.md)Internet.

Se le attività o i log non sono ancora disponibili, contattare il difensore del partner Internet per ottenere assistenza aggiuntiva.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Cosa accade quando la connessione Internet si interrompe?

I sensori e gli agenti continuano a funzionare e a archiviare i dati finché il dispositivo è in esecuzione. I dati vengono memorizzati nella cache dei messaggi di sicurezza in base alla configurazione delle dimensioni. Quando il dispositivo recupera la connettività, viene ripreso anche l’invio dei messaggi sulla sicurezza.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>L'agente può influire sulle prestazioni del dispositivo o di altro software installato?

L'agente utilizza le risorse del computer come qualsiasi altra applicazione o processo e non deve compromettere le normali attività del dispositivo. L'utilizzo delle risorse nel dispositivo in cui viene eseguito l'agente viene abbinato all’impostazione e alla configurazione. Prima di tentare la distribuzione in un ambiente di produzione, è consigliabile testare la configurazione dell'agente in un ambiente indipendente, insieme all'interoperabilità con altre applicazioni e funzionalità.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Nel caso di operazioni di manutenzione sul dispositivo, è possibile disattivare l'agente?

L'agente non può essere disattivato.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Esiste un modo per verificare se l'agente funziona correttamente?

Se l'agente interrompe la comunicazione o non riesce a inviare messaggi sulla sicurezza, viene generato un avviso che informa che il **dispositivo è invisibile all'utente**.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare a usare Defender per l'it, vedere gli articoli seguenti:

- Leggi la [Panoramica](overview.md) di Defender for Internet
- Verificare i [prerequisiti del servizio](service-prerequisites.md)
- Vedere ulteriori informazioni su come [iniziare](getting-started.md)
- Informazioni sugli [avvisi di sicurezza di Defender](concept-security-alerts.md)
