---
title: Informazioni sull'architettura del Centro sicurezza di Azure per la soluzione IoT Documenti Microsoft
description: Informazioni sul flusso di informazioni nel Servizio sicurezza di Azure per il servizio IoT.Learn about the flow of information in the Azure Security Center for IoT service.
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
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922171"
---
# <a name="azure-security-center-for-iot-architecture"></a>Centro sicurezza di Azure per IoT: architettura

Questo articolo illustra l'architettura del sistema funzionale della soluzione Centro sicurezza di Azure per IoT.This article explains the functional system architecture of the Azure Security Center for IoT solution. 

## <a name="azure-security-center-for-iot-components"></a>Centro sicurezza di Azure per i componenti IoTAzure Security Center for IoT components

Il Centro sicurezza di Azure per IoT è composto dai componenti seguenti:Azure Security Center for IoT is composed of the following components:
- Integrazione dell'hub IoT
- Agenti dispositivo (opzionale)
- Invia SDK per i messaggi di sicurezza
- Pipeline di analisi
 
### <a name="azure-security-center-for-iot-workflows"></a>Centro sicurezza di Azure per i flussi di lavoro IoTAzure Security Center for IoT workflows

Azure Security Center for IoT works in one of two feature workflows: Built-in and Enhanced  

### <a name="built-in"></a>Predefinito
In modalità predefinita, il Centro sicurezza di Azure per ioT è abilitato quando si sceglie di attivare l'opzione Sicurezza nell'hub IoT.In **Built-in** mode, Azure Security Center for IoT is enabled when you elect to turn on **the Security** option in your IoT Hub. Offrendo monitoraggio in tempo reale, consigli e avvisi, la modalità integrata offre visibilità del dispositivo in un solo passaggio e una sicurezza senza pari. La modalità build-in non richiede l'installazione dell'agente su alcun dispositivo e utilizza analisi avanzate sulle attività registrate per analizzare e proteggere il dispositivo sul campo. 

### <a name="enhanced"></a>Avanzato 
In modalità **avanzata,** dopo aver attivato l'opzione **Sicurezza** nell'hub IoT e aver installato il Centro sicurezza di Azure per gli agenti dispositivo IoT nei dispositivi, gli agenti raccolgono, aggregano e analizzano gli eventi di sicurezza non elaborati dai dispositivi. Gli eventi di sicurezza non elaborati possono includere connessioni IP, creazione di processi, account di accesso degli utenti e altre informazioni rilevanti per la sicurezza. Il Centro sicurezza di Azure per gli agenti di dispositivo IoT gestisce anche l'aggregazione di eventi per evitare una velocità effettiva di rete elevata. Gli agenti sono altamente personalizzabili, consentendo di utilizzarli per attività specifiche, ad esempio l'invio di informazioni importanti al contratto di servizio più veloce o per aggregare informazioni di sicurezza e contesto estesi in segmenti più grandi, evitando costi di servizio più elevati.

![Centro sicurezza di Azure per IoT: architettura](./media/architecture/azure-iot-security-architecture.png)
 
Gli agenti dispositivo e altre applicazioni usano **l'SDK** dei messaggi di sicurezza di Azure per inviare informazioni di sicurezza nell'hub IoT di Azure.Device agents, and other applications use the Azure send security message SDK to send security information into Azure IoT Hub. L'hub IoT preleva queste informazioni e le inoltra al Centro sicurezza di Azure per il servizio IoT.IoT Hub pick sups to this information and forwards to the Azure Security Center for IoT service.

Dopo aver abilitato il servizio Di sicurezza di Azure per IoT, oltre ai dati inoltrati, l'hub IoT invia anche tutti i dati interni per l'analisi da parte del Centro sicurezza di Azure per IoT.Once the Azure Security Center for IoT service is enabled, in addition to the forwarded data, IoT Hub also sends out all of its internal data for analysis by Azure Security Center for IoT. Questi dati includono i log delle operazioni del cloud del dispositivo, le identità del dispositivo e la configurazione dell'hub. Tutte queste informazioni consentono di creare il Centro sicurezza di Azure per la pipeline di analisi IoT.All of this information helps to create the Azure Security Center for IoT analytics pipeline.
 
La pipeline di analisi del Centro sicurezza di Azure per l'IoT riceve anche flussi di informazioni sulle minacce aggiuntivi da varie origini all'interno di Microsoft e dei partner Microsoft.Azure Security Center for IoT analytics pipeline also receives additional threat intelligence streams from various sources within Microsoft and Microsoft partners. Il Centro sicurezza di Azure per l'intera pipeline di analisi IoT funziona con ogni configurazione del cliente effettuata nel servizio ( ad esempio avvisi personalizzati e l'uso dell'SDK del messaggio di sicurezza di invio).
 
Usando la pipeline di analisi, Centro sicurezza di Azure per ioT combina tutti i flussi di informazioni per generare avvisi e suggerimenti utilizzabili. La pipeline contiene sia regole personalizzate create da ricercatori ed esperti di sicurezza, sia modelli di apprendimento automatico alla ricerca di deviazione dal comportamento standard del dispositivo e dall'analisi dei rischi.
 
Il Centro sicurezza di Azure per i suggerimenti e gli avvisi IoT (output della pipeline di analisi) viene scritto nell'area di lavoro Log Analytics di ogni cliente. L'inclusione degli eventi non elaborati nell'area di lavoro, nonché degli avvisi e dei consigli, consente indagini approfondite e query utilizzando i dettagli esatti delle attività sospette rilevate.  

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state apprese l'architettura di base e il flusso di lavoro della soluzione Centro sicurezza di Azure per IoT.In this article, you learned about the basic architecture and workflow of Azure Security Center for IoT solution. Per altre informazioni sui prerequisiti, come iniziare e abilitare la soluzione di sicurezza nell'hub IoT, vedere gli articoli seguenti:To learn more about prerequisites, how to get started and enable your security solution in IoT Hub, see the following articles:

- [Prerequisiti del servizio](service-prerequisites.md)
- [Introduzione](getting-started.md)
- [Configurare la soluzione](quickstart-configure-your-solution.md)
- [Abilitare la sicurezza nell'hub IoTEnable security in IoT Hub](quickstart-onboard-iot-hub.md)
- [Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)
- [Avvisi di sicurezza del Centro sicurezza di Azure per IoT](concept-security-alerts.md)
