---
title: Set di impostazioni delle attività per Azure Media Indexer
description: Questo argomento offre una panoramica del set di impostazioni delle attività per servizi multimediali di Azure Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896033"
---
# <a name="task-preset-for-azure-media-indexer"></a>Set di impostazioni delle attività per Azure Media Indexer 

Azure Media Indexer è un processore di contenuti multimediali che consente di eseguire le seguenti attività: eseguire ricerche nel contenuto e nei file multimediali, generare tracce con parole chiave e sottotitoli codificati, indicizzare i file di risorse che fanno parte della risorsa.

Questo argomento descrive il set di impostazioni delle attività che è necessario passare al processo di indicizzazione. Per un esempio completo, vedere [Indicizzazione di file multimediali con Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>File XML di configurazione di Azure Media Indexer

La tabella seguente illustra gli elementi e gli attributi del file XML di configurazione.

|name|Valore richiesto|Description|
|---|---|---|
|Input|true|File di asset che si desidera indicizzare.<br/>Azure Media Indexer supporta i seguenti formati di file multimediali: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>È possibile specificare il nome o i nomi file nell'attributo **name** o **list** dell'elemento **input**, come illustrato di seguito. Se non si specifica il file di risorse da indicizzare, viene selezionato il file primario. Se non è impostato alcun file di asset primario, viene indicizzato il primo file dell'asset di input.<br/><br/>Per specificare in modo esplicito il nome del file di asset, eseguire:<br/>```<input name="TestFile.wmv" />```<br/><br/>È anche possibile indicizzare più file di asset contemporaneamente (fino a 10). A tale scopo, effettuare l'operazione seguente:<br/>- Creare un file di testo (file manifesto) con estensione lst.<br/>- Nel file manifesto aggiungere un elenco di tutti i nomi dei file di risorse inclusi nella risorsa di input.<br/>- Aggiungere (caricare) il file manifesto nella risorsa.<br/>- Specificare il nome del file manifesto nell'attributo list dell'input.<br/>```<input list="input.lst">```<br/><br/>**Nota**: se si aggiungono più di 10 file al file manifesto, il processo di indicizzazione avrà esito negativo con codice di errore 2006.|
|metadata|false|Metadati per il file o i file di risorse specificati.<br/>```<metadata key="..." value="..." />```<br/><br/>È possibile fornire i valori per le chiavi predefinite. <br/><br/>Attualmente sono supportate le chiavi seguenti:<br/><br/>**title** e **description**: usate per aggiornare il modello di lingua per migliorare l'accuratezza del riconoscimento vocale.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** e **password**: usate per l'autenticazione quando si scaricano file da Internet tramite http o https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>I valori di nome utente e password si applicano a tutti gli URL di file multimediali nel manifesto di input.|
|elastico<br/><br/>Aggiunto nella versione 1.2. Attualmente la sola funzionalità supportata è il riconoscimento vocale ("ASR").|false|La funzionalità di riconoscimento vocale ha le chiavi di impostazioni seguenti:<br/><br/>Lingua:<br/>- Lingua naturale da riconoscere nel file multimediale.<br/>- Inglese, spagnolo<br/><br/>CaptionFormats:<br/>- Elenco separato da punto e virgola dei formati desiderati per l'output dell'eventuale barra del titolo.<br/>-TTML; WEBVTT<br/><br/><br/>GenerateKeywords:<br/>- Flag booleano che specifica se sia o meno necessario un file XML di parole chiave.<br/>- True; false|

## <a name="azure-media-indexer-configuration-xml-example"></a>Esempio di file XML di configurazione di Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Passaggi successivi

Vedere [Indicizzazione di file multimediali con Azure Media Indexer](media-services-index-content.md).

