<properties 
	pageTitle="Configurare il codificatore Elemental Live per inviare un flusso live a velocità in bit singola." 
	description="In questo argomento viene illustrato come configurare il codificatore Elemental Live per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Usare il codificatore Elemental Live per inviare un flusso live a velocità in bit singola.

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In questo argomento viene illustrato come configurare il codificatore [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il portale di Azure classico per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program).

##Prerequisiti

- Sono necessarie conoscenze pratiche dell'uso dell'interfaccia Web Elemental Live per la creazione di eventi live.
- [Creare un account di Servizi multimediali di Azure](media-services-create-account.md)
- Assicurarsi che sia in esecuzione un endpoint di streaming con almeno un'unità di streaming allocata. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md)

- Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Avviare lo strumento e connettersi al proprio account AMS.

##Suggerimenti

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## Elemental Live con inserimento RTP

In questa sezione viene illustrato come configurare il codificatore Elemental Live che invia un flusso live a velocità in bit singola su RTP. Per altre informazioni, vedere [Flusso MPEG-TS su RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### Creare un canale

1.  Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Crea canale** dal menu.

![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni canale selezionare **Standard** per l'opzione Codifica live con Protocollo di input impostato su **RTP (MPEG-TS)**. È possibile confermare tutte le altre impostazioni predefinite.


Assicurarsi che l'opzione **Start the new channel now** sia selezionata.

3. Fare clic su **Create Channel**. ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE]Per l'avvio del canale possono essere richiesti fino a 20 minuti.

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT]Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurare il codificatore Elemental Live 

In questa esercitazione vengono usate le seguenti impostazioni di output. Nel resto di questa sezione vengono descritti in maggiore dettaglio i passaggi di configurazione.

**Video**:
 
- Codec: H.264 
- Profilo: alto (livello 4.0) 
- Velocità in bit: 5000 kbps 
- Fotogramma chiave: 2 secondi (60 secondi) 
- Frequenza dei fotogrammi: 30
 
**Audio**:

- Codec: AAC (LC) 
- Velocità in bit: 192 kbps 
- Frequenza di campionamento: 44,1 kHz


####Procedura di configurazione

1. Passare all'interfaccia Web di **Elemental Live** e configurare il codificatore per lo streaming **UDP/TS**. 

2. Dopo aver creato un nuovo evento, scorrere verso il basso fino ai gruppi di output e aggiungere il gruppo di output **UDP/TS**.

3. Creare un nuovo output selezionando **Nuovo flusso** e quindi facendo clic su **Aggiungi output**.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
	
	>[AZURE.NOTE]È consigliabile che per l'evento Elemental il codice temporale sia impostato sull'orologio di sistema per facilitare la riconnessione del codificatore in caso di un errore di flusso.

4. Dopo aver creato l'output, fare clic su **Aggiungi flusso**. È ora possibile configurare le impostazioni di output.
5. Scorrere verso il basso fino al flusso "Flusso 1" appena creato, fare clic sulla scheda **Video** a sinistra ed espandere la sezione delle impostazioni **Avanzate**. 

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

	Anche se per Elemental Live è disponibile un'ampia gamma di personalizzazioni, per iniziare con lo streaming verso AMS sono consigliate le impostazioni seguenti.
	
	- Risoluzione: 1280 x 720 
	- Frequenza fotogrammi: 30 
	- Dimensioni GOP: 60 fotogrammi 
	- Modalità interlacciamento: progressivo 
	- Velocità in bit: 5000000 bit/s (regolabile in base alle limitazioni di rete) 
	

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Ottenere l'URL di input del canale.
	
	Tornare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è cambiato da **iniziale** a **in esecuzione**, è possibile ottenere l'URL di input.
	  
	Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il mouse sull'opzione **Copy Input URL to clipboard** e quindi selezionare **Primary Input URL**.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
	
1. Incollare queste informazioni nel campo **Destinazione principale** di Elemental. Per tutte le altre opzioni è possibile mantenere l'impostazione predefinita.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

	Per garantire una maggiore ridondanza, ripetere questi passaggi con l'URL di input secondario mediante la creazione di una scheda separata "Output" per lo streaming UDP/TS.
	
7. Fare clic su **Crea** (se è stato creato un nuovo evento) o **Aggiorna** (in caso di modifica di un evento preesistente) e quindi procedere con l'avvio del codificatore.

>[AZURE.IMPORTANT]Prima di scegliere **Avvia** nell'interfaccia Web di Elemental Live, è **necessario** verificare che il canale sia pronto. Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un evento per più di 15 minuti.

Dopo 30 secondi di esecuzione del flusso, tornare allo strumento AMSE e testare la riproduzione.

###Testare la riproduzione
  
1. Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu, passare il mouse su **Riproduci anteprima** e scegliere **con Azure Media Player**.  

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

###Creare un programma.

1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE, fare clic con il pulsante destro all'interno dell'area di programma e selezionare **Creare un nuovo programma**.  

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.
3. Selezionare la casella di controllo **Start the Program now**.
4. Fare clic su **Create Program**.  
  
	Nota: la creazione di un programma richiede meno tempo rispetto alla creazione del canale.
 
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)**, quindi scegliere **with Azure Media Player**.
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (o recuperare queste informazioni dall'opzione **Program information and settings** nel menu). 

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.

## Risoluzione dei problemi

Vedere l’argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1203_2015-->