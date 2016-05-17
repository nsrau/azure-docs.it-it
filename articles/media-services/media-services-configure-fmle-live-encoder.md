<properties 
	pageTitle="Configurare il codificatore FMLE per l'invio di un flusso singolo in diretta di velocità in bit" 
	description="In questo argomento viene illustrato come configurare il codificatore Flash Media Live Encoder (FMLE) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live." 
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
	ms.date="05/03/2016" 
	ms.author="juliako;cenkdin;anilmur"/>

#Usare il codificatore FMLE per inviare un flusso live a velocità in bit singola.

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

In questo argomento viene illustrato come configurare il codificatore [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il portale di Azure classico per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

Si noti che questa esercitazione descrive l'utilizzo di AAC. Tuttavia, per impostazione predefinita FMLE non supporta AAC. È necessario acquistare un plug-in per codificare AAC come da MainConcept: [plug-in AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

##Prerequisiti

- [Creare un account di Servizi multimediali di Azure](media-services-create-account.md)
- Assicurarsi che sia in esecuzione un endpoint di streaming con almeno un'unità di streaming allocata. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md)
- Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Avviare lo strumento e connettersi al proprio account AMS.

##Suggerimenti

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## Creare un canale

1.  Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel** dal menu.

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Channel Settings, selezionare **Standard** per l'opzione Live Encoding con Input Protocol impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite.


Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

3. Fare clic su **Create Channel**. ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] Per l'avvio del canale possono essere richiesti fino a 20 minuti.


Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

>[AZURE.IMPORTANT] Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_fmle_rtmp></a>Configurare il codificatore FMLE

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

1. Passare all’interfaccia Flash Media Live Encoder (FMLE) nel computer in uso.

	L'interfaccia è una pagina principale di impostazioni. Annotare le seguenti impostazioni consigliate per iniziare a utilizzare streaming tramite FMLE.
	
	- Formato: Frequenza dei fotogrammi h. 264: 30,00 
	- Dimensione di input: 1280 x 720 
	- Velocità in bit: Kbps 5000 (può essere regolato in base alle limitazioni di rete)  

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

	Quando si utilizzano le origini interlacciate, selezionare l'opzione "Deinterlaccia"

2. Selezionare l'icona di chiave accanto a formato, queste impostazioni aggiuntive devono essere:

	- Profilo: principale
	- Livello: 4.0
	- Frequenza dei fotogrammi chiave: 2 secondi 
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. Impostare la seguente impostazione importante dell’audio:
	
	- Formato: AAC 
	- Frequenza di campionamento: 44100 kHz
	- Velocità in bit: 192 kbps
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. Ottenere l’input URL del canale per assegnargli il FMLE **RTMP Endpoint**.
	
	Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è cambiato da **iniziale** a **in esecuzione**, è possibile ottenere l'URL di input.
	  
	Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il mouse sull'opzione **Copy Input URL to clipboard** e quindi selezionare **Primary Input URL**.
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. Incollare le informazioni nel campo **URL FMS** della sezione di output e assegnare un nome di flusso.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

	Per garantire la ridondanza aggiuntiva, ripetere questi passaggi con l'URL di Input secondari.
8. Selezionare **Connessione**.

>[AZURE.IMPORTANT] Prima di fare clic su **Connessione**, ci si **deve** assicurare che il Canale sia pronto. Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un feed per l’input/contributo per più di 15 minuti.

##Testare la riproduzione
  
1. Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu, passare il mouse su **Riproduci anteprima** e scegliere **con Azure Media Player**.  

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

##Creare un programma.

1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE, fare clic con il pulsante destro all'interno dell'area di programma e selezionare **Creare un nuovo programma**.  

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

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

<!---HONumber=AcomDC_0504_2016-->