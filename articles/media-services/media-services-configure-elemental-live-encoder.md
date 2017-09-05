---
title: "Configurare il codificatore Elemental Live per inviare un flusso live a velocità in bit singola | Microsoft Docs"
description: "In questo argomento viene illustrato come configurare il codificatore Elemental Live per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: bb4966cbd2af7a14caeac108a001b0820a67d2a1
ms.contentlocale: it-it
ms.lasthandoff: 01/11/2017

---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Usare il codificatore Elemental Live per inviare un flusso live a velocità in bit singola.
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

In questo argomento viene illustrato come configurare il codificatore [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live.  Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il Portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Prerequisiti
* Sono necessarie conoscenze pratiche dell'uso dell'interfaccia Web Elemental Live per la creazione di eventi live.
* [Creare un account Servizi multimediali di Azure](media-services-portal-create-account.md)
* Verificare che sia presente un endpoint di streaming in esecuzione. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).
* Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Avviare lo strumento e connettersi al proprio account AMS.

## <a name="tips"></a>Suggerimenti
* Se possibile, usare una connessione a Internet con cablaggio fisico.
* È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
* Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## <a name="elemental-live-with-rtp-ingest"></a>Elemental Live con inserimento RTP
In questa sezione viene illustrato come configurare il codificatore Elemental Live che invia un flusso live a velocità in bit singola su RTP.  Per altre informazioni, vedere [Flusso MPEG-TS su RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Creare un canale

1. Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel** dal menu.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni del canale selezionare **Standard** per l'opzione Codifica live con il protocollo di input impostato su **RTP (MPEG-TS)**. È possibile confermare tutte le altre impostazioni predefinite.

    Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

3. Fare clic su **Create Channel**.

   ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> Per l'avvio del canale possono essere richiesti fino a 20 minuti.
>
>

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Configurare il codificatore Elemental Live
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

#### <a name="configuration-steps"></a>Procedura di configurazione
1. Accedere all'interfaccia Web di **Elemental Live** e configurare il codificatore per lo streaming **UDP/TS**.
2. Dopo aver creato un nuovo evento, scorrere verso il basso fino ai gruppi di output e aggiungere il gruppo di output **UDP/TS** .
3. Creare un nuovo output selezionando **Nuovo flusso** e quindi facendo clic su **Aggiungi output**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > È consigliabile che per l'evento Elemental il codice temporale sia impostato sull'orologio di sistema per facilitare la riconnessione del codificatore in caso di un errore di flusso.
   >
   >
4. Dopo aver creato l'output, fare clic su **Aggiungi flusso**. È ora possibile configurare le impostazioni di output.
5. Scorrere verso il basso fino al flusso "Flusso 1" appena creato, fare clic sulla scheda **Video** a sinistra ed espandere la sezione delle impostazioni **Avanzate**.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Anche se per Elemental Live è disponibile un'ampia gamma di personalizzazioni, per iniziare con lo streaming verso AMS sono consigliate le impostazioni seguenti.

   * Risoluzione: 1280 x 720
   * Frequenza fotogrammi: 30
   * Dimensioni GOP: 60 fotogrammi
   * Modalità interlacciamento: progressivo
   * Velocità in bit: 5000000 bit/s (regolabile in base alle limitazioni di rete)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Ottenere l'URL di input del canale.

    Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è passato da **Avvio in corso** a **In esecuzione**, è possibile ottenere l'URL di input.

    Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il puntatore sull'opzione **Copy Input URL to clipboard** (Copia URL di input negli Appunti) e quindi selezionare **Primary Input URL** (URL di input primario).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Incollare queste informazioni nel campo **Destinazione principale** di Elemental. Per tutte le altre opzioni è possibile mantenere l'impostazione predefinita.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Per garantire una maggiore ridondanza, ripetere questi passaggi con l'URL di input secondario mediante la creazione di una scheda separata "Output" per lo streaming UDP/TS.
3. Fare clic su **Crea** (se è stato creato un nuovo evento) o **Aggiorna** (in caso di modifica di un evento preesistente) e quindi procedere con l'avvio del codificatore.

> [!IMPORTANT]
> Prima di scegliere **Avvia** nell'interfaccia Web di Elemental Live, **è necessario** verificare che il canale sia pronto.
> Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un evento per più di 15 minuti.
>
>

Dopo 30 secondi di esecuzione del flusso, tornare allo strumento AMSE e testare la riproduzione.  

### <a name="test-playback"></a>Testare la riproduzione

Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu passare il mouse su **Playback the Preview** (Riproduci anteprima) e scegliere **with Azure Media Player** (Con Azure Media Player).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.   

### <a name="create-a-program"></a>Creare un programma.
1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE fare clic con il pulsante destro del mouse all'interno dell'area di programma e selezionare **Create New Program** (Crea nuovo programma).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.  
3. Selezionare la casella di controllo **Start the Program now** .
4. Fare clic su **Create Program**.  

    >[!NOTE]
    > La creazione di un programma richiede meno tempo rispetto alla creazione del canale.   
      
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)** (Riproduci programma/i), quindi scegliere **with Azure Media Player** (Con Azure Media Player).  
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (Copia URL di output negli Appunti) oppure recuperare queste informazioni dall'opzione **Program information and settings** (Impostazioni e informazioni programma) nel menu.

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.  

## <a name="troubleshooting"></a>risoluzione dei problemi
Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

