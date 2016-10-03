<properties 
	pageTitle="Configurare il codificatore Telestream Wirecast per inviare un flusso live a velocità in bit singola | Microsoft Azure" 
	description="In questo argomento viene illustrato come configurare il codificatore live Wirecast per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. " 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>  

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako;cenkdin;anilmur"/>  

#Usare il codificatore Wirecast per inviare un flusso live a velocità in bit singola.

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In questo argomento viene illustrato come configurare il codificatore [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il portale di Azure classico per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


##Prerequisiti

- [Creare un account di Servizi multimediali di Azure](media-services-create-account.md)
- Assicurarsi che sia in esecuzione un endpoint di streaming con almeno un'unità di streaming allocata. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md)
- Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Avviare lo strumento e connettersi al proprio account AMS.

##Suggerimenti

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.


## Creare un canale

1.  Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel** dal menu.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Channel Settings, selezionare **Standard** per l'opzione Live Encoding con Input Protocol impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite.


Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

3. Fare clic su **Create Channel**. ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] Per l'avvio del canale possono essere richiesti fino a 20 minuti.

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurare il codificatore Telestream Wirecast

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


###Procedura di configurazione

1. Aprire l'applicazione Telestream Wirecast sul computer utilizzato e impostare per il flusso RTP.
2. Configurare l'output passando per la scheda **Output** e selezionando **Impostazioni di Output**.
	
	Assicurarsi che la **Destinazione dell'Output** sia impostata sul **Server RTP**.
3. Fare clic su **OK**.
4. Nella pagina Impostazioni, impostare il campo **Destinazione** su **Servizi multimediali di Azure**.
 
	Il profilo di codifica è già pre-selezionato per **Azure H.264 720p 16:9 (1280x720)**. Per personalizzare queste impostazioni, selezionare l'icona raffigurante un ingranaggio a destra dell'elenco a discesa e poi scegliere **Nuovo predefinito**.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configura le impostazioni predefinite del codificatore.

	Denominare il set di impostazioni e verificare le seguenti impostazioni consigliate:

	**Video**
	
	- Codificatore: MainConcept h. 264
	- Fotogrammi al secondo: 30
	- Velocità in bit media: 5000 kbit al secondo (può essere regolato in base alle limitazioni di rete)
	- Profilo: principale
	- Fotogramma chiave ogni: 60 fotogrammi

	**Audio**

	- Velocità in bit di destinazione: 192 kbit/sec
	- Frequenza di campionamento: 44,100 kHz
	 
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Premere **Salva**.

	Il campo di codifica è ora il profilo appena creato disponibile per la selezione.

	Verificare che venga selezionato il nuovo profilo.

7. Ottenere l’input URL del canale per assegnargli il Wirecast **RTMP Endpoint**.\\
	
	Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è cambiato da **iniziale** a **in esecuzione**, è possibile ottenere l'URL di input.
	  
	Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il mouse sull'opzione **Copy Input URL to clipboard** e quindi selezionare **Primary Input URL**.
	
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Nella finestra di Wirecast **Impostazioni di Output** incollare queste informazioni nel campo **indirizzo** della sezione di output e assegnare un nome di flusso.


	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Selezionare **OK**.

10. Nella schermata principale **Wirecast**, confermare che le origini di input per video e audio sono pronte e quindi premere **Flusso** nell'angolo superiore sinistro.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT] Prima di fare clic su **flusso**, ci si **deve** assicurare che il canale sia pronto. Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un feed per l’input/contributo per più di 15 minuti.

##Testare la riproduzione
  
1. Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu, passare il mouse su **Riproduci anteprima** e scegliere **con Azure Media Player**.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

##Creare un programma.

1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE, fare clic con il pulsante destro all'interno dell'area di programma e selezionare **Creare un nuovo programma**.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.
3. Selezionare la casella di controllo **Start the Program now**.
4. Fare clic su **Create Program**.
  
	Nota: la creazione di un programma richiede meno tempo rispetto alla creazione del canale.
 
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)**, quindi scegliere **with Azure Media Player**.
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (o recuperare queste informazioni dall'opzione **Program information and settings** nel menu).

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.


## Risoluzione dei problemi
 
Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

##Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->