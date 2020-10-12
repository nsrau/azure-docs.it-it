---
title: Architettura della soluzione
description: Informazioni sul flusso di informazioni in Azure Defender per il servizio Internet delle cose.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843414"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender per l'architettura dell'it

Questo articolo illustra l'architettura del sistema funzionale della soluzione Defender for Internet.

## <a name="defender-for-iot-components"></a>Defender per i componenti dell'it

Defender for è costituito dai componenti seguenti:

- Integrazione dell'hub Internet
- Agenti dispositivo (facoltativo)
- Inviare il messaggio di sicurezza SDK
- Pipeline di analisi

### <a name="defender-for-iot-workflows"></a>Defender per flussi di lavoro Internet

Defender per l'Internet delle cose in uno dei due flussi di lavoro di funzionalità: incorporata e migliorata

### <a name="built-in"></a>Predefinito

In modalità **predefinita** , Defender for Internet è abilitato quando si sceglie di attivare l'opzione di **sicurezza** nell'hub Internet delle cose. Il monitoraggio, le raccomandazioni e gli avvisi in tempo reale, la modalità predefinita offre visibilità dei dispositivi in un singolo passaggio e sicurezza senza corrispondenza. La modalità di compilazione non richiede l'installazione dell'agente su tutti i dispositivi e usa l'analisi avanzata sulle attività registrate per analizzare e proteggere il dispositivo Field.

### <a name="enhanced"></a>Avanzato

In modalità **avanzata** , dopo l'attivazione dell'opzione di **sicurezza** nell'hub Internet e l'installazione di Defender per gli agenti di dispositivi di tutto il dispositivo, gli agenti raccolgono, aggregano e analizzano gli eventi di sicurezza non elaborati dai dispositivi. Gli eventi di sicurezza non elaborati possono includere connessioni IP, creazione di processi, account di accesso utente e altre informazioni rilevanti per la sicurezza. I difensori per gli agenti del dispositivo Internet gestisce anche l'aggregazione di eventi per evitare una elevata velocità effettiva della rete. Gli agenti sono altamente personalizzabili e possono essere usati per attività specifiche, ad esempio per l'invio di informazioni importanti al contratto di servizio più veloce o per l'aggregazione di informazioni e contesto di sicurezza estese in segmenti più grandi, evitando i costi di servizio più elevati.

![Defender per l'architettura dell'it](./media/architecture/azure-iot-security-architecture.png)

Gli agenti dispositivo e altre applicazioni usano **Azure Send Security Message SDK** per inviare informazioni di sicurezza nell'hub Azure. L'hub tutto ottiene queste informazioni e le invia al Defender per il servizio Internet delle cose.

Una volta abilitato il servizio Defender for Internet, oltre ai dati inoltrati, l'hub Internet invia anche tutti i dati interni per l'analisi da parte di Defender. Questi dati includono i log delle operazioni del cloud, le identità del dispositivo e la configurazione dell'hub. Tutte queste informazioni consentono di creare il Defender per la pipeline di analisi Internet delle cose.

Il Defender per la pipeline di analisi del servizio Internet riceve anche flussi di intelligence per le minacce aggiuntivi da diverse origini all'interno di partner Microsoft e Microsoft. Il Defender per l'intera pipeline di analisi funziona con tutte le configurazioni dei clienti effettuate sul servizio, ad esempio gli avvisi personalizzati e l'uso dell'SDK del messaggio di sicurezza di invio.

Con la pipeline di analisi, Defender for Internet usa tutti i flussi di informazioni per generare raccomandazioni e avvisi di utilità pratica. La pipeline contiene sia le regole personalizzate create dai ricercatori e gli esperti della sicurezza, sia i modelli di apprendimento automatico che cercano la deviazione dal comportamento del dispositivo standard e dall'analisi dei rischi.

Il servizio Defender per gli avvisi e le raccomandazioni per l'it (output della pipeline di analisi) viene scritto nell'area di lavoro Log Analytics di ogni cliente. L'inclusione degli eventi non elaborati nell'area di lavoro e degli avvisi e delle raccomandazioni consente l'analisi approfondita e le query usando i dettagli esatti delle attività sospette rilevate.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come l'architettura di base e il flusso di lavoro di Defender per la soluzione Internet delle cose. Per altre informazioni sui prerequisiti, su come iniziare e abilitare la soluzione di sicurezza nell'hub Internet, vedere gli articoli seguenti:

- [Prerequisiti del servizio](service-prerequisites.md)
- [Introduzione](getting-started.md)
- [Configurare la soluzione](quickstart-configure-your-solution.md)
- [Abilitare la sicurezza nell'hub Internet](quickstart-onboard-iot-hub.md)
- [Domande frequenti su Defender](resources-frequently-asked-questions.md)
- [Avvisi di sicurezza di Defender for Internet](concept-security-alerts.md)
