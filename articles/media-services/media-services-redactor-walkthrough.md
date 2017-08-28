---
title: 'Procedura dettagliata: offuscare i volti con Analisi Servizi multimediali di Azure | Documentazione Microsoft'
description: Questo argomento fornisce istruzioni dettagliate su come eseguire un flusso di lavoro di offuscamento completo usando Azure Media Services Explorer (AMSE) e Azure Media Redactor Visualizer (strumento open source).
services: media-services
documentationcenter: 
author: Lichard
manager: cfowler
editor: 
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/03/2017
ms.author: rli; juliako;
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b34502aa75d94da1abcda880bb1a251a8455f3f8
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Procedura dettagliata: offuscare i volti con Analisi Servizi multimediali di Azure

## <a name="overview"></a>Panoramica

**Azure Media Redactor** è un processore di contenuti multimediali di [Analisi Servizi multimediali di Azure](media-services-analytics-overview.md) che offre funzionalità scalabili di offuscamento dei volti nel cloud. L'offuscamento dei volti consente di modificare un video per sfocare i volti di persone selezionate. Può essere opportuno usare tale servizio in scenari di pubblica sicurezza e notizie giornalistiche. Offuscare manualmente alcuni minuti di filmato contenenti più volti può richiedere ore, ma con questo servizio il processo di offuscamento dei volti richiederà pochi semplici passaggi. Per altre informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Per informazioni dettagliate su **Azure Media Redactor**, vedere la [panoramica sull'offuscamento dei volti](media-services-face-redaction.md).

Questo argomento fornisce istruzioni dettagliate su come eseguire un flusso di lavoro di offuscamento completo usando Azure Media Services Explorer (AMSE) e Azure Media Redactor Visualizer (strumento open source).

Il processore di contenuti multimediali **Azure Media Redactor** è attualmente in anteprima. È disponibile in tutte le aree di Azure pubbliche, nonché nei data center cinesi e del governo degli USA. Al momento questa versione di anteprima è disponibile gratuitamente. Nella versione corrente la durata dei video elaborati è limitata a 10 minuti.

Per altre informazioni, vedere [questo](https://azure.microsoft.com/en-us/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Flusso di lavoro di Azure Media Services Explorer

Il modo più semplice iniziare a usare Azure Media Redactor è usare lo strumento AMSE open source su GitHub. Se non occorre accedere al file JSON di annotazioni o alle immagini .JPG dei volti, è possibile eseguire un flusso di lavoro semplificato in modalità **combinata**.

### <a name="download-and-setup"></a>Download e installazione

1. Scaricare lo strumento AMSE da [qui](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Accedere al proprio account di Servizi multimediali usando la chiave del servizio.

    Per ottenere il nome dell'account e la chiave, accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'account AMS. Dopodiché, selezionare Impostazioni > Chiavi. Nella finestra Gestisci chiavi sono visualizzati il nome dell'account e le chiavi primaria e secondaria. Copiare i valori del nome dell'account e della chiave primaria.

![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Primo passaggio: modalità analisi

1. Caricare il file multimediale facendo clic su Asset -> Carica oppure trascinandolo. 
1. Fare clic sul file multimediale con il pulsante destro del mouse ed elaborarlo usando Analisi Servizi multimediali -> Azure Media Redactor -> Modalità analisi. 


![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

L'output includerà un file JSON di annotazioni contenente i dati relativi alla posizione del volto, oltre all'immagine di ogni volto rilevato in formato .JPG. 

![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

###<a name="second-pass--redact-mode"></a>Secondo passaggio: modalità offuscamento

1. Caricare l'asset video originale nell'output del primo passaggio e impostarlo come asset principale. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Facoltativo) Caricare un file "Dance_idlist.txt" che include un elenco delimitato da nuova riga degli ID che si desidera offuscare. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Facoltativo) Modificare il file annotations.json, ad esempio aumentando le dimensioni dei rettangoli di selezione. 
4. Fare clic con il pulsante destro del mouse sull'asset di output del primo passaggio, scegliere Redactor ed eseguirlo in modalità **Redact** (Offusca). 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Scaricare o condividere l'asset di output con l'offuscamento. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

##<a name="azure-media-redactor-visualizer-open-source-tool"></a>Strumento open source Azure Media Redactor Visualizer

Il [visualizzatore](https://github.com/Microsoft/azure-media-redactor-visualizer) open source ha lo scopo di introdurre gli sviluppatori al formato delle annotazioni con l'analisi e l'uso dell'output.

Dopo aver clonato il repository, per eseguire il progetto sarà necessario scaricare FFmpeg dal [sito ufficiale](https://ffmpeg.org/download.html).

Se lo sviluppatore tenta di analizzare i dati delle annotazioni JSON, consultare i codici di esempio in Models.MetaData.

### <a name="set-up-the-tool"></a>Impostare lo strumento

1.  Scaricare e compilare l'intera soluzione. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Scaricare FFmpeg da [qui](https://ffmpeg.org/download.html). Questo progetto è stato originariamente sviluppato con la versione be1d324 (04-10-2016) con il collegamento statico. 
3.  Copiare ffmpeg.exe e ffprobe.exe nella stessa cartella di output di AzureMediaRedactor.exe. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Eseguire AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Usare lo strumento

1. Elaborare il video nell'account di Servizi multimediali di Azure con il Management Pack Redactor in modalità di analisi. 
2. Scaricare il file video originale e l'output del processo di offuscamento e analisi. 
3. Eseguire l'applicazione del visualizzatore e scegliere i file nella finestra qui sopra. 

    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Visualizzare l'anteprima del file. Selezionare i volti da offuscare usando la barra laterale a destra. 
    
    ![Offuscamento dei volti](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Il campo di testo in basso verrà aggiornato con gli ID dei volti. Creare un file denominato "idlist.txt" contenente questi ID come elenco delimitato da nuova riga. 

    >[!NOTE]
    > Il file idlist.txt deve essere salvato in formato ANSI. È possibile usare il Blocco note per il salvataggio in formato ANSI.
    
6.  Caricare questo file nell'output di asset del Passaggio 1. In questo asset caricare anche il video originale e impostarlo come asset principale. 
7.  Eseguire il processo di offuscamento nell'asset con la modalità "Redact" (Offusca) per ottenere il video finale offuscato. 

## <a name="next-steps"></a>Passaggi successivi 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Collegamenti correlati
[Panoramica di Analisi servizi multimediali di Azure](media-services-analytics-overview.md)

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Presto sarà disponibile l'offuscamento dei volti per Analisi Servizi multimediali di Azure](https://azure.microsoft.com/blog/azure-media-redactor/)

