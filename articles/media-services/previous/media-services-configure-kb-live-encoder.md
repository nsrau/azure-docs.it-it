---
title: "Configurare il codificatore Haivision KB per l'invio di un flusso live a velocità in bit singola in Azure | Microsoft Docs"
description: "Questo argomento illustra come configurare il codificatore live Haivision KB per l'invio di un flusso a velocità in bit singola ai canali AMS abilitati per la codifica live."
services: media-services
documentationcenter: 
author: dbgeorge
manager: vsood
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Usare il codificatore live Haivision KB per inviare un flusso live a velocità in bit singola
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Questo argomento illustra come configurare il [codificatore live Haivision KB](https://www.haivision.com/products/kb-series/) per l'invio di un flusso a velocità in bit singola ai canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il Portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Prerequisiti
*   Accesso a un codificatore Haivision KB che esegue SW v5.01 o versione successiva.
* [Creare un account Servizi multimediali di Azure](media-services-portal-create-account.md)
* Verificare che sia presente un endpoint di streaming in esecuzione. Per altre informazioni, vedere [Gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md)
* Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
* Avviare lo strumento e connettersi al proprio account AMS.

## <a name="tips"></a>Suggerimenti
* Se possibile, usare una connessione a Internet con cablaggio fisico.
* È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
* Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.

## <a name="create-a-channel"></a>Creare un canale
1. Nello strumento AMSE passare alla scheda **Live** e fare clic con il pulsante destro del mouse all'interno dell'area del canale. Scegliere **Create channel** dal menu.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni del canale selezionare **Standard** per l'opzione di codifica live con il protocollo di input impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite. Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.
3. Fare clic su **Create Channel**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Per l'avvio del canale possono essere richiesti fino a 20 minuti.

## <a name="configure-the-haivision-kb-encoder"></a>Configurare il codificatore Haivision KB
In questa esercitazione vengono usate le seguenti impostazioni di output. Nel resto di questa sezione vengono descritti in maggiore dettaglio i passaggi di configurazione.

Video:
-   Codec: H.264
-   Profilo: alto (livello 4.0)
-   Velocità in bit: 5000 kbps
-   Fotogramma chiave: 2 secondi (60 secondi)
-   Frequenza dei fotogrammi: 30

Audio:
-   Codec: AAC (LC)
-   Velocità in bit: 192 kbps
-   Frequenza di campionamento: 44,1 kHz

## <a name="configuration-steps"></a>Procedura di configurazione
1.  Accedere all'interfaccia utente Haivision KB.
2.  Fare clic sul **pulsante di menu** nel centro di controllo canali e selezionare **Add Channel** (Aggiungi canale).  
    ![Screen Shot 2017-08-14 at 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Digitare il nome del canale nel campo **Name** (Nome) e fare clic su Next (Avanti).  
    ![Screen Shot 2017-08-14 at 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selezionare l'origine di **Channel Input** (Input canale) nell'elenco a discesa **Input Source** (Origine di input) e fare clic su Next (Avanti).
    ![Screen Shot 2017-08-14 at 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Nell'elenco a discesa **Encoder Template** (Modello codificatore) scegliere **H264-720-AAC-192** e fare clic su Next (Avanti).
    ![Screen Shot 2017-08-14 at 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Nell'elenco a discesa **Select New Output** (Seleziona nuovo output) scegliere **RTMP** e fare clic su Next (Avanti).  
    ![Screen Shot 2017-08-14 at 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Nella finestra **Channel Output** (Output canale) inserire le informazioni sul flusso di Azure. Copiare il collegamento **RTMP** dalla configurazione iniziale del canale e incollarlo nell'area **Server**. Nell'area **Output Name** (Nome output) digitare il nome del canale. Nell'area Stream Name Template (Modello nome flusso) usare il modello RTMPStreamName_%video_bitrate% per assegnare un nome al flusso.
    ![Screen Shot 2017-08-14 at 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Fare clic su Next (Avanti) e quindi su Done (Fine).
9.  Fare clic sul **pulsante Play** per avviare il canale del codificatore.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testare la riproduzione
Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu passare il mouse su Playback the Preview (Anteprima riproduzione) e selezionare with Azure Media Player (con Azure Media Player).

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Come materiale sussidiario, vedere l'articolo sulla risoluzione dei problemi.

## <a name="create-a-program"></a>Creare un programma
1.  Una volta che viene confermata la riproduzione del canale, creare un programma. Nella scheda Live dello strumento AMSE fare clic con il pulsante destro del mouse all'interno dell'area del programma e selezionare Create New Program (Crea programma).
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Assegnare un nome al programma e, se necessario, modificare la durata dell'intervallo di archiviazione, la cui impostazione predefinita è di quattro ore. È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.
2.  Selezionare la casella Start the Program now (Avvia il programma ora).
3.  Fare clic su Create Program (Crea programma).
4.  Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma, passare a Playback the program(s) (Riproduci programmi) e quindi selezionare with Azure Media Player (con Azure Media Player).
5.  Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e selezionare Copy the Output URL to Clipboard (Copia URL di output negli Appunti) oppure recuperare queste informazioni dall'opzione Program information and settings (Impostazioni e informazioni programma) nel menu.

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.

> [!NOTE]
> La creazione di un programma richiede meno tempo rispetto alla creazione del canale.