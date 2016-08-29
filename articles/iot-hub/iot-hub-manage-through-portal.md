<properties
	 pageTitle="Usare il portale di Azure per gestire gli hub IoT | Microsoft Azure"
	 description="Panoramica delle operazioni di creazione e gestione di hub IoT di Azure tramite il portale di Azure."
	 services="iot-hub"
	 documentationCenter=""
	 authors="nasing"
	 manager="timlt"
	 editor=""/>

<tags
	 ms.service="iot-hub"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.tgt_pltfrm="na"
	 ms.workload="na"
	 ms.date="08/11/2016"
	 ms.author="nasing"/>  

# Gestire hub IoT tramite il portale di Azure

## Introduzione

Questo articolo fornisce un'introduzione all'uso dell'hub IoT di Azure tramite il portale di Azure, come trovare hub IoT e come crearli e gestirli.

## Dove trovare hub IoT

Gli hub IoT possono essere situati in diverse posizioni.

1. **+ Nuovo**: **hub IoT di Azure** è un servizio IoT e si trova nella categoria **Internet delle cose** in **+ Nuovo**, come altri servizi.

2. È possibile accedere agli hub IoT anche tramite il Marketplace come servizio in **Internet delle cose**.

## Creare un hub IoT

È possibile creare un hub IoT usando i metodi seguenti.

1. La creazione di un hub IoT con l'opzione **+ Nuovo** consente di accedere al pannello visualizzato nell'immagine seguente. I passaggi per la creazione di un hub IoT tramite questo metodo e tramite il Marketplace sono identici.

2. Creazione di un hub IoT tramite il Marketplace: se si fa clic su **Crea** viene aperto un pannello identico a quello precedente per l'esperienza **+Nuovo**. Le sezioni seguenti elencano i numerosi passaggi previsti per la creazione di un hub IoT.

### Scegliere il nome dell'hub IoT

Per creare un hub IoT, è necessario assegnare un nome all'hub. Si noti che questo nome deve essere univoco tra tutti gli hub. Non è consentita la duplicazione degli hub nel back-end, quindi è consigliabile assegnare all'hub un nome quanto più possibile univoco.

### Scegliere il piano tariffario

È possibile scegliere fra quattro piani: **Gratuito**, **Standard 1** e **Standard 2** e **Standard S3**. Il piano gratuito consente la connessione di solo 500 dispositivi all'hub IoT e di un massimo di 8.000 messaggi al giorno.

**Standard S1**: l'edizione Hub IoT S1 è progettata per le soluzioni IoT con un numero elevato di dispositivi che generano quantità relativamente limitate di dati per dispositivo. Ogni unità dell'edizione S1 consente fino a 400.000 messaggi al giorno tra tutti i dispositivi.

**Standard S2**: l'edizione Hub IoT S2 è progettata per le soluzioni IoT in cui i dispositivi generano grandi quantità di dati. Ogni unità dell'edizione S2 consente fino a 6 milioni di messaggi al giorno tra tutti i dispositivi connessi.

**Standard S3**: l'edizione Hub IoT S3 è progettata per soluzioni IoT che generano grandi quantità di dati. Ogni unità dell'edizione S3 consente fino a 300 milioni di messaggi al giorno tra tutti i dispositivi connessi.

![][4]

> [AZURE.NOTE] L'hub IoT consente solo un hub gratuito per sottoscrizione.

### Unità hub IoT

Un'unità hub IoT include un certo numero di messaggi al giorno. La scelta del numero di unità IoT comporta che il numero totale di messaggi supportati per questo hub corrisponde al numero di unità moltiplicato per il numero di messaggi al giorno per il piano. Ad esempio, se si desidera che l'hub IoT supporti 700.000 messaggi in entrata, selezionare due unità del piano S1.

### Partizioni da dispositivo a cloud e gruppo di risorse

È possibile modificare il numero di partizioni per un hub IoT. Le partizioni predefinite sono impostate su 4, ma è possibile scegliere un altro numero di partizioni da un elenco a discesa.

Per i gruppi di risorse non è necessario creare in modo esplicito un gruppo di risorse vuoto. Quando si crea un nuovo gruppo, è possibile scegliere di creare un nuovo gruppo di risorse o usarne uno esistente.

![][5]

### Scegliere le sottoscrizioni

L'hub IoT di Azure mostra automaticamente un elenco di sottoscrizioni alle quali sono collegati gli account utente. È possibile scegliere una delle opzioni disponibili per associare l'hub IoT a una sottoscrizione.

### Scegliere la località

L'opzione relativa alla posizione offre un elenco delle aree in cui viene offerto l'hub IoT. Hub IoT è disponibile per la distribuzione nelle località seguenti Australia orientale, Australia sud-orientale, Asia orientale, Asia sudorientale, Europa settentrionale, Europa occidentale, Giappone orientale, Giappone occidentale, Stati Uniti orientali, Stati Uniti occidentali.

### Creare l'hub IoT

Dopo aver completato tutti i passaggi precedenti, l'hub IoT è pronto per essere creato. Fare clic su **Crea** per avviare il processo di back-end per la creazione di questo hub IoT con le opzioni specifiche e distribuirlo nella posizione specificata.

Possono essere necessari alcuni minuti per la creazione dell'hub IoT, perché l'esecuzione della distribuzione del back-end nei server delle località appropriate richiede tempo.

## Modificare le impostazioni dell'hub IoT

È possibile modificare le impostazioni di un hub IoT esistente dopo averlo creato dal pannello Hub IoT.

![][8]  

**Criteri di accesso condiviso**: questi criteri definiscono le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. È possibile accedere a questi criteri facendo clic su **Criteri di accesso condiviso** in **Generale**. In questo pannello è possibile modificare i criteri esistenti o aggiungerne di nuovi.

### Creare nuovi criteri

- Fare clic su **Aggiungi** per aprire un pannello in cui è possibile immettere il nome dei nuovi criteri e le autorizzazioni da associare a questi criteri, come illustrato nella figura seguente.

	Sono disponibili numerose autorizzazioni che possono essere associate a questi criteri condivisi. I primi due criteri di **lettura del Registro di sistema ** e di **scrittura del Registro di sistema** consentono di concedere diritti di accesso in lettura e scrittura per l'archivio identità del dispositivo o per il registro delle identità. Scegliendo l'opzione di scrittura, verrà scelta automaticamente l'opzione di lettura.

 	I criteri di **Connessione servizio** concedono le autorizzazioni per accedere agli endpoint lato cloud, ad esempio il gruppo di consumatori dei servizi collegati all'hub IoT. I criteri di **Connessione dispositivo** concedono le autorizzazioni per l'invio e la ricezione di messaggi sugli endpoint lato dispositivo dell'hub IoT.

- Fare clic su **Crea** per aggiungere i criteri appena creati all'elenco esistente.

![][10]

## Messaggistica

Fare clic su **Messaggistica** per visualizzare un elenco di proprietà di messaggistica per l'hub IoT che si sta modificando. Sono disponibili due tipi di proprietà principali che possono essere modificati o copiati: **Da cloud a dispositivo** e **Da dispositivo a cloud**.

- Impostazioni **da cloud a dispositivo**: questa impostazione include due impostazioni secondarie **Durata (TTL) da cloud a dispositivo** e **Tempo di conservazione** per i messaggi. Quando si crea l'hub IoT inizialmente, entrambe queste impostazioni vengono create con un valore predefinito di un'ora. Per modificare questi valori usare i dispositivi di scorrimento o digitare i valori.

- Impostazioni **da dispositivo a cloud**: questa impostazione include diverse impostazioni secondarie, alcune delle quali denominate/assegnate quando si crea l'hub IoT e possono essere copiate solo in altre impostazioni secondarie personalizzabili. Queste impostazioni sono elencate nella sezione successiva.

**Partizioni**: questo valore viene impostato quando si crea l'hub IoT e può essere modificato tramite questa impostazione.

**Nome ed endpoint compatibili con Hub eventi**: quando viene creato quando l'hub IoT, viene creato internamente un Hub eventi a cui si potrebbe avere l'esigenza di accedere in determinate condizioni. Il nome e l'endpoint dell'Hub eventi non possono essere personalizzati, ma sono disponibili per l'uso tramite il pulsante **Copia**.

**Tempo di conservazione**: per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite l'elenco a discesa. Questo valore è espresso in giorni per Da dispositivo a cloud e non in ore, come lo è l'impostazione simile per Da cloud a dispositivo.

**Gruppi di consumer**: è un'impostazione simile ad altri sistemi di messaggistica che possono essere usati per eseguire il pull dei dati in modo specifico per connettere altre applicazioni o servizi all'hub IoT. Ogni hub IoT viene creato con un gruppo di consumer predefinito. Tuttavia, è possibile aggiungere o eliminare gruppi di consumer negli hub IoT.

> [AZURE.NOTE] Il gruppo di consumer predefinito non può essere modificato o eliminato.

![][11]

## Caricamento di file

Per usare la funzionalità di caricamento di file nell'hub IoT, è prima di tutto necessario associare un account di archiviazione di Azure all'hub. Selezionare le impostazioni di **Caricamento del file** per visualizzare un elenco di proprietà di caricamento del file per l'hub IoT da modificare.

**Contenitore di archiviazione**: usare il portale per selezionare il contenitore BLOB in un account di archiviazione nella sottoscrizione corrente da associare all'hub IoT. Se necessario, è possibile creare un nuovo account di archiviazione nel pannello **Account di archiviazione** e un nuovo contenitore BLOB nel pannello **Contenitori**. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

![][14]

**Receive notifications for uploaded files** (Ricezione di notifiche per i file caricati): abilitare o disabilitare le notifiche di caricamento del file mediante l'apposita opzione.

**SAS TTL** (TTL di firma di accesso condiviso): questa impostazione indica la durata degli URI di firma di accesso condiviso restituiti dal dispositivo tramite l’hub IoT. Per impostazione predefinita è impostato su un'ora, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

**File notification settings default TTL** (TTL predefinito per le impostazioni di notifica dei file): durata di una notifica di caricamento del file. Per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

**File notification maximum delivery count** (Numero massimo di recapiti per le notifiche dei file): numero di tentativi che verranno eseguiti dall'hub IoT per distribuire una notifica di caricamento del file. Per impostazione predefinita è impostato su 10, ma può essere personalizzato con altri valori tramite il dispositivo di scorrimento.

![][13]

## Prezzi e scalabilità

I prezzi di un hub IoT esistente possono essere modificati tramite le impostazioni disponibili in **Prezzi**, con le eccezioni seguenti:

- Nell'implementazione corrente un hub IoT con uno SKU gratuito non può cambiare i piani con quelli di uno degli SKU a pagamento o viceversa.
- Nella sottoscrizione può essere presente un solo piano gratuito per l'hub IoT.

![][12]

Il passaggio da un piano più elevato (S2 o S3) a un piano inferiore (S1 o S2) è consentito solo quando il numero di messaggi inviati per un dato giorno non sono in conflitto. Ad esempio, se il numero di messaggi al giorno supera 400.000, il livello per l'hub IoT può essere modificato, ma se si modifica il livello passando a S1, l'hub sarà limitato per quel giorno.

## Eliminare l'hub IoT

È possibile passare all'hub IoT che si vuole eliminare facendo clic su **Sfoglia** e quindi scegliendo l'hub appropriato da eliminare. Fare clic sul pulsante **Elimina** sotto il nome dell'hub per eliminarlo.

## Passaggi successivi

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

- [Gestire in blocco i dispositivi IoT][lnk-bulk]
- [Metriche di utilizzo][lnk-metrics]
- [Monitoraggio delle operazioni][lnk-monitor]
- [Gestire l'accesso all'hub IoT][lnk-itpro]

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png
  [13]: ./media/iot-hub-manage-through-portal/file-upload-settings.png
  [14]: ./media/iot-hub-manage-through-portal/file-upload-container-selection.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0817_2016-->