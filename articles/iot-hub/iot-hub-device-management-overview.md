---
title: Panoramica dei dispositivi con l'hub IoT di Azure | Microsoft Docs
description: "Panoramica della gestione dei dispositivi nell'hub IoT di Azure: modelli di gestione del ciclo di vita dei dispositivi aziendali, ad esempio riavvio, ripristino delle impostazioni predefinite, aggiornamento del firmware, configurazione, dispositivi gemelli, query e processi."
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400984"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Panoramica della gestione dei dispositivi con l'hub IoT

L'hub IoT di Azure fornisce le funzionalità e un modello di estendibilità che consentono agli sviluppatori di dispositivi e sistemi di back-end di creare soluzioni affidabili per la gestione dei dispositivi. I dispositivi spaziano da sensori vincolati e microcontroller a scopo singolo, a potenti gateway che instradano le comunicazioni per gruppi di dispositivi.  Inoltre, i casi d'uso e i requisiti per gli operatori IoT variano notevolmente all'interno dei diversi settori.  Nonostante questa variabilità, la gestione dei dispositivi con l'hub IoT fornisce le funzionalità, i modelli e le librerie di codice necessari per soddisfare un insieme eterogeneo di dispositivi e utenti finali.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Una parte essenziale della creazione di una soluzione IoT enterprise è offrire una strategia per la gestione continuativa della raccolta di dispositivi da parte degli operatori. Gli operatori IoT necessitano di strumenti e applicazioni semplici e affidabili che consentano di concentrarsi sugli aspetti più strategici del loro lavoro. Questo articolo include:

* Una rapida panoramica sull'approccio dell'hub IoT di Azure alla gestione dei dispositivi.
* Una descrizione dei comuni principi di gestione dei dispositivi.
* Una descrizione del ciclo di vita dei dispositivi.
* Una panoramica sui comuni modelli di gestione dei dispositivi.

## <a name="device-management-principles"></a>Principi di gestione dei dispositivi

All'IoT è associato un esclusivo insieme di sfide di gestione dei dispositivi e ogni soluzione aziendale deve tenere in considerazione i principi riportati di seguito:

![Grafico dei principi di gestione dei dispositivi](media/iot-hub-device-management-overview/image4.png)

* **Scalabilità e automazione**: Le soluzioni IoT richiedono strumenti semplici che possono automatizzare le attività di routine e abilitare uno staff operativo relativamente ridotto di gestire milioni di dispositivi. Quotidianamente, gli operatori si aspettano di gestire le operazioni dei dispositivi in remoto e in blocco ricevendo avvisi solo quando si verificano problemi che richiedono attenzione diretta.

* **Apertura e compatibilità**: L'ecosistema dei dispositivi è straordinariamente eterogeneo. Gli strumenti di gestione devono essere ottimizzati per gestire una vasta gamma di protocolli, piattaforme e classi di dispositivi. È necessario che gli operatori possano supportare molti tipi di dispositivi, dai più limitati chip incorporati per singoli processi a computer potenti e dotati di funzionalità complete.

* **Riconoscimento del contesto**: Gli ambienti IoT sono dinamici e in continua evoluzione. L'affidabilità del servizio è fondamentale. Le operazioni di gestione dei dispositivi devono tener conto dei fattori seguenti perché i tempi di inattività per la manutenzione non influiscano su operazioni aziendali critiche o creino condizioni di pericolo:

    * Finestre di manutenzione del contratto di servizio
    * Stato di rete e di alimentazione
    * Condizioni in uso
    * Georilevazione dei dispositivi

* **Molti ruoli del servizio**: Supporto per i flussi di lavoro univoci e i processi di ruoli di operazioni IoT è fondamentale. Il personale operativo deve poter lavorare in armonia con i vincoli specificati dei reparti IT interni.  Inoltre, deve trovare modi sostenibili per segnalare informazioni operative sui dispositivi in tempo reale ai supervisori e agli altri ruoli di gestione aziendale.

## <a name="device-lifecycle"></a>Ciclo di vita dei dispositivi
Esistono alcune fasi generali per la gestione dei dispositivi, comuni a tutti i progetti ioT enterprise. In IoT di Azure, ci sono cinque fasi nel ciclo di vita dei dispositivi:

![Le cinque fasi del ciclo di vita dei dispositivi Azure IoT: pianificazione, provisioning, configurazione, monitoraggio e ritiro](./media/iot-hub-device-management-overview/image5.png)

Ciascuna di queste cinque fasi prevede diversi requisiti per gli operatori dei dispositivi che devono essere soddisfatte per fornire una soluzione completa:

* **Pianificare**: Gli operatori possono creare uno schema di metadati di dispositivo che ne consente facilmente e accuratamente eseguire una query per un gruppo di dispositivi per le operazioni di gestione in blocco di destinazione. È possibile usare il dispositivo gemello per archiviare i metadati del dispositivo sotto forma di tag e proprietà.
  
    *Altre informazioni*: 
    * [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md)
    * [Informazioni sui dispositivi gemelli](iot-hub-devguide-device-twins.md)
    * [Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Provision**: Effettuare il provisioning di nuovi dispositivi all'IoT Hub e gli operatori possono individuare immediatamente le funzionalità di dispositivo in modo sicuro.  Usare il registro di identità dell'hub IoT per creare credenziali e identità flessibili per i dispositivi ed eseguire l'operazione in blocco tramite un processo. Creare dispositivi per creare report delle funzionalità e delle condizioni tramite le proprietà del dispositivo nel dispositivo gemello.
  
    *Altre informazioni*: 
    * [Gestire le identità dei dispositivi](iot-hub-devguide-identity-registry.md)
    * [Gestione in blocco delle identità dei dispositivi](iot-hub-bulk-identity-mgmt.md)
    * [Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)
    * [Servizio Device Provisioning in hub IoT di Azure](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurare**: Facilitare le modifiche di configurazione delle operazioni bulk e gli aggiornamenti del firmware ai dispositivi, mantenendo l'integrità e sicurezza. Per eseguire queste operazioni di gestione dei dispositivi in blocco, usare le proprietà desiderate oppure metodi diretti e processi di trasmissione.
  
    *Altre informazioni*:
    * [Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)
    * [Configurare e monitorare i dispositivi IoT su larga scala](iot-hub-auto-device-config.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Monitoraggio**: Monitorare l'integrità complessiva di raccolta dispositivi, lo stato di operazioni in corso e degli avvisi agli operatori i problemi che potrebbero richiedere la loro attenzione.  Applicare il dispositivo gemello per consentire ai dispositivi di creare report in tempo reale sulle condizioni operative e sullo stato delle operazioni di aggiornamento. Creare report avanzati sui dashboard per mostrare i problemi più immediati mediante le query su dispositivo gemello.
  
    *Altre informazioni*: 
    * [Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)
    * [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
    * [Configurare e monitorare i dispositivi IoT su larga scala](iot-hub-auto-device-config.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Ritirare**: Sostituire o rimuovere le autorizzazioni di dispositivi dopo un errore, ciclo di aggiornamento o alla fine della durata del servizio.  Usare il dispositivo gemello per mantenere le informazioni sul dispositivo se il dispositivo fisico viene sostituito o archiviato in caso di ritiro. Usare il registro di identità dell'hub IoT per revocare in modo sicuro le credenziali e le identità del dispositivo.
  
    *Altre informazioni*: 
    * [Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)
    * [Gestire le identità dei dispositivi](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Modelli di gestione dei dispositivi

L'hub IoT supporta il set seguente di modelli di gestione dei dispositivi. Le [esercitazioni sulla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md) illustrano con maggior dettaglio come estendere questi modelli per adattarli perfettamente allo scenario e come progettare nuovi modelli basati sui modelli di base.

* **Reboot**: L'app back-end Usa comunicare al dispositivo tramite un metodo diretto che è stato avviato un riavvio.  Il dispositivo usa le proprietà segnalate per aggiornare lo stato di riavvio del dispositivo.
  
    ![Grafico del modello di riavvio della gestione dei dispositivi](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Ripristino delle impostazioni predefinite**: L'app back-end Usa comunicare al dispositivo tramite un metodo diretto che è stato avviato un ripristino delle impostazioni predefinite. Il dispositivo usa le proprietà segnalate per aggiornare lo stato del ripristino delle impostazioni predefinite del dispositivo.
  
    ![Grafico del modello di ripristino delle impostazioni predefinite della gestione dei dispositivi](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configurazione**: L'app back-end Usa le proprietà desiderate per configurare il software in esecuzione nel dispositivo. Il dispositivo usa le proprietà segnalate per aggiornare lo stato di configurazione del dispositivo.
  
    ![Grafico del modello di configurazione della gestione dei dispositivi](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Aggiornamento del firmware**: L'app back-end Usa una configurazione di gestione automatica dei dispositivi per selezionare i dispositivi che riceveranno l'aggiornamento, per indicare dove trovare l'aggiornamento e per monitorare il processo di aggiornamento dei dispositivi. Il dispositivo avvia un processo in più passaggi per scaricare, verificare e applicare l'immagine del firmware e quindi eseguire il riavvio prima di riconnettersi al servizio dell'hub IoT. Durante i diversi passaggi di questo processo, il dispositivo usa le proprietà segnalate per aggiornare avanzamento e stato del dispositivo.
  
    ![Grafico del modello di aggiornamento del firmware della gestione dei dispositivi](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Segnalare lo stato e stato**: Query end viene eseguito sui dispositivi gemelli, nuovamente la soluzione in un set di dispositivi per creare report sullo stato e sull'avanzamento delle azioni in esecuzione nei dispositivi.
  
    ![Grafico del modello di creazione di report sull'avanzamento e sullo stato della gestione dei dispositivi](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Fasi successive

Le funzionalità, i modelli e le librerie di codice forniti dall'hub IoT per la gestione dei dispositivi consentono di creare applicazioni IoT che soddisfano i requisiti degli operatori IoT aziendali in ogni fase del ciclo di vita dei dispositivi.

Per altre informazioni sulle funzionalità di gestione dei dispositivi nell'hub IoT, vedere l'esercitazione [Get started with device management](iot-hub-node-node-device-management-get-started.md) (Introduzione alla gestione dei dispositivi).