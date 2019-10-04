---
title: Informazioni sul centro sicurezza di Azure per l'architettura della soluzione Internet delle cose | Microsoft Docs
description: Informazioni sul flusso di informazioni nel centro sicurezza di Azure per il servizio Internet delle cose.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: a013d4cfcfddc709e60e91adf57bc27c98934a96
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596576"
---
# <a name="azure-security-center-for-iot-architecture"></a>Centro sicurezza di Azure per l'architettura dell'it

Questo articolo illustra l'architettura del sistema funzionale del Centro sicurezza di Azure per la soluzione Internet delle cose. 

## <a name="azure-security-center-for-iot-components"></a>Centro sicurezza di Azure per i componenti di Internet delle cose

Il Centro sicurezza di Azure per l'it è costituito dai componenti seguenti:
- Integrazione dell'hub Internet
- Agenti dispositivo (facoltativo)
- Inviare il messaggio di sicurezza SDK
- Pipeline di analisi
 
### <a name="azure-security-center-for-iot-workflows"></a>Centro sicurezza di Azure per flussi di lavoro Internet

Il Centro sicurezza di Azure per l'it funziona in uno dei due flussi di lavoro di funzionalità: Incorporata e migliorata  

### <a name="built-in"></a>Predefinito
In modalità **predefinita** , il Centro sicurezza di Azure per l'it è abilitato quando si sceglie di attivare l'opzione di **sicurezza** nell'hub Internet. Il monitoraggio, le raccomandazioni e gli avvisi in tempo reale, la modalità incorporata offre visibilità del dispositivo in fase di individuazione e sicurezza senza corrispondenza. La modalità di compilazione non richiede l'installazione dell'agente su tutti i dispositivi e usa l'analisi avanzata sulle attività registrate per analizzare e proteggere il dispositivo Field. 

### <a name="enhanced"></a>Avanzato 
In modalità **avanzata** , dopo l'attivazione dell'opzione di **sicurezza** nell'hub Internet e l'installazione del Centro sicurezza di Azure per gli agenti di dispositivi di tutto il dispositivo, gli agenti raccolgono, aggregano e analizzano gli eventi di sicurezza non elaborati dai dispositivi. Gli eventi di sicurezza non elaborati possono includere connessioni IP, creazione di processi, account di accesso utente e altre informazioni rilevanti per la sicurezza. Il Centro sicurezza di Azure per gli agenti del dispositivo Internet gestisce anche l'aggregazione di eventi per evitare una elevata velocità effettiva della rete. Gli agenti sono altamente personalizzabili e possono essere usati per attività specifiche, ad esempio per l'invio di informazioni importanti al contratto di servizio più veloce o per l'aggregazione di informazioni e contesto di sicurezza estese in segmenti più grandi, evitando i costi di servizio più elevati.

![Centro sicurezza di Azure per l'architettura dell'it](./media/architecture/azure-iot-security-architecture.png)
 
Gli agenti dispositivo e altre applicazioni usano **Azure Send Security Message SDK** per inviare informazioni di sicurezza nell'hub Azure. Hub cose preleva queste informazioni e le invia al centro sicurezza di Azure per il servizio Internet delle cose.

Una volta abilitato il Centro sicurezza di Azure per il servizio Internet, oltre ai dati inoltrati, l'hub Internet invia anche tutti i dati interni per l'analisi da parte del Centro sicurezza di Azure. Questi dati includono i log delle operazioni del cloud, le identità del dispositivo e la configurazione dell'hub. Tutte queste informazioni consentono di creare il Centro sicurezza di Azure per la pipeline di analisi dei dati.
 
Il Centro sicurezza di Azure per la pipeline di analisi Internet delle cose riceve anche flussi di intelligence per le minacce aggiuntivi da diverse fonti all'interno dei partner Microsoft e Microsoft Il Centro sicurezza di Azure per l'intera pipeline Analytics funziona con tutte le configurazioni dei clienti effettuate sul servizio, ad esempio gli avvisi personalizzati e l'uso dell'SDK del messaggio di sicurezza di invio.
 
Usando la pipeline di analisi, il Centro sicurezza di Azure combina tutti i flussi di informazioni per generare raccomandazioni e avvisi di utilità pratica. La pipeline contiene sia le regole personalizzate create dai ricercatori e gli esperti della sicurezza, sia i modelli di apprendimento automatico che cercano la deviazione dal comportamento del dispositivo standard e dall'analisi dei rischi.
 
Il Centro sicurezza di Azure per le raccomandazioni e gli avvisi relativi all'it (output della pipeline di analisi) viene scritto nell'area di lavoro Log Analytics di ogni cliente. L'inclusione degli eventi non elaborati nell'area di lavoro e degli avvisi e delle raccomandazioni consente l'analisi approfondita e le query usando i dettagli esatti delle attività sospette rilevate.  

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato l'architettura di base e il flusso di lavoro del Centro sicurezza di Azure per la soluzione Internet delle cose. Per altre informazioni sui prerequisiti, su come iniziare e abilitare la soluzione di sicurezza nell'hub Internet, vedere gli articoli seguenti:

- [Prerequisiti del servizio](service-prerequisites.md)
- [Introduzione](getting-started.md)
- [Configurare la soluzione](quickstart-configure-your-solution.md)
- [Abilitare la sicurezza nell'hub Internet](quickstart-onboard-iot-hub.md)
- [Domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)
- [Centro sicurezza di Azure per gli avvisi di sicurezza](concept-security-alerts.md)
