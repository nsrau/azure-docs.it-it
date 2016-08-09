<properties
	pageTitle="Personalizzazione di soluzioni preconfigurate | Microsoft Azure"
	description="Fornisce una guida alla personalizzazione delle soluzioni preconfigurate di Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/27/2016"
     ms.author="stevehob"/>

# Personalizzare una soluzione preconfigurata

Le soluzioni preconfigurate disponibili in Azure IoT Suite dimostrano come i servizi nella suite si integrano per fornire una soluzione end-to-end. Esistono poi diverse posizioni in cui è possibile personalizzare ed estendere la soluzione per adattarla a scenari specifici. Le sezioni seguenti descrivono questi punti di personalizzazione comuni.

## Ricerca del codice sorgente

Il codice sorgente per le soluzioni preconfigurate è disponibile in GitHub nei repository seguenti:

- Monitoraggio remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Manutenzione predittiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Il codice sorgente per le soluzioni preconfigurate viene fornito per illustrare i modelli e le procedure usate per implementare la funzionalità end-to-end di una soluzione IoT tramite Azure IoT Suite. È possibile trovare altre informazioni su come compilare e distribuire le soluzioni in repository GitHub.

## Modifica delle regole preconfigurate

La soluzione per il monitoraggio remoto include tre processi di [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) per implementare le informazioni sul dispositivo, la telemetria e la logica delle regole visualizzate per la soluzione.

I tre processi di analisi di flusso e la relativa sintassi sono descritti in dettaglio in [Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

È possibile modificare questi processi direttamente per alterare la logica o aggiungere una logica specifica allo scenario. È possibile trovare i processi di analisi di flusso come segue:
 
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a un nuovo gruppo di risorse con lo stesso nome della soluzione IoT.
3. Selezionare il processo di Analisi di flusso di Azure che si vuole modificare.
4. Arrestare il processo selezionando **Arresta** nel set di comandi.
5. Modificare i valori di input, query e output.

    Una modifica semplice consiste nel cambiare la query per il processo **Regole** in modo da usare **"<"** anziché **">"**. Il portale della soluzione visualizzerà ancora **">"** quando si modifica una regola, ma si noterà che il comportamento viene capovolto a causa della modifica del processo sottostante.

6. Avviare il processo

> [AZURE.NOTE] Il dashboard per il monitoraggio remoto dipende da dati specifici, quindi la modifica dei processi può causare un errore del dashboard.

## Aggiunta di regole personalizzate

Oltre a modificare i processi preconfigurati di analisi di flusso di Azure, è possibile usare il portale di Azure per aggiungere nuovi processi o nuove query ai processi esistenti.

## Personalizzazione dei dispositivi

Una delle attività di estensione più comuni è l'uso di dispositivi specifici per lo scenario. Esistono diversi metodi per usare i dispositivi, tra cui modificare un dispositivo simulato in modo che corrisponda allo scenario o usare l'[SDK per dispositivi IoT][] per connettere il dispositivo fisico alla soluzione.

Per una guida dettagliata sull'aggiunta di dispositivi alla soluzione preconfigurata per il monitoraggio remoto, vedere il documento relativo ai [dispositivi di connessione a Iot Suite](iot-suite-connecting-devices.md) e l'[esempio C SDK per il monitoraggio remoto](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring), studiato per usare la soluzione preconfigurata per il monitoraggio remoto.

### Creazione del dispositivo simulato

Nel codice sorgente della soluzione per il monitoraggio remoto (a cui viene fatto riferimento più indietro) è incluso un simulatore .NET. Il provisioning di questo simulatore viene eseguito nell'ambito della soluzione ed è possibile modificarlo per inviare metadati diversi e la telemetria o per rispondere a comandi diversi.

Nella soluzione preconfigurata per il monitoraggio remoto il simulatore preconfigurato rappresenta un dispositivo di raffreddamento che invia messaggi di telemetria su temperatura e umidità. Il simulatore può essere modificato nel progetto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) dopo aver duplicato il repository GitHub.

### Posizioni disponibili per i dispositivi simulati

Il set predefinito di posizioni si trova a Seattle/Redmond, Washington, Stati Uniti d'America. È possibile modificare queste posizioni nel file [SampleDeviceFactory.cs][lnk-sample-device-factory].


### Compilazione e uso del proprio dispositivo (fisico)

Gli [SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) forniscono librerie per la connessione di numerosi tipi di dispositivi (linguaggi e sistemi operativi) alle soluzioni IoT.

## Modifica dei limiti del dashboard

### Numero di dispositivi visualizzati nell'elenco a discesa del dashboard

Il valore predefinito è 200. È possibile modificare questo numero nel file [DashboardController.cs][lnk-dashboard-controller].

### Numero di pin da visualizzare nel controllo di Bing Mappe

Il valore predefinito è 200. È possibile modificare questo numero nel file [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### Periodo di tempo del grafico di dati di telemetria

Il valore predefinito è 10 minuti. È possibile modificare questo valore nel file [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## Configurazione manuale dei ruoli dell’applicazione

La procedura seguente descrive come aggiungere i ruoli applicazione **Admin** e **ReadOnly** a una soluzione preconfigurata. Si noti che le soluzioni preconfigurate per le quali è stato eseguito il provisioning dal sito azureiotsuite.com includono già i ruoli **Admin** e **ReadOnly**.

I membri del ruolo **ReadOnly** possono visualizzare il dashboard e l'elenco dei dispositivi, ma non sono autorizzati ad aggiungere dispositivi, modificare gli attributi del dispositivo o inviare comandi. I membri del ruolo **Admin** hanno accesso completo a tutte le funzionalità nella soluzione.

1. Passare al [portale di Azure classico][lnk-classic-portal].

2. Selezionare **Active Directory**.

3. Fare clic sul nome del tenant AAD usato durante il provisioning della soluzione.

4. Fare clic su **Applicazioni**.

5. Fare clic sul nome dell'applicazione che coincide con il nome della soluzione preconfigurata. Se l'applicazione non viene visualizzata nell'elenco, selezionare **Applicazioni di proprietà dell'azienda** nell'elenco a discesa **Mostra** e fare clic sul segno di spunta.

6.  Nella parte inferiore della pagina fare clic su **Gestisci manifesto** e quindi su **Scarica manifesto**.

7. Verrà scaricato un file con estensione JSON nel computer locale. Aprire il file per modificarlo in un editor di testo di propria scelta.

8. Nella terza riga del file con estensione JSON, è possibile trovare:

  ```
  "appRoles" : [],
  ```
  Sostituirlo con quanto riportato di seguito:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Salvare il file con estensione JSON aggiornato (è possibile sovrascrivere il file esistente).

10.  Nel portale di gestione di Azure, nella parte inferiore della pagina selezionare **Gestisci manifesto** e quindi **Carica manifesto** per caricare il file con estensione json salvato nel passaggio precedente.

11. Sono stati aggiunti all'applicazione i ruoli **Admin** e **ReadOnly**.

12. Per assegnare uno di questi ruoli a un utente nella directory, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions].

## Commenti e suggerimenti

Per altre informazioni relative a una personalizzazione, inviare suggerimenti a [UserVoice](https://feedback.azure.com/forums/321918-azure-iot) oppure lasciare un commento nell'apposita sezione alla fine di questo articolo.

## Passaggi successivi

Per altre informazioni sulle opzioni per personalizzare le soluzioni preconfigurate, vedere:

- [Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite][lnk-logicapp]
- [Usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto][lnk-dynamic]
- [Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[SDK per dispositivi IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0727_2016-->