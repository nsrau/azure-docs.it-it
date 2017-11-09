---
title: Analizzare i contenuti multimediali usando il portale di Azure | Microsoft Docs
description: Questo argomento illustra come elaborare i contenuti multimediali con processori di contenuti multimediali (MP) di Analisi Servizi multimediali tramite il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 22032aef0cc8b7b015503043028873e70c21ee85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analizzare i contenuti multimediali usando il portale di Azure
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Panoramica
Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale, con conformità, sicurezza e copertura globale di livello enterprise, che semplificano alle aziende e alle organizzazioni l'acquisizione di informazioni dettagliate dai file video. Per una panoramica più dettagliata di Analisi Servizi multimediali, vedere [questo](media-services-analytics-overview.md) argomento. 

Questo argomento illustra come elaborare i contenuti multimediali con processori di contenuti multimediali (MP) di Analisi Servizi multimediali tramite il portale di Azure. I processori di contenuti multimediali di Analisi Servizi multimediali producono file MP4 o JSON. Se un processore di contenuti multimediali produce un file MP4, è possibile scaricare progressivamente il file. Se un processore di contenuti multimediali produce un file JSON, è possibile scaricare il file dall'archiviazione BLOB di Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Scegliere un asset da analizzare
1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Nella finestra **Impostazioni** selezionare **Asset**.  
   .
    ![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selezionare l'asset da analizzare e premere il pulsante **Analizza**.
   
    ![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Nella finestra **Elabora l'asset di file multimediale con Analisi Servizi multimediali** selezionare il processore. 
   
    Il resto dell'articolo spiega i motivi e le modalità per usare ogni processore. 
5. Premere **Crea** per iniziare un processo.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Il processore di contenuti multimediali **Azure Media Indexer** consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce per i sottotitoli codificati. Questa sezione offre alcuni dettagli sulle opzioni che è possibile specificare per questo Management Pack.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Linguaggio
Linguaggio naturale da riconoscere nel file multimediale, ad esempio l'inglese o lo spagnolo. 

### <a name="captions"></a>Sottotitoli
È possibile scegliere un formato di sottotitoli che verrà generato dal contenuto. Un processo di indicizzazione può generare file di sottotitoli codificati nei formati seguenti:  

* **SAMI**
* **TTML**
* **WebVTT**

I file di sottotitoli codificati con questi formati possono essere utili per rendere i file audio e video accessibili alle persone con problemi uditivi.

### <a name="aib-file"></a>File aib
Selezionare questa opzione per generare il file Audio Index Blob per l'uso con SQL Server IFilter personalizzato. Per altre informazioni, vedere [questo](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Parole chiave
Selezionare questa opzione per generare un file XML di parole chiave. Questo file contiene parole chiave estratte da contenuti vocali, con informazioni sulla frequenza e sull'offset.

### <a name="job-name"></a>Nome processo
Nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare lo stato di avanzamento di un processo. 

### <a name="output-file"></a>File di output
Nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse è un processore multimediale che crea fluidi video in time-lapse da contenuti registrati in prima persona o da fotocamere d'azione.  Per altre informazioni, vedere [questo](media-services-hyperlapse-content.md) argomento. Questa sezione offre alcuni dettagli sulle opzioni che è possibile specificare per questo Management Pack.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocità
Specificare la velocità di riproduzione del video di input. L'output è costituito da un rendering stabilizzato e in time-lapse del video di input.

### <a name="job-name"></a>Nome processo
Nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare lo stato di avanzamento di un processo. 

### <a name="output-file"></a>File di output
Nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-face-detector"></a>Rilevamento multimediale volti di Azure
Il processore di contenuti multimediali **Rilevamento multimediale volti di Azure** consente di contare, monitorare i movimenti e persino di valutare la partecipazione e le reazioni del pubblico in base alle espressioni del volto. Questo servizio contiene due funzionalità: 

* **Rilevamento volti**
  
    Il Rilevamento volti rileva e monitora i volti umani all'interno di un video. È possibile rilevare e monitorare diversi volti mentre le persone si muovono; i metadati relativi a ora e luogo vengono restituiti in un file JSON. Durante il monitoraggio, il sistema tenterà di assegnare sempre lo stesso ID al volto mentre la persona si muove sullo schermo, anche se è nascosta o se esce brevemente dall'inquadratura.
  
  > [!NOTE]
  > Questo servizio non esegue il riconoscimento facciale. Se una persona esce dall'inquadratura o viene nascosta troppo a lungo, al suo ritorno le verrà assegnato un nuovo ID.
  > 
  > 
* **Rilevamento emozioni**
  
    Il Rilevamento emozioni è un componente facoltativo del Processore multimediale di rilevamento volti che restituisce un'analisi di vari attributi emotivi dei volti rilevati, inclusi la felicità, la paura, la tristezza, la rabbia e altre emozioni. 

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modalità di rilevamento
Il processore può usare una delle modalità seguenti:

* rilevamento volti
* rilevamento emozioni per volto
* rilevamento emozioni aggregato

### <a name="job-name"></a>Nome processo
Nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare lo stato di avanzamento di un processo. 

### <a name="output-file"></a>File di output
Nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-motion-detector"></a>Rilevatore multimediale di movimento Azure
Il processore di contenuti multimediali **Rilevatore multimediale di movimento Azure** consente di identificare in modo efficace le sezioni interessanti all'interno di un video altrimenti lungo e privo di eventi. Il rilevamento di movimento può essere usato nei filmati statici della videocamera per individuare le sezioni del video in cui si verificano movimenti. Viene generato un file JSON contenente i metadati con i timestamp e l'area di delimitazione in cui si è verificato l'evento.

Questa tecnologia, destinata alle trasmissioni video di sicurezza, è in grado di classificare i movimenti in eventi rilevanti e falsi positivi, ad esempio variazioni di luminosità e delle ombre. In questo modo è possibile generare avvisi di sicurezza dalle trasmissioni della videocamera senza che venga segnalata una serie infinita di eventi irrilevanti e al contempo estrarre i momenti di interesse da video di sorveglianza estremamente lunghi.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Anteprime video multimediali di Azure
Questo processore consente di creare un riepilogo per video lunghi selezionando in modo automatico frammenti interessanti del video di origine. Questa funzione risulta particolarmente utile quando si intende creare una panoramica rapida dei contenuti offerti nella versione più lunga del video. Per informazioni dettagliate ed esempi, vedere [Uso delle anteprime video multimediali di Azure per creare un riepilogo video](media-services-video-summarization.md)

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome processo
Nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare lo stato di avanzamento di un processo. 

### <a name="output-file"></a>File di output
Nome descrittivo che consente di identificare il contenuto di output. 

## <a name="next-steps"></a>Passaggi successivi
Visualizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

