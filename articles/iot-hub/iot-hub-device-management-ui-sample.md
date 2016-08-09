<properties
 pageTitle="Usare l'interfaccia utente di Gestione dei dispositivi dell'hub IoT | Microsoft Azure"
 description="Procedura dettagliata sull'uso dell'interfaccia utente di Gestione dei dispositivi dell'hub IoT"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/08/2016"
 ms.author="dobett"/>

# Esplorare la Gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio

L'interazione con l'interfaccia utente di Gestione dei dispositivi di esempio consente di consolidare i concetti e le funzionalità trattate negli articoli [Panoramica][lnk-dm-overview] e [Introduzione][lnk-get-started] di Gestione dei dispositivi dell'hub IoT di Azure. Questo articolo illustra i tre concetti principali di Gestione dei dispositivi: *dispositivo gemello*, *query su dispositivi* e *processi del dispositivo* come indicato nell'esempio dell'interfaccia utente Web di Gestione dei dispositivi.

Gli sviluppatori che desiderano compilare in modo indipendente l'esperienza interattiva di Gestione dei dispositivi possono usufruire della base di codici di esempio dell'interfaccia utente per realizzare un progetto personalizzato. È possibile consultare l'intero codice del progetto e i documenti Leggimi che descrivono in modo dettagliato funzionalità aggiuntive per gli sviluppatori nel repository di GitHub relativo all'[interfaccia utente di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-github].

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario completare le procedure contenute nell'articolo [Get started with Azure IoT Hub device management][lnk-get-started] (Introduzione a Gestione dei dispositivi dell'hub IoT di Azure). Se non è stata eseguita la procedura, tornare e completare tutti i passaggi descritti in questo articolo prima di procedere.

Al termine dell'esercitazione "Introduzione", è necessario disporre degli elementi seguenti nel sistema di test:

- Sei dispositivi simulati **iotdm\_simple\_sample** eseguiti in finestre della console/del terminal, ognuno con un messaggio di esito positivo "Registrato".

- L'applicazione di interfaccia utente di esempio di Gestione dei dispositivi compilata e in esecuzione locale all'indirizzo <http://127.0.0.1:3003>.

## Visualizzazione predefinita dei dispositivi

La schermata iniziale predefinita per l'interfaccia utente di esempio di Gestione dei dispositivi è la vista **Devices** (Dispositivi), che include i 5 componenti seguenti:

![][1]

1.  *Pulsanti di navigazione*: vista **Devices** (Dispositivi) (selezionata), **Job History** (Cronologia processo) e **Add a Device** (Aggiungi un dispositivo).

2. *Ricerca dispositivo*: trovare e modificare un singolo dispositivo dall'ID del dispositivo.

3.  *Azioni dispositivo*: azione **Modifica**, **Elimina** ed **Esporta**.

4.  *Processi dispositivo*: **Reboot** (Riavvia) dispositivo, **Firmware Update** (Aggiornamento firmware) e **Factory Reset** (Ripristino impostazioni predefinite).

5.  *Filtro griglia dispositivo*: editor di filtri per la compilazione e il salvataggio di viste personalizzate della griglia del dispositivo.

6.  *Griglia dispositivo*: consente di visualizzare tutti i dispositivi registrati con l'istanza dell'hub IoT con proprietà predefinite (**Device ID** (ID dispositivo), **Status** (Stato) e **Tags** (Tag).

La [panoramica di Gestione dei dispositivi][lnk-dm-overview] ha introdotto il concetto di *dispositivo gemello* che rappresenta un dispositivo fisico (o simulato) nell'hub IoT di Azure. Dalla griglia dei dispositivi è possibile selezionare un dispositivo registrato dall'elenco dei dispositivi per visualizzare e modificare il relativo dispositivo gemello.

Immettere questa vista dettagliata sul primo dispositivo simulato, **Device11 7ce4a850**, selezionando la riga del dispositivo corrispondente e quindi scegliendo il pulsante **Modifica**, facendo doppio clic sulla riga o immettendo l'ID del dispositivo nella casella di ricerca.

Si sta ora visualizzando la rappresentazione completa dei componenti del dispositivo gemello, in cui è possibile aggiornare le proprietà scrivibili ed eseguire altre operazioni sul dispositivo come indicato di seguito:

![][2]

1.  **Edit a Device Twin** (Modificare un dispositivo gemello): sono incluse le opzioni **Enabled/Disabled** (Abilitato/Disabilitato) per il dispositivo.

2.  **Service Properties** (Proprietà del servizio): sono inclusi i **Tags** (Tag) del dispositivo.

3.  **Device Properties** (Proprietà del dispositivo): fare clic per espandere questa sezione.

4.  Pulsante **Refresh** (Aggiorna): consente di recuperare le proprietà e i valori più recenti di un dispositivo gemello.

Per continuare, fare clic su **Annulla** nell'angolo in basso a destra di questa visualizzazione per tornare alla pagina dell'elenco dei dispositivi predefinito.

## Usare le query del dispositivo per filtrarne la visualizzazione

Le query del dispositivo rappresentano un potente strumento per individuare rapidamente un dispositivo o un gruppo di dispositivi con una particolare proprietà, ad esempio uno specifico tag. L'interfaccia utente di esempio usa le query del dispositivo per popolare l'elenco di dispositivi nella pagina dell'elenco di dispositivi predefinito. L'interfaccia utente di esempio consente di aggiungere e rimuovere le proprietà del servizio nella tabella e applicare filtri su alcune di queste proprietà.

Per creare un filtro personalizzato sul tag della proprietà del servizio "bacon", seguire questa procedura:

1.  Fare clic sull'icona a imbuto per aprire la visualizzazione di modifica delle query del dispositivo:

    ![][3]

2.  Fare clic su **+ Add a filter** (+ Aggiungi filtro) per espandere l'editor. Selezionare **Tags** (Tag) nell'elenco a discesa delle proprietà e immettere **bacon** nel campo di testo, quindi fare clic su **Applica**. Nell'elenco di dispositivi vengono ora visualizzati solo i tre dispositivi con il tag "bacon":

    ![][4]

3.  Nel campo del titolo della query, situato accanto all'icona a imbuto, assegnare il nome **Only Bacon** (Solo Bacon) alla query e fare clic su **Salva**:

    ![][5]

4.  Fare clic sull'icona a imbuto per uscire dall'editor delle query del dispositivo.

## Usare un processo del dispositivo per simulare il riavvio del dispositivo 

Come descritto nella panoramica di Gestione dei dispositivi, i processi del dispositivo consentono di orchestrare azioni semplici o complesse in uno o più dispositivi fisici. In questa sezione verrà creato un processo del dispositivo nell'interfaccia utente di esempio per eseguire un'operazione di riavvio su tutti i dispositivi simulati con il tag "bacon":

1.  Dall'elenco delle query del dispositivo **Only Bacon** (Solo Bacon), fare clic su ogni riga del dispositivo per selezionarla per il processo di riavvio:

    ![][6]

2.  Fare clic sul pulsante **Reboot** (Riavvia) sulla barra degli strumenti dei processi del dispositivo per creare il processo di riavvio. Dopo aver scelto **Sì**, fare clic sul collegamento ipertestuale **Job History** (Cronologia processo) nella finestra di dialogo **Job for Device has started** (Processo per il dispositivo avviato) per passare alla visualizzazione dei processi del dispositivo.

    ![][7]

È stato creato un unico processo padre che genera tre processi figlio, ognuno dei quali esegue l'operazione di riavvio su uno dei tre dispositivi con tag "bacon":

![][8]

L'aggiornamento di questa schermata dopo alcuni istanti modifica lo stato del processo padre e dei tre processi figlio su **completed** (completato), che indica che le operazioni di riavvio hanno avuto esito positivo e sono state confermate dai dispositivi simulati. Usare la colonna **Device ID** (ID dispositivo) per determinare i processi associati ai diversi dispositivi.


> [AZURE.NOTE] Se i processi figlio restituiscono lo stato "failed" (non riuscito), verificare che i dispositivi simulati siano ancora in esecuzione nel sistema di test. In caso contrario, eseguire nuovamente lo script simulate.bat o simulate.sh e ripetere la procedura del processo di riavvio del dispositivo riportata in alto.

## Passaggi successivi

L'esplorazione guidata dei concetti di Gestione dei dispositivi e l'uso della relativa interfaccia utente di esempio sono stati completati. Per ottenere informazioni più avanzate sulle API di Gestione dei dispositivi e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

- [Come usare il dispositivo gemello][lnk-tutorial-twin]
- [Come trovare dispositivi gemelli tramite query][lnk-tutorial-queries]
- [Come usare processi del dispositivo per aggiornare il firmware del dispositivo][lnk-tutorial-jobs]
- [Abilitare i dispositivi gestiti protetti da un gateway IoT][lnk-dm-gateway]
- [Introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure][lnk-library-c]

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Gestire hub IoT tramite il portale di Azure][lnk-portal]
- [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->