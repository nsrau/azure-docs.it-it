---
title: Domande frequenti su Defender
description: Trovare le risposte alle domande più frequenti su Azure Defender per le funzionalità e il servizio Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939759"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Domande frequenti su Azure Defender

Questo articolo fornisce un elenco di domande frequenti e risposte su Defender for Internet.

## <a name="does-azure-provide-support-for-iot-security"></a>Azure fornisce supporto per la sicurezza per IoT?

Come parte della soluzione di sicurezza complessiva, Azure offre una visualizzazione integrata per il monitoraggio e la gestione della sicurezza per IoT tramite il Centro sicurezza di Azure. Gli sviluppatori di applicazioni possono usare la vista hub IoT per gestire la sicurezza delle applicazioni IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual è la proposta di valore unica di Azure per la sicurezza per IoT?

Defender for Internet consente alle aziende di estendere la propria vista di sicurezza informatica esistente alla soluzione intera. Azure offre una visione end-to-end della soluzione aziendale che consente di intraprendere azioni e decisioni di business in base all’approccio alla sicurezza scelto e ai dati raccolti. La combinazione delle funzionalità per la sicurezza di Azure IoT, Azure IoT Edge e del Centro sicurezza di Azure consente di creare le soluzioni di scelta con le caratteristiche di sicurezza necessarie.

## <a name="who-is-defender-for-iot-made-for"></a>Chi è Defender per le cose?

Defender for Internet è integrato nell'ambito della sicurezza dell'hub Azure e fornisce la gestione per le operazioni di sicurezza quotidiane della soluzione aziendale. Defender for Internet è integrato anche nelle funzionalità del Centro sicurezza di Azure e offre una visualizzazione integrata per il monitoraggio e la gestione della sicurezza degli stessi come parte della soluzione di sicurezza complessiva.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Quali sono le differenze tra Defender e il concorso?

Sebbene altre soluzioni forniscano una serie di funzionalità che consentono ai clienti di creare soluzioni personalizzate, Defender for Internet offre una soluzione di sicurezza per gli elementi End-to-end univoca che offre una panoramica completa della sicurezza di tutte le risorse di Azure correlate. Azure assicura una distribuzione rapida e la piena integrazione con gli elementi gemelli del modulo hub IoT per una facile integrazione con gli strumenti di gestione dei dispositivi esistenti.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>È necessario essere un cliente del Centro sicurezza di Azure per usare il servizio?

No, ma è consigliabile. Senza il Centro sicurezza di Azure, Defender per l'it riceve dati di risorse connesse limitati e fornisce un'analisi limitata della superficie di attacco potenziale, delle minacce e dei potenziali attacchi.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>È necessario essere clienti di Azure?

Sì. Defender for Internet è basato sulla connettività e sull'infrastruttura di Azure.

## <a name="do-i-have-to-install-an-agent"></a>È necessario installare un agente?

L'installazione dell'agente nei dispositivi Internet non è obbligatoria per consentire l'uso di Defender. È possibile scegliere tra le tre opzioni indicate di seguito, che offrono diversi livelli di funzionalità per il monitoraggio e la gestione della sicurezza in base alla selezione:

1. Installare Defender for Internet Security Agent con o senza modifiche. Questa opzione offre il massimo livello di informazioni dettagliate sulla sicurezza rispetto al comportamento e all'accesso del dispositivo.

1. Creare un agente personalizzato e implementare il Defender per lo schema del messaggio di sicurezza. Questa opzione consente l'uso di Defender per gli strumenti di analisi delle cose all'inizio dell'agente di sicurezza del dispositivo.

1. Nessuna installazione dell'agente di sicurezza sui dispositivi IoT. Questa opzione consente il monitoraggio delle comunicazioni dell'hub IoT, con funzionalità di gestione e monitoraggio della sicurezza ridotte.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Che cosa fa il Defender per l'agente Internet?

Defender per l'agente di gestione dei dispositivi it fornisce copertura delle minacce a livello di dispositivo per la configurazione del dispositivo, il comportamento e l'accesso (tramite l'analisi della configurazione), elaborazione & connettività. Il Defender per l'agente sicurezza non esegue l'analisi dell'attività o dei dati correlati all'azienda.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Dove posso ottenere il Defender per l'agente sicurezza?

Defender for Internet Security Agent è open source ed è disponibile su GitHub nelle versioni di Windows e Linux a 32 bit e a 64 bit: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Dove viene installato il Defender per l'agente Internet?

Informazioni dettagliate sull'installazione e sulla distribuzione degli agente sono disponibili su GitHub: https://github.com/Azure/Azure-IoT-Security.

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quali sono le dipendenze e i prerequisiti dell'agente?

Defender per Internet delle cose supporta un'ampia gamma di piattaforme. Vedere [Piattaforme per dispositivi supportate](how-to-deploy-agent.md) per verificare il supporto per i dispositivi specifici.

## <a name="which-data-is-collected-by-the-agent"></a>Quali dati vengono raccolti dall'agente?

I dati raccolti dall’agente riguardano: connettività, accesso, configurazione del firewall, elenco dei processi e baseline del sistema operativo.

## <a name="how-much-data-will-the-agent-generate"></a>Quanti dati vengono generati dall'agente?

La generazione dei dati dell'agente è determinata dal dispositivo, dall'applicazione, dal tipo di connettività e dalla configurazione dell'agente del cliente. A causa dell’elevata variabilità tra dispositivi e soluzioni IoT, è consigliabile innanzitutto distribuire l'agente in un lab o in un'impostazione di test per osservare, apprendere e impostare la configurazione specifica adatta alle proprie esigenze, misurando al contempo la quantità di dati generati. Dopo l'avvio del servizio, il Defender per l'agente di gestione delle cose fornisce consigli operativi per ottimizzare la velocità effettiva dell'agente per semplificare il processo di configurazione e personalizzazione.

## <a name="how-can-i-control-my-billing"></a>Come è possibile controllare la fatturazione?

Defender for Internet offre analisi degli agenti configurabili, buffer di dati e la possibilità di creare avvisi personalizzati che aumentano o riducono la quantità di dati generati dall'agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>I messaggi dell’agente consumano quote dall'hub IoT?

Sì. I dati trasmessi dall'agente vengono conteggiati nella quota dell'hub IoT.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Come procedere? È stato installato un agente ma non vengono visualizzate attività o log...

1. Verificare che il [tipo di agente corrisponda alla piattaforma del sistema operativo designata del dispositivo](how-to-deploy-agent.md).

1. Verificare che l'[agente sia in esecuzione nel dispositivo](how-to-agent-configuration.md).

1. Verificare che il [servizio sia stato abilitato correttamente](quickstart-onboard-iot-hub.md) per la **sicurezza** nell'hub IoT.

1. Verificare che il dispositivo sia [configurato nell'hub Internet con il modulo Defender for](quickstart-create-security-twin.md)Internet.

Se le attività o i log non sono ancora disponibili, contattare il difensore del partner Internet per ottenere assistenza aggiuntiva.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Cosa accade quando la connessione Internet si interrompe?

L'agente continua a eseguire e memorizzare i dati fino a quando il dispositivo è in esecuzione. I dati vengono memorizzati nella cache dei messaggi di sicurezza in base alla configurazione delle dimensioni. Quando il dispositivo recupera la connettività, viene ripreso anche l’invio dei messaggi sulla sicurezza.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se il dispositivo viene riavviato, viene effettuato il ripristino automatico dell'agente di sicurezza?

L'agente di sicurezza è progettato per essere eseguito di nuovo automaticamente a ogni riavvio del dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>L'agente può influire sulle prestazioni del dispositivo o di altro software installato?

L'agente utilizza le risorse del computer come qualsiasi altra applicazione o processo e non deve compromettere le normali attività del dispositivo. L'utilizzo delle risorse nel dispositivo in cui viene eseguito l'agente viene abbinato all’impostazione e alla configurazione. Prima di tentare la distribuzione in un ambiente di produzione, è consigliabile testare la configurazione dell'agente in un ambiente indipendente, insieme all'interoperabilità con altre applicazioni e funzionalità.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Nel caso di operazioni di manutenzione sul dispositivo, è possibile disattivare l'agente?

L'agente non può essere disattivato.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Esiste un modo per verificare se l'agente funziona correttamente?

Se l'agente interrompe la comunicazione o non riesce a inviare messaggi sulla sicurezza, viene generato un avviso che informa che il **dispositivo è invisibile all'utente**.

## <a name="can-i-create-my-own-alerts"></a>È possibile creare avvisi personalizzati?

Sì. È possibile impostare un avviso personalizzato per un set di comportamenti predefinito, ad esempio l'indirizzo IP e le porte aperte. Vedere la pagina sulla [creazione di avvisi personalizzati](quickstart-create-custom-alerts.md) per ulteriori informazioni sugli avvisi personalizzati e su come crearli.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Dove è possibile visualizzare I log? È possibile personalizzare I log?

- Visualizzare i log e le raccomandazioni tramite l'area di lavoro Log Analytics connessa. Configurare la durata e le dimensioni per l’archiviazione nell'area di lavoro.

- I dati non elaborati dall'agente di sicurezza possono anche essere memorizzati nell'account Log Analytics. Prima di modificare la configurazione di questa opzione, prendere in considerazione le dimensioni, la durata, i requisiti di archiviazione e i costi associati.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Perché è necessario aggiungere Defender per l'identità del modulo? Scopo

Il modulo Defender for Internet è usato per la configurazione e la gestione degli agenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare a usare Defender per l'it, vedere gli articoli seguenti:

- Leggi la [Panoramica](overview.md) di Defender for Internet
- Verificare i [prerequisiti del servizio](service-prerequisites.md)
- Vedere ulteriori informazioni su come [iniziare](getting-started.md)
- Informazioni sugli [avvisi di sicurezza di Defender](concept-security-alerts.md)
