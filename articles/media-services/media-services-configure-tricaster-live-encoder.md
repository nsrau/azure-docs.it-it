---
title: "Configurare il codificatore NewTek TriCaster per inviare un flusso live a velocità in bit singola | Microsoft Docs"
description: "In questo argomento viene illustrato come configurare il codificatore Tricaster per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live."
services: media-services
documentationcenter: 
author: cenkdin
manager: erikre
editor: 
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/12/2016
ms.author: juliako;cenkd;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 602f86f17baffe706f27963e8d9963f082971f54
ms.openlocfilehash: de649ebc0f832f1c549c2e0f39eb00efbf1fcdfc


---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizzare il codificatore NewTek TriCaster per inviare un flusso live a velocità in bit singola.
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

In questo argomento viene illustrato come configurare il codificatore live [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il Portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Quando si usa Tricaster per l'invio di un feed di contributo ai canali AMS abilitati per la codifica live, in particolare quando si usano alcune funzionalità di Tricaster, ad esempio tagli rapidi tra i feed o passaggi all'interno e all'esterno di slate, sono possibili anomalie audio/video nell'evento live. Il team di AMS sta lavorando per risolvere questi problemi. Fino a quel momento non è consigliabile usare queste funzionalità.
>
>

## <a name="prerequisites"></a>Prerequisiti
* [Creare un account Servizi multimediali di Azure](media-services-portal-create-account.md)
* Assicurarsi che sia in esecuzione un endpoint di streaming con almeno un'unità di streaming allocata. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md)
* Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Avviare lo strumento e connettersi al proprio account AMS.

## <a name="tips"></a>Suggerimenti
* Se possibile, usare una connessione a Internet con cablaggio fisico.
* È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
* Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## <a name="create-a-channel"></a>Creare un canale
1. Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel**  dal menu.

![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

1. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni del canale selezionare **Standard** per l'opzione di codifica live con il protocollo di input impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite.

Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

1. Fare clic su **Create Channel**.
   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Per l'avvio del canale possono essere richiesti fino a 20 minuti.
>
>

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpaconfigure-the-newtek-tricaster-encoder"></a><a id=configure_tricaster_rtmp></a>Configurare il codificatore NewTek TriCaster
In questa esercitazione vengono usate le seguenti impostazioni di output. Nel resto di questa sezione vengono descritti in maggiore dettaglio i passaggi di configurazione.

**Video**:

* Codec: H.264
* Profilo: alto (livello 4.0)
* Velocità in bit: 5000 kbps
* Fotogramma chiave: 2 secondi (60 secondi)
* Frequenza dei fotogrammi: 30

**Audio**:

* Codec: AAC (LC)
* Velocità in bit: 192 kbps
* Frequenza di campionamento: 44,1 kHz

### <a name="configuration-steps"></a>Procedura di configurazione
1. Creare un nuovo progetto **NewTek TriCaster** in base a quale origine di input del video si sta utilizzando.
2. Una volta all'interno di tale progetto, cercare il pulsante **Flusso** e scegliere l'icona raffigurante un ingranaggio per accedere al menu di configurazione del flusso.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Una volta aperto il menu, fare clic su **New** sotto l'intestazione di connessione. Quando richiesto per il tipo di connessione, selezionare **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Fare clic su **OK**.
5. Un profilo FMLE può ora essere importato facendo clic sulla freccia a discesa sotto **Streaming Profile** (Profilo streaming) e passando a **Sfoglia**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Passare a dove è stato salvato il profilo FMLE configurato.
7. Selezionarlo e premere **OK**.

    Una volta caricato il profilo, andare al passaggio successivo.
8. Ottenere l’input URL del canale per assegnargli il Tricaster **RTMP Endpoint**.

    Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è passato da **Avvio in corso** a **In esecuzione**, è possibile ottenere l'URL di input.

    Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il puntatore sull'opzione **Copy Input URL to clipboard** (Copia URL di input negli Appunti) e quindi selezionare **Primary Input URL** (URL di input primario).  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Incollare le informazioni nel campo **Location** (Posizione) sotto **Flash Server** (Server flash) all'interno del progetto Tricaster. Assegnare un nome di flusso nel campo **ID flusso** .

    Se le informazioni relative al flusso sono state aggiunte al profilo FMLE, è possibile anche importarle in questa sezione facendo clic su **Importa impostazioni**, andando al profilo FMLE salvato e facendo clic su **OK**. I campi rilevanti Flash Server devono popolarsi con le informazioni da FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Al termine, fare clic su **OK** nella parte inferiore della schermata. Quando gli input audio e video nel Tricaster sono pronti, iniziare lo streaming di AMS facendo clic sul pulsante **Flusso** .

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Prima di fare clic su **Flusso**, **è necessario** assicurarsi che il canale sia pronto.
> Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un feed per l’input/contributo per più di 15 minuti.
>
>

## <a name="test-playback"></a>Testare la riproduzione
1. Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu passare il mouse su **Playback the Preview** (Riproduci anteprima) e scegliere **with Azure Media Player** (Con Azure Media Player).  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.  

## <a name="create-a-program"></a>Creare un programma.
1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE fare clic con il pulsante destro del mouse all'interno dell'area di programma e selezionare **Create New Program** (Crea nuovo programma).  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.  
3. Selezionare la casella di controllo **Start the Program now** .
4. Fare clic su **Create Program**.  

    Nota: la creazione di un programma richiede meno tempo rispetto alla creazione del canale.    
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)** (Riproduci programma/i), quindi scegliere **with Azure Media Player** (Con Azure Media Player).  
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (Copia URL di output negli Appunti) oppure recuperare queste informazioni dall'opzione **Program information and settings** (Impostazioni e informazioni programma) nel menu.

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.  

## <a name="troubleshooting"></a>risoluzione dei problemi
Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Nov16_HO3-->


