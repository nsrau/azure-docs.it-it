---
title: "Configurare il codificatore Telestream Wirecast per inviare un flusso live a velocità in bit singola | Microsoft Docs"
description: "In questo argomento viene illustrato come configurare il codificatore live Wirecast per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. "
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 93be5aa26cc66d7cfc89b88f9273cb40acaa1b17
ms.contentlocale: it-it
ms.lasthandoff: 01/11/2017

---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Usare il codificatore Wirecast per inviare un flusso live a velocità in bit singola.
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

In questo argomento viene illustrato come configurare il codificatore [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live.  Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il Portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Prerequisiti
* [Creare un account Servizi multimediali di Azure](media-services-portal-create-account.md)
* Verificare che sia presente un endpoint di streaming in esecuzione. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md)
* Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Avviare lo strumento e connettersi al proprio account AMS.

## <a name="tips"></a>Suggerimenti
* Se possibile, usare una connessione a Internet con cablaggio fisico.
* È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
* Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## <a name="create-a-channel"></a>Creare un canale
1. Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel** dal menu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni del canale selezionare **Standard** per l'opzione di codifica live con il protocollo di input impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite.

    Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

3. Fare clic su **Create Channel**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Per l'avvio del canale possono essere richiesti fino a 20 minuti.
>
>

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>Configurare il codificatore Telestream Wirecast
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
1. Aprire l'applicazione Telestream Wirecast sul computer utilizzato e impostare per il flusso RTP.
2. Configurare l'output accedendo alla scheda **Output** e selezionando **Output Settings…** (Impostazioni di output...).

    Assicurarsi che **Destinazione di uscita** sia impostato su **RTMP Server** (Server RTMP).
3. Fare clic su **OK**.
4. Nella pagina delle impostazioni impostare il campo **Destinazione** su **Servizi multimediali di Azure**.

    Il profilo di codifica è già pre-selezionato per **Azure H.264 720p 16:9 (1280x720)**. Per personalizzare queste impostazioni, selezionare l'icona raffigurante un ingranaggio a destra dell'elenco a discesa e poi scegliere **Nuovo predefinito**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configura le impostazioni predefinite del codificatore.

    Denominare il set di impostazioni e verificare le seguenti impostazioni consigliate:

    **Video**

   * Codificatore: MainConcept h. 264
   * Fotogrammi al secondo: 30
   * Velocità in bit media: 5000 kbit al secondo (può essere regolato in base alle limitazioni di rete)
   * Profilo: principale
   * Fotogramma chiave ogni: 60 fotogrammi

    **Audio**

   * Velocità in bit di destinazione: 192 kbit/sec
   * Frequenza di campionamento: 44,100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Premere **Salva**.

    Il campo di codifica è ora il profilo appena creato disponibile per la selezione.

    Verificare che venga selezionato il nuovo profilo.
7. Ottenere l’input URL del canale per assegnargli il Wirecast **RTMP Endpoint**.\

    Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è passato da **Avvio in corso** a **In esecuzione**, è possibile ottenere l'URL di input.

    Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il puntatore sull'opzione **Copy Input URL to clipboard** (Copia URL di input negli Appunti) e quindi selezionare **Primary Input URL** (URL di input primario).  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Nella finestra **Output Settings** (Impostazioni di output) di Wirecast incollare queste informazioni nel campo **Indirizzo** della sezione relativa all'output e assegnare un nome di flusso.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selezionare **OK**.
2. Nella schermata principale **Wirecast** confermare che le origini di input per video e audio sono pronte e quindi premere **Flusso** nell'angolo superiore sinistro.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Prima di fare clic su **Flusso**, **è necessario** assicurarsi che il canale sia pronto.
> Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un feed per l’input/contributo per più di 15 minuti.
>
>

## <a name="test-playback"></a>Testare la riproduzione

Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu passare il mouse su **Playback the Preview** (Riproduci anteprima) e scegliere **with Azure Media Player** (Con Azure Media Player).  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.  

## <a name="create-a-program"></a>Creare un programma.
1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE fare clic con il pulsante destro del mouse all'interno dell'area di programma e selezionare **Create New Program** (Crea nuovo programma).  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.  
3. Selezionare la casella di controllo **Start the Program now** .
4. Fare clic su **Create Program**.  

   >[!NOTE]
   >La creazione di un programma richiede meno tempo rispetto alla creazione del canale.
       
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)** (Riproduci programma/i), quindi scegliere **with Azure Media Player** (Con Azure Media Player).  
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (Copia URL di output negli Appunti) oppure recuperare queste informazioni dall'opzione **Program information and settings** (Impostazioni e informazioni programma) nel menu.

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.  

## <a name="troubleshooting"></a>risoluzione dei problemi
Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

