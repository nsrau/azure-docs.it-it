---
title: "Configurare il codificatore FMLE per l&quot;invio di un flusso singolo in diretta di velocità in bit | Microsoft Docs"
description: "In questo argomento viene illustrato come configurare il codificatore Flash Media Live Encoder (FMLE) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/12/2016
ms.author: juliako;cenkdin;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 602f86f17baffe706f27963e8d9963f082971f54
ms.openlocfilehash: 80b391e43417ce09bd8aaaa04e48fcd85baf9323


---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Usare il codificatore FMLE per inviare un flusso live a velocità in bit singola.
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

In questo argomento viene illustrato come configurare il codificatore [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) per inviare un flusso a velocità in bit singola a canali AMS abilitati per la codifica live. Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire Servizi multimediali di Azure (AMS) con lo strumento Azure Media Services Explorer (AMSE). Questo strumento può essere eseguito solo in PC Windows. Gli utenti di sistemi Mac o Linux possono usare il Portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md).

Si noti che questa esercitazione descrive l'utilizzo di AAC. Tuttavia, per impostazione predefinita FMLE non supporta AAC. È necessario acquistare un plug-in per codificare AAC come da MainConcept: [plug-in AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

1. Specificare un nome di canale. Il campo della descrizione è facoltativo. In Impostazioni del canale selezionare **Standard** per l'opzione di codifica live con il protocollo di input impostato su **RTMP**. È possibile confermare tutte le altre impostazioni predefinite.

Assicurarsi che l'opzione **Avvia ora il nuovo canale** sia selezionata.

1. Fare clic su **Create Channel**.
   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Per l'avvio del canale possono essere richiesti fino a 20 minuti.
>
>

Durante l'avvio di canale è possibile [configurare il codificatore](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Si noti che la fatturazione inizia non appena il canale passa a uno stato di pronto. Per altre informazioni, vedere [Stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurefmlertmpaconfigure-the-fmle-encoder"></a><a id=configure_fmle_rtmp></a>Configurare il codificatore FMLE
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
1. Passare all’interfaccia Flash Media Live Encoder (FMLE) nel computer in uso.

    L'interfaccia è una pagina principale di impostazioni. Annotare le seguenti impostazioni consigliate per iniziare a utilizzare streaming tramite FMLE.

   * Formato: Frequenza dei fotogrammi h. 264: 30,00
   * Dimensione di input: 1280 x 720
   * Velocità in bit: Kbps 5000 (può essere regolato in base alle limitazioni di rete)  

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Quando si utilizzano le origini interlacciate, selezionare l'opzione "Deinterlaccia"
2. Selezionare l'icona di chiave accanto a formato, queste impostazioni aggiuntive devono essere:

   * Profilo: principale
   * Livello: 4.0
   * Frequenza dei fotogrammi chiave: 2 secondi

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Impostare la seguente impostazione importante dell’audio:

   * Formato: AAC
   * Frequenza di campionamento: 44100 kHz
   * Velocità in bit: 192 kbps

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Ottenere l’input URL del canale per assegnargli il FMLE **RTMP Endpoint**.

    Passare allo strumento AMSE e controllare lo stato di completamento del canale. Una volta che lo stato è passato da **Avvio in corso** a **In esecuzione**, è possibile ottenere l'URL di input.

    Quando il canale è in esecuzione, fare clic con il pulsante destro del mouse sul nome del canale, spostarsi verso il basso per passare il puntatore sull'opzione **Copy Input URL to clipboard** (Copia URL di input negli Appunti) e quindi selezionare **Primary Input URL** (URL di input primario).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Incollare le informazioni nel campo **URL FMS** della sezione di output e assegnare un nome di flusso.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Per garantire la ridondanza aggiuntiva, ripetere questi passaggi con l'URL di Input secondari.
6. Selezionare **Connessione**.

> [!IMPORTANT]
> Prima di fare clic su **Connetti**, **è necessario** assicurarsi che il canale sia pronto.
> Assicurarsi inoltre di non lasciare il canale in uno stato pronto senza un feed per l’input/contributo per più di 15 minuti.
>
>

## <a name="test-playback"></a>Testare la riproduzione
1. Passare allo strumento AMSE e fare clic con il pulsante destro del mouse sul canale da testare. Nel menu passare il mouse su **Playback the Preview** (Riproduci anteprima) e scegliere **with Azure Media Player** (Con Azure Media Player).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Se il flusso viene visualizzato nel lettore, ciò indica che il codificatore è stato configurato correttamente per connettersi a AMS.

In caso di errore, sarà necessario reimpostare il canale e regolare le impostazioni del codificatore. Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.  

## <a name="create-a-program"></a>Creare un programma.
1. Una volta che viene confermata la riproduzione del canale, creare un programma. Sotto la scheda **Live** nello strumento AMSE fare clic con il pulsante destro del mouse all'interno dell'area di programma e selezionare **Create New Program** (Crea nuovo programma).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Assegnare un nome al programma e, se necessario, modificare l'opzione **Archive Window Length** (con impostazione predefinita di 4 ore). È inoltre possibile specificare un percorso di archiviazione o confermare l'impostazione predefinita.  
3. Selezionare la casella di controllo **Start the Program now** .
4. Fare clic su **Create Program**.  

    Nota: la creazione di un programma richiede meno tempo rispetto alla creazione del canale.    
5. Quando il programma è in esecuzione, verificare il funzionamento della riproduzione. A tale scopo, fare clic con il pulsante destro del mouse sul programma e passare **Playback the program(s)** (Riproduci programma/i), quindi scegliere **with Azure Media Player** (Con Azure Media Player).  
6. Dopo questa verifica, fare nuovamente clic con il pulsante destro del mouse sul programma e scegliere **Copy the Output URL to Clipboard** (Copia URL di output negli Appunti) oppure recuperare queste informazioni dall'opzione **Program information and settings** (Impostazioni e informazioni programma) nel menu.

Il flusso è ora pronto per essere incorporato in un lettore o distribuito per la visualizzazione pubblica live.  

## <a name="troubleshooting"></a>risoluzione dei problemi
Vedere l’argomento sulla [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per ricevere istruzioni.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Nov16_HO3-->


