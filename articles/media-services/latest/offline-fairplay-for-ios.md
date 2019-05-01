---
title: Proteggere il contenuto HLS con Apple FairPlay offline | Microsoft Docs
description: Questo argomento offre una panoramica su come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HTTP Live Streaming (HLS) con Apple FairPlay in modalità offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 25bc7798853d350139a7802eaad68d52a1d7d99f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996608"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Modalità offline di FairPlay Streaming per iOS 

 Servizi multimediali di Azure include un set di [servizi di protezione del contenuto](https://azure.microsoft.com/services/media-services/content-protection/) ben progettati che riguardano le tecnologie seguenti:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Crittografia AES-128

La crittografia DRM (Digital Rights Management) o AES (Advanced Encryption Standard) del contenuto viene eseguita in modo dinamico su richiesta per vari protocolli di streaming. Servizi multimediali offre anche servizi di distribuzione delle chiavi di licenza DRM o decrittografia AES.

Oltre alla protezione del contenuto per lo streaming online su vari protocolli, anche la modalità offline per il contenuto protetto è una funzionalità molto richiesta. Il supporto della modalità offline è necessaria per gli scenari seguenti:

* Riproduzione del contenuto quando la connessione Internet non è disponibile, ad esempio in viaggio.
* Alcuni provider di contenuti potrebbero disattivare la distribuzione di licenze DRM oltre i confini di un paese. Per visualizzare del contenuto all'esterno del paese, è prima necessario scaricarlo offline.
* In alcuni paesi la disponibilità di Internet e/o la larghezza di banda sono limitate. Gli utenti possono quindi scegliere di scaricare prima il contenuto per ottenere una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente. In questo caso, il problema in genere non riguarda la disponibilità della rete, ma la larghezza di banda di rete limitata. I provider OTT (Over-The-Top) o OVP (Online Video Platform) richiedono il supporto della modalità offline.

Questo articolo illustra il supporto della modalità offline di FairPlay Streaming (FPS) per dispositivi che eseguono iOS 10 o versioni successive. Questa funzionalità non è supportata per altre piattaforme Apple, ad esempio watchOS, tvOS o Safari su macOS.

## <a name="prerequisites"></a>Prerequisiti

Prima di implementare la tecnologia DRM offline per FairPlay in un dispositivo iOS 10 o versioni successive:

* Consultare la protezione del contenuto online per FairPlay: 

    - [Configurazione e requisiti della licenza Apple FairPlay](fairplay-license-overview.md)
    - [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
    - Un esempio di .NET che include la configurazione dello streaming FairPlay online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Ottenere FPS SDK da Apple Developer Network. Questo SDK è costituito da due componenti:

    - FPS Server SDK, che contiene il modulo KSM (Key Security Module), esempi di client, una specifica e un set di vettori di test.
    - FPS Deployment Pack, che contiene la specifica della funzione D, le istruzioni su come generare il certificato FPS, la chiave privata specifica del cliente e la chiave privata dell'applicazione. Apple rilascia FPS Deployment Pack ai soli provider di contenuti con licenza.
* Clonare https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    È necessario modificare il codice in [Encrypt with DRM using .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) (Eseguire la crittografia con DRM usando .NET) per aggiungere configurazioni FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configurare la protezione dei contenuti in Servizi multimediali di Azure

Nel metodo [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) eseguire queste operazioni:

Rimuovere il commento dal codice che configura l'opzione dei criteri FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Inoltre, rimuovere il commento dal codice che aggiunge CBCS ContentKeyPolicyOption all'elenco di ContentKeyPolicyOption

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Abilitare la modalità offline

Per abilitare la modalità offline, creare un'entità StreamingPolicy personalizzata e usare il relativo nome quando si crea un'entità StreamingLocator in [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

L'account di Servizi multimediali è ora configurato per la distribuzione di licenze FairPlay offline.

## <a name="sample-ios-player"></a>Esempio di lettore iOS

Il supporto della modalità offline di FPS è disponibile solo in iOS 10 e versioni successive. FPS Server SDK (versione 3.0 o successive) contiene il documento di istruzioni e un esempio per la modalità offline di FPS. In particolare, FPS Server SDK (versione 3.0 o successive) contiene i due elementi seguenti correlati alla modalità offline:

* Documento: "Offline Playback with FairPlay Streaming and HTTP Live Streaming" (Riproduzione offline con FairPlay Streaming e HTTP Live Streaming). Apple, 14 settembre 2016. In FPS Server SDK versione 4.0 questo documento è stato unito al documento principale di FPS.
* Codice di esempio: esempio HLSCatalog (parte di FPS Server SDK di Apple) per la modalità offline di FPS in \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Nell'app HLSCatalog di esempio, per implementare le funzionalità della modalità offline vengono usati i file di codice seguenti:

    - File di codice AssetPersistenceManager.swift: AssetPersistenceManager è la classe principale in questo esempio che illustra quanto segue:

        - Come gestire il download dei flussi HLS, ad esempio le API usate per avviare e annullare i download e per eliminare asset esistenti dai dispositivi.
        - Come monitorare lo stato di avanzamento del download.
    - File di codice AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController è la principale interfaccia di questo esempio. Fornisce un elenco degli asset che l'esempio può usare per eseguire operazioni di riproduzione, download o eliminazione o per annullare un download. 

Questi passaggi illustrano come configurare un lettore iOS in esecuzione. Se si inizia dall'esempio HLSCatalog in FPS Server SDK versione 4.0.1, apportare le modifiche seguenti al codice:

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando il codice seguente. "drmUrl" deve essere una variabile assegnata all'URL di HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestApplicationCertificate()`. Questa implementazione varia a seconda del fatto che si incorpori o meno il certificato (solo chiave pubblica) nel dispositivo o si ospiti il certificato sul Web. L'implementazione seguente usa il certificato dell'applicazione ospitato degli esempi di test. "certUrl" deve essere una variabile che contiene l'URL del certificato dell'applicazione.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Per il test integrato finale, l'URL del video e l'URL del certificato dell'applicazione sono entrambi riportati nella sezione "Test integrato".

In HLSCatalog\Shared\Resources\Streams.plist aggiungere l'URL del video di test. Per l'ID della chiave simmetrica, usare l'URL di acquisizione delle licenze FairPlay con il protocollo skd come valore univoco.

![Flussi dell'app iOS FairPlay offline](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Per l'URL del video di test, l'URL di acquisizione delle licenze FairPlay e l'URL del certificato dell'applicazione, usare valori personalizzati, se configurati. In alternativa, è possibile continuare con la sezione successiva, che contiene esempi di test.

## <a name="integrated-test"></a>Test integrato

Per Servizi multimediali sono disponibili tre test di esempio che riguardano questi tre scenari:

* FPS protetto, con video, audio e traccia audio alternativa
* FPS protetto, con video e audio, ma senza traccia audio alternativa
* FPS protetto, solo con video, senza audio

È possibile trovare gli esempi in [questo sito di demo](https://aka.ms/poc#22), con il certificato dell'applicazione corrispondente ospitato in un'app Web di Azure.
Con l'esempio versione 3 o 4 di FPS Server SDK, se una playlist master contiene una traccia audio alternativa, durante la modalità offline viene riprodotto solo l'audio. È pertanto necessario rimuovere la traccia audio alternativa. In altre parole, il secondo e il terzo esempio elencati in precedenza funzionano in modalità online e offline. L'esempio elencato per primo riproduce solo l'audio durante la modalità offline, mentre lo streaming online funziona correttamente.

## <a name="faq"></a>Domande frequenti

Questa sezione include le risposte ad alcune domande frequenti utili per la risoluzione dei problemi:

- **Perché in modalità offline viene riprodotto solo l'audio senza video?** Questo comportamento sembra essere predefinito nell'app di esempio. Se in modalità offline è presente una traccia audio alternativa (come nel caso di HLS), per impostazione predefinita in iOS 10 e iOS 11 viene riprodotta tale traccia. Per ovviare a questo comportamento per la modalità offline di FPS, rimuovere la traccia audio alternativa dal flusso. Per eseguire questa operazione in Servizi multimediali, aggiungere il filtro di manifesto dinamico "audio-only=false". In altre parole, un URL HLS termina con .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Perché, anche dopo aver aggiunto audio-only=false, in modalità offline continua a essere riprodotto solo l'audio senza il video?** A seconda del modo in cui è progettata la chiave di cache della rete CDN, è possibile che il contenuto sia memorizzato nella cache. Ripulire la cache.
- **La modalità offline di FPS è anche supportata in iOS 11 oltre che in iOS 10?** Sì. La modalità offline di FPS è supportata per iOS 10 e iOS 11.
- **Il documento "Offline Playback with FairPlay Streaming and HTTP Live Streaming" non è incluso in FPS Server SDK. Perché?** A partire dalla versione 4 di FPS Server SDK, questo documento è stato incluso in "FairPlay Streaming Programming Guide" (Guida alla programmazione di FairPlay Streaming).
- **Qual è la struttura del file scaricato/offline nei dispositivi iOS?** La struttura del file scaricato in un dispositivo iOS è simile a quella illustrata nello screenshot seguente. La cartella `_keys` archivia le licenze FPS scaricate, con un singolo file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. La prima cartella con il nome che termina con un trattino seguito da un valore numerico include contenuto video. Il valore numerico è il valore PeakBandwidth del rendering del video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza cartella denominata "Data" contiene la playlist master del contenuto FPS. Infine, boot.xml fornisce una descrizione completa del contenuto della cartella `.movpkg`. 

![Struttura del file dell'app di esempio iOS di FairPlay offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Un file boot.xml di esempio:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Passaggi successivi

Scoprire come applicare la [protezione con AES-128](protect-with-aes128.md)
