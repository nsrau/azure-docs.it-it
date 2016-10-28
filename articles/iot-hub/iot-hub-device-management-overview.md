<properties
 pageTitle="Panoramica di Gestione dei dispositivi | Microsoft Azure"
 description="Panoramica di Gestione dei dispositivi dell'hub IoT di Azure"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>



# Panoramica di Gestione dei dispositivi dell'hub IoT di Azure (anteprima)

## Approccio di gestione dei dispositivi IoT di Azure

La gestione dei dispositivi dell'hub IoT di Azure offre le funzionalità e il modello di estendibilità per dispositivi e back-end che consentono di sfruttare la gestione dei dispositivi IoT per la vasta gamma di dispositivi e protocolli dell'IoT. Nell'ambito dell'IoT, i dispositivi includono da sensori molto limitati e microcontroller finalizzati a un unico scopo a gateway più potenti che supportano altri dispositivi e protocolli. Anche le soluzioni IoT variano considerevolmente all'interno dei settori e delle applicazioni verticali, con casi d'uso esclusivi per gli operatori nei singoli settori. Le soluzioni IoT possono sfruttare le funzionalità, i modelli e le librerie di codice per la gestione dei dispositivi dell'hub IoT per supportare la gestione dei dispositivi per set eterogenei di dispositivi e utenti.

## Introduzione

Una parte essenziale della creazione di una soluzione IoT efficiente è offrire una strategia per la gestione continuativa dell'insieme dei dispositivi da parte degli operatori. Gli operatori IoT necessitano di strumenti e applicazioni sia semplici che affidabili che consentano di concentrarsi sugli aspetti più strategici del loro lavoro. L'hub IoT di Azure offre blocchi predefiniti per creare applicazioni IoT che facilitino i più importanti modelli di gestione dei dispositivi.

I dispositivi si considerano gestiti dall'hub IoT quando eseguono una semplice applicazione denominata agente di gestione dei dispositivi che connette in modo sicuro il dispositivo al cloud. Il codice dell'agente consente a un operatore sul lato applicazione di attestare in remoto lo stato del dispositivo ed eseguire operazioni di gestione come l'applicazione di modifiche alla configurazione della rete o la distribuzione di aggiornamenti del firmware.

## Principi di gestione dei dispositivi IoT

All'IoT è associato un esclusivo insieme di sfide di gestione e una soluzione deve tenere in considerazione i principi di gestione dei dispositivi IoT riportati di seguito.

![][img-dm_principles]

- **Scalabilità e automazione**: l'IoT richiede strumenti semplici per automatizzare le attività di routine e consentire a uno staff operativo relativamente ridotto di gestire milioni di dispositivi. Quotidianamente, gli operatori si aspettano di gestire le operazioni dei dispositivi in remoto e in blocco ricevendo avvisi solo quando si verificano problemi che richiedono attenzione diretta.

- **Apertura e compatibilità**: l'ecosistema dei dispositivi IoT è straordinariamente eterogeneo. Gli strumenti di gestione devono essere ottimizzati per gestire una vasta gamma di protocolli, piattaforme e classi di dispositivi. È necessario che gli operatori possano supportare tutti i dispositivi, dai più limitati chip incorporati per singoli processi a computer potenti e dotati di funzionalità complete.

- **Riconoscimento del contesto**: gli ambienti IoT sono dinamici e in continua evoluzione. L'affidabilità del servizio è fondamentale. Le operazioni di gestione dei dispositivi devono considerare le finestre di manutenzione del contratto di servizio, lo stato di rete e di alimentazione, le condizioni in uso e la georilevazione dei dispositivi affinché i tempi di inattività di manutenzione non influiscano su operazioni aziendali critiche o creino condizioni di pericolo.

- **Servizio per diversi ruoli**: il supporto degli esclusivi processi e flussi di lavoro dei ruoli operativi dell'IoT è essenziale. Lo staff operativo deve anche armonizzarsi con gli specifici vincoli dei reparti IT interni e sottoporre le informazioni rilevanti sul funzionamento dei dispositivi a supervisori e altri ruoli di gestione.

## Ciclo di vita dei dispositivi IoT 

Nonostante i progetti IoT varino considerevolmente, esiste un set di modelli comuni per la gestione dei dispositivi. In Azure IoT, questi modelli sono identificati all'interno di un ciclo di vita dei dispositivi IoT costituito da cinque fasi distinte:

![][img-device_lifecycle]

1. **Pianificazione**: gli operatori possono creare uno schema di proprietà dei dispositivi che consentirà di eseguire query su un gruppo di dispositivi e indirizzare le operazioni di gestione in blocco in modo facile e accurato.

    *Blocchi predefiniti correlati*: [Introduzione ai dispositivi gemelli][lnk-twins-getstarted], [Come usare le proprietà dei dispositivi gemelli][lnk-twin-properties]

2. **Provisioning**: i nuovi dispositivi vengono autenticati in modo sicuro nell'hub IoT e gli operatori possono individuare immediatamente le funzionalità dei dispositivi e lo stato corrente.

    *Blocchi predefiniti correlati*: [Introduzione all'hub IoT][lnk-hub-getstarted], [Come usare le proprietà dei dispositivi gemelli][lnk-twin-properties]

3. **Configurazione**: vengono facilitate le operazioni in blocco di modifica alla configurazione e aggiornamento del firmware dei dispositivi mantenendo al tempo stesso integrità e sicurezza.

    *Blocchi predefiniti correlati*: [Come usare le proprietà dei dispositivi gemelli][lnk-twin-properties], [Metodi C2D][lnk-c2d-methods], [Pianificare e trasmettere i processi][lnk-jobs]

4. **Monitoraggio**: vengono monitorati l'integrità complessiva dell'insieme dei dispositivi e lo stato delle implementazioni di aggiornamenti in corso per segnalare agli operatori i problemi che potrebbero richiedere la loro attenzione.

    *Blocchi predefiniti correlati*: [Come usare le proprietà dei dispositivi gemelli][lnk-twin-properties]

5. **Ritiro**: i dispositivi vengono sostituiti o ritirati dopo un guasto o un ciclo di aggiornamento oppure alla fine della vita utile.

    *Blocchi predefiniti correlati*:
    
## Modelli di gestione dei dispositivi dell'hub IoT

L'hub IoT supporta il set seguente di modelli (iniziali) di gestione dei dispositivi. Come illustrato nelle [esercitazioni][lnk-get-started], è possibile estendere questi modelli per adattarli esattamente al proprio scenario e progettare nuovi modelli per altri scenari su questi modelli di base.

1. **Riavvio**: l'applicazione back-end comunica al dispositivo tramite un metodo D2C che è stato avviato un riavvio. Il dispositivo usa le proprietà segnalate del dispositivo gemello per aggiornare il proprio stato di riavvio.

    ![][img-reboot_pattern]

2. **Ripristino delle impostazioni predefinite**: l'applicazione back-end comunica al dispositivo tramite un metodo D2C che è stato avviato un ripristino delle impostazioni predefinite. Il dispositivo usa le proprietà segnalate del dispositivo gemello per aggiornare il proprio stato di ripristino delle impostazioni predefinite.

    ![][img-facreset_pattern]

3. **Configurazione**: l'applicazione back-end usa le proprietà desiderate del dispositivo gemello per configurare il software in esecuzione nel dispositivo. Il dispositivo usa le proprietà segnalate del dispositivo gemello per aggiornare il proprio stato di configurazione.

    ![][img-config_pattern]

4. **Aggiornamento del firmware**: l'applicazione back-end comunica al dispositivo tramite un metodo D2C che è stato avviato un aggiornamento del firmware. Il dispositivo avvia un processo in più passaggi per scaricare il pacchetto del firmware, applicarlo e infine riconnettersi al servizio hub IoT. Durante questo processo in più passaggi, il dispositivo usa le proprietà segnalate del dispositivo gemello per aggiornare il proprio avanzamento e stato.

    ![][img-fwupdate_pattern]

5. **Creazione di report sull'avanzamento e sullo stato**: l'applicazione back-end esegue query sui dispositivi gemelli in un set di dispositivi per creare report sullo stato e sull'avanzamento delle azioni in esecuzione nel dispositivo.

    ![][img-report_progress_pattern]

## Passaggi successivi

Usando i blocchi predefiniti offerti dall'hub IoT di Azure, gli sviluppatori possono creare applicazioni IoT che soddisfino gli esclusivi requisiti degli operatori IoT in ogni fase del ciclo di vita dei dispositivi.

Per altre informazioni sulle funzionalità di gestione dei dispositivi dell'hub IoT di Azure, vedere l'esercitazione di [introduzione alla gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->