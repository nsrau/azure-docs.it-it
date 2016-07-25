<properties
 pageTitle="Panoramica di Gestione dei dispositivi | Microsoft Azure"
 description="Panoramica di Gestione dei dispositivi dell'hub IoT di Azure: dispositivi gemelli, query su dispositivi, processi del dispositivo"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Panoramica di Gestione dei dispositivi dell'hub IoT di Azure (anteprima)

Gestione dei dispositivi dell'hub IoT di Azure consente la gestione dei dispositivi IoT basati su standard per gestire, configurare e aggiornare i dispositivi in modalità remota.

La gestione dei dispositivi in IoT di Azure include tre concetti principali:

1.  **Dispositivo gemello:** rappresentazione del dispositivo fisico nell'hub IoT.

2.  **Query su dispositivi**: consente di trovare dispositivi gemelli e generare informazioni aggregate di più dispositivi gemelli. È ad esempio possibile eseguire query per trovare tutti i dispositivi gemelli con la versione firmware 1.0.

3.  **Processi del dispositivo**: azione da eseguire in uno o più dispositivi fisici, ad esempio aggiornamento del firmware, riavvio e ripristino delle impostazioni predefinite.

## Dispositivo gemello

Il dispositivo gemello è rappresentazione di un dispositivo fisico in IoT di Azure. Per rappresentare il dispositivo gemello, viene usato l'oggetto **Microsoft.Azure.Devices.Device**.

![][img-twin]

Il dispositivo gemello include i componenti seguenti:

1.  **Campi del dispositivo:** i campi del dispositivo sono proprietà predefinite usate sia per la messaggistica che la gestione dei dispositivi dell'hub IoT di Azure. Questi campi consentono all'hub IoT di identificare i dispositivi fisici e di connettersi ad essi. I campi del dispositivo non sono sincronizzati con il dispositivo e sono archiviati esclusivamente nel dispositivo gemello. Tra questi campi son inclusi l'ID dispositivo e le informazioni di autenticazione.

2.  **Proprietà del dispositivo:** le proprietà del dispositivo sono costituite da un dizionario predefinito di proprietà che descrivono il dispositivo fisico. Il dispositivo fisico è il dispositivo master di ogni proprietà del dispositivo e costituisce l'archivio autorevole di ogni valore corrispondente. Una rappresentazione che risulterà infine coerente di queste proprietà è archiviata nel dispositivo gemello nel cloud. La coerenza e l'aggiornamento sono soggetti alle impostazioni di sincronizzazione, descritte in [Esercitazione: Come usare il dispositivo gemello][lnk-tutorial-twin]. Tra gli esempi di proprietà del dispositivo sono inclusi la versione del firmware, il livello della batteria e il nome del produttore.

3.  **Proprietà del servizio:** le proprietà del servizio sono coppie **&lt;chiave-valore&gt;** che lo sviluppatore aggiunge al dizionario delle proprietà del servizio. Queste proprietà estendono il modello di dati per il dispositivo gemello, per consentire di identificare meglio il dispositivo. Le proprietà del servizio non sono sincronizzate con il dispositivo e sono archiviate esclusivamente nel dispositivo gemello nel cloud. Un esempio di proprietà del servizio è **&lt;NextServiceDate, 11/12/2017&gt;**, che può essere usata per trovare i dispositivi in base alla data di manutenzione successiva.

4.  **Tag:** i tag sono un subset di proprietà del servizio costituiti da stringhe arbitrarie anziché da proprietà del dizionario. Possono essere usati per aggiungere note ai dispositivi gemelli o per organizzare i dispositivi in gruppi. I tag non sono sincronizzati con il dispositivo e sono archiviati esclusivamente nel dispositivo gemello. Se, ad esempio, il dispositivo gemello rappresenta un furgone fisico, è possibile aggiungere un tag per ogni tipo di carico nel furgone, come **mele**, **arance** e **banane**.

## Query su dispositivi

Nella sezione precedente sono stati illustrati i vari componenti del dispositivo gemello. Ora verrà descritto come trovare i dispositivi gemelli nel registro dei dispositivi dell'hub IoT in base alle proprietà del dispositivo, alle proprietà del servizio o ai tag. Si può eseguire una query sui dispositivi per trovare i dispositivi che devono essere aggiornati. È possibile eseguire una query che restituisca tutti i dispositivi con la versione del firmware specificata ed eseguire l'input del risultato in un'azione specifica, nota nell'hub IoT come processo del dispositivo. Questo tipo di azione verrà descritto nella sezione seguente.

È possibile eseguire query usando tag e proprietà:

-   Per eseguire una query che restituisca i dispositivi gemelli usando i tag, è necessario passare una matrice di stringhe. La query restituirà il set di dispositivi taggati con tutte le stringhe passate.

-   Per eseguire una query che restituisca i dispositivi gemelli usando le proprietà del servizio o le proprietà del dispositivo, è necessario usare un'espressione di query JSON. L'esempio seguente mostra come eseguire una query che restituisca tutti i dispositivi la cui proprietà del dispositivo include la chiave **FirmwareVersion** e il valore **1.0**. È possibile osservare che il tipo **type** della proprietà è **device**. Questo indica che la query viene eseguita in base alle proprietà del dispositivo e non alle proprietà del servizio.

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Processi del dispositivo

Il concetto successivo nella gestione dei dispositivi riguarda i processi del dispositivo, che consentono il coordinamento di orchestrazioni a più fasi su più dispositivi.

Al momento esistono sei tipi di processi del dispositivo forniti da Gestione dei dispositivi dell'hub IoT di Azure. Ne verranno aggiunti altri in futuro in base alle esigenze dei clienti.

- **Aggiornamento del firmware**: aggiorna il firmware, o l'immagine del sistema operativo, nel dispositivo fisico.
- **Riavvio**: riavvia il dispositivo fisico.
- **Ripristino delle impostazioni predefinite**: ripristina il firmware, o l'immagine del sistema operativo, a un'immagine di backup predefinita archiviata nel dispositivo.
- **Aggiornamento della configurazione**: configura l'agente client dell'hub IoT in esecuzione nel dispositivo fisico.
- **Lettura proprietà del dispositivo**: ottiene il valore più recente di una proprietà del dispositivo nel dispositivo fisico.
- **Scrittura proprietà del dispositivo:** modifica una proprietà del dispositivo nel dispositivo fisico.

Per informazioni dettagliate su come usare ognuno di questi processi, vedere la [documentazione dell'API per C# e Node.js][lnk-apidocs].

Un processo può operare in più dispositivi. Quando si avvia un processo, viene creato un processo figlio associato per ogni dispositivo. Un processo figlio opera in un solo dispositivo. Ogni processo figlio contiene un puntatore al relativo processo padre. Il processo padre è solo un contenitore per i processi figlio e non implementa alcuna logica per distinguere i tipi di dispositivi, ad esempio per distinguere l'aggiornamento di un dispositivo Intel Edison e l'aggiornamento di un dispositivo Raspberry Pi. Il diagramma seguente illustra la relazione tra un processo padre, i relativi processi figlio e i dispositivi fisici associati.

![][img-jobs]

Per informazioni sullo stato dei processi che sono stati avviati, è possibile eseguire una query sulla cronologia processi. Per alcune query di esempio, vedere la [libreria di query][lnk-query-samples].

## Implementazione nel dispositivo

Dopo aver descritto i concetti lato server, è ora possibile illustrare come si crea un dispositivo fisico gestito. La libreria client di Gestione dei dispositivi dell'hub IoT di Azure consente di gestire i dispositivi IoT con l'hub IoT di Azure. "Gestire" significa anche riavviare, ripristinare le impostazioni predefinite e aggiornare il firmware. Attualmente viene fornita una libreria C indipendente dalla piattaforma, ma presto verrà aggiunto il supporto per altri linguaggi.

La libreria client di Gestione dei dispositivi ha due responsabilità principali nella gestione dei dispositivi:

- Sincronizzare le proprietà nel dispositivo fisico con il dispositivo gemello corrispondente nell'hub IoT
- Pianificare i processi del dispositivo inviati dall'hub IoT al dispositivo

Per altre informazioni su queste responsabilità e sull'implementazione nel dispositivo fisico, vedere l'[introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure per C][lnk-library-c].

## Passaggi successivi

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si utilizzano gli SDK, è possibile scegliere tra una serie di protocolli di rete per comunicare con l’hub IoT. Per ulteriori informazioni, vedere [Informazioni sugli SDK del dispositivo][lnk-device-sdks].

Per ottenere altre informazioni sulle funzionalità di Gestione dei dispositivi dell'hub IoT di Azure, vedere l'esercitazione [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started].

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0713_2016-->