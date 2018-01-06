---
title: Proteggere il contenuto HLS con Apple FairPlay offline | Microsoft Docs
description: "Questo argomento offre una panoramica su come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HTTP Live Streaming (HLS) con Apple FairPlay in modalità offline."
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), offline, iOS 10
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 15f6d422f3171ae5161e0d4d4bcd8ec98529c766
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="offline-fairplay-streaming"></a>FairPlay Streaming offline
 Servizi multimediali di Azure fornisce un set di ben progettata [servizi di protezione del contenuto](https://azure.microsoft.com/services/media-services/content-protection/) che copre:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Crittografia AES-128

Gestione dei diritti digitali (DRM) / crittografia Advanced Encryption Standard (AES) del contenuto viene eseguita in modo dinamico su richiesta per diversi protocolli di streaming. Servizi di distribuzione delle chiavi di DRM licenza/AES decrittografia vengono inoltre forniti da servizi multimediali.

Oltre alla protezione del contenuto per lo streaming online su vari protocolli, anche la modalità offline per il contenuto protetto è una funzionalità molto richiesta. Il supporto di modalità non in linea è necessario per gli scenari seguenti:

* Riproduzione quando la connessione internet non è disponibile, ad esempio durante il viaggio.
* Alcuni provider di contenuti potrebbe impedire il recapito licenza DRM oltre il bordo di un paese. Se gli utenti desiderano controllare contenuto durante gli spostamenti all'esterno del paese, è necessario il download non in linea.
* In alcuni paesi, è comunque limitata disponibilità internet e/o della larghezza di banda. Gli utenti potrebbero scegliere di scaricare innanzitutto per poter visualizzare il contenuto in una soluzione che è sufficientemente elevato per un'esperienza di visualizzazione soddisfacente. In questo caso, il problema non è in genere disponibilità di rete, ma la larghezza di banda di rete limitata. Over-the-top (OTT) / provider di piattaforma video online (OVP) richiede il supporto di modalità non in linea.

Questo articolo descrive il supporto di modalità non in linea FairPlay Streaming (FPS) destinate a dispositivi che eseguono iOS, 10 o versioni successive. Questa funzionalità non è supportata per altre piattaforme di Apple, ad esempio watchOS, tvOS o Safari in Mac OS.

## <a name="preliminary-steps"></a>Operazioni preliminari
Prima di implementare DRM offline per FairPlay in un dispositivo iOS 10 +:

* Acquisire familiarità con la protezione del contenuto online per FairPlay. Per ulteriori informazioni, vedere i seguenti articoli ed esempi:

    - [Apple FairPlay Streaming per servizi multimediali di Azure è disponibile](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Proteggere il contenuto HLS con Apple FairPlay o Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [A sample for online FPS streaming](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/) (Esempio di streaming FPS online)

* Ottenere il SDK di frequenza dei Fotogrammi da in Apple Developer Network. il SDK FPS contiene due componenti:

    - La frequenza dei Fotogrammi Server SDK, che contiene il modulo di protezione di chiave (KSM), esempi di client, una specifica e un set di vettori di test.
    - Pacchetto di distribuzione FPS, che contiene la specifica funzione D, con istruzioni su come generare il certificato FPS, la chiave privata di specifiche del cliente e chiave privata dell'applicazione. Apple genera il pacchetto di distribuzione FPS solo per i provider di contenuti con licenza.

## <a name="configuration-in-media-services"></a>Configurazione in servizi multimediali
Per la configurazione di frequenza dei Fotogrammi modalità offline tramite il [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), usare Media Services .NET SDK versione 4.0.0.4 o versioni successive, che fornisce le API necessaria per configurare la modalità offline FPS.
È necessario anche il codice per configurare la protezione dei contenuti FPS modalità online. Dopo aver ottenuto il codice per configurare la protezione del contenuto in modalità online per la frequenza dei Fotogrammi, è necessario solo le due modifiche seguenti.

## <a name="code-change-in-the-fairplay-configuration"></a>Modifica del codice nella configurazione FairPlay
La prima modifica consiste nel definire "Abilita modalità non in linea" valore booleano, chiamato objDRMSettings.EnableOfflineMode, che restituisce true se consente lo scenario DRM offline. A seconda di questo indicatore, apportare la seguente modifica alla configurazione FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Modifica di codice nella configurazione dei criteri di recapito di asset
La seconda modifica consiste nell'aggiungere la terza chiave nel dizionario < AssetDeliveryPolicyConfigurationKey, string >.
Aggiungere AssetDeliveryPolicyConfigurationKey come illustrato di seguito:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Dopo questo passaggio, la stringa < Dictionary_AssetDeliveryPolicyConfigurationKey > nei criteri di recapito di asset FPS contiene le seguenti tre voci:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl o AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, a seconda di fattori, ad esempio il server di chiave/valore FPS KSM utilizzato e se si riutilizza il recapito di asset stesso criteri tra più Asset
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Account servizi multimediali è ora configurato per il recapito non in linea licenze FairPlay.

## <a name="sample-ios-player"></a>Esempio di lettore iOS
Il supporto di modalità non in linea di frequenza dei Fotogrammi è disponibile solo per iOS 10 e versioni successive. La frequenza dei Fotogrammi Server SDK (versione 3.0 o versione successiva) contiene il documento e l'esempio per la modalità offline FPS. In particolare, FPS Server SDK (versione 3.0 o versione successiva) contiene i due elementi seguenti correlati alla modalità offline:

* Documento: "HTTP e la riproduzione Offline con FairPlay Streaming Live Streaming." Apple, 14 settembre 2016. Frequenza dei Fotogrammi Server SDK versione 4.0, in questo documento viene unito nel documento FPS principale.
* Codice di esempio: HLSCatalog di esempio per la modalità offline nel 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ versione Streaming Server SDK \FairPlay FPS. Nell'applicazione di esempio HLSCatalog, i file di codice seguente vengono utilizzati per implementare le funzionalità di modalità non in linea:

    - File di codice AssetPersistenceManager.swift: AssetPersistenceManager è la classe principale in questo esempio che illustra come:

        - Gestire i flussi HLS download, ad esempio le API utilizzate per avviare e annullare i download ed eliminare risorse esistenti, disattivare i dispositivi.
        - Monitorare lo stato di avanzamento del download.
    - File di codice AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController è l'interfaccia principale di questo esempio. Fornisce un elenco degli asset che di esempio consente di riprodurre, scaricare, eliminare o annullare un download. 

Questa procedura viene illustrato come impostare un lettore di iOS in esecuzione. Se che si avvia tratto dall'esempio HLSCatalog FPS Server SDK versione 4.0.1, apportare le modifiche al codice seguente:

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementare il metodo `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando il codice seguente. Consente di utilizzare una variabile assegnata all'URL di HLS "drmUr".

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

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestApplicationCertificate()`. Questa implementazione varia a seconda del fatto che si incorpori o meno il certificato (solo chiave pubblica) nel dispositivo o si ospiti il certificato sul Web. L'implementazione seguente utilizza il certificato di applicazione ospitata utilizzato negli esempi di test. Consente di utilizzare una variabile che contiene l'URL del certificato dell'applicazione "certUrl".

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

Per i test finali integrato, l'URL del video sia l'URL di certificato dell'applicazione vengono forniti nella sezione "Test integrato".

In HLSCatalog\Shared\Resources\Streams.plist, aggiungere l'URL di video di test. Per il contenuto ID chiave, utilizzare l'URL di acquisizione di licenze FairPlay con il protocollo skd come valore univoco.

![Flussi dell'app iOS FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Utilizzare la propria prova URL video, l'URL di acquisizione licenza FairPlay e URL certificato dell'applicazione, se è necessario impostare. Oppure è possibile continuare a della sezione successiva, che è inclusi esempi di test.

## <a name="integrated-test"></a>Test integrato
Tre esempi di test in servizi multimediali illustrano i tre scenari seguenti:

* Frequenza dei Fotogrammi protetto, con traccia audio, video, audio e alternativo
* Frequenza dei Fotogrammi protetto, con video e audio, ma nessuna traccia audio alternativa
* Frequenza dei Fotogrammi protetto, con solo video e audio non

È possibile trovare questi esempi all'indirizzo [questo sito demo](http://aka.ms/poc#22), con il corrispondente certificato dell'applicazione ospitata in un'app web di Azure.
Con la versione 3 o esempio versione 4 del SDK Server FPS, se una playlist master contiene audio alternativo, durante la modalità non in linea riproduce contenuti audio solo. Pertanto, è necessario rimuovere l'audio alternativo. In altre parole, gli esempi di secondo e terzi elencati in precedenza funzionano in modalità online e offline. L'esempio elencato prima riprodurre l'audio solo durante la modalità offline, mentre in linea streaming funziona correttamente.

## <a name="faq"></a>Domande frequenti
Le seguenti domande frequenti forniscono assistenza per la risoluzione dei problemi:

- **Perché solo l'audio svolge ma non video durante la modalità offline?** Questo comportamento sembra essere predefinito nell'app di esempio. Quando una traccia audio alternativa è present (ovvero nel caso di HLS) durante la modalità offline, iOS 10 e 11 iOS predefinito per la traccia audio alternativa. Per compensare questo comportamento per la modalità offline FPS, rimuovere la traccia audio alternativa dal flusso. A tale scopo su servizi multimediali, aggiungere il filtro dinamico manifesto "solo audio = false." In altre parole, un URL di HLS termina con .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Motivo per cui ancora riprodotto audio solo senza video durante la modalità non in linea dopo che è possibile aggiungere solo audio = false?** A seconda della struttura di chiave della cache (CDN) di rete del recapito del contenuto, potrebbe essere memorizzato nella cache il contenuto. Cancellazione della cache.
- **La modalità offline di FPS è anche supportata in iOS 11 oltre che in iOS 10?** Sì. La modalità offline di frequenza dei Fotogrammi è supportata per iOS 10 e 11 iOS.
- **Non è possibile visualizzare il documento "Non in linea la riproduzione con FairPlay Streaming e HTTP Live Streaming" FPS Server SDK** Poiché FPS Server SDK versione 4, questo documento è stato unito la "FairPlay Streaming Guida per programmatori."
- **Che cosa rappresenta l'ultimo parametro nell'API seguente per la modalità offline di FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Per la documentazione per questa API, vedere [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metodo](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Il parametro rappresenta la durata di noleggio offline, con l'ora come unità.
- **Qual è la struttura del file scaricato/offline nei dispositivi iOS?** La struttura dei file scaricati in un dispositivo iOS è simile nella schermata seguente. Il `_keys` archivi cartelle scaricati licenze FPS, con un archivio file per ogni host del servizio di licenza. Il `.movpkg` cartella vengono archiviati al contenuto audio e video. La prima cartella con un nome che termina con un trattino seguito da un valore numerico contenuto video. Il valore numerico è PeakBandwidth delle copie video. La seconda cartella con un nome che termina con un trattino seguito da 0 contiene contenuto audio. La terza cartella denominata "Data" contiene la playlist master del contenuto FPS. Infine, boot.xml fornisce una descrizione completa del `.movpkg` contenuto della cartella. 

![Struttura di file dell'app di esempio offline FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Un file boot.xml di esempio:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="summary"></a>Summary
Questo documento include i passaggi e le informazioni che è possibile utilizzare per implementare la modalità offline FPS:

* Configurazione di protezione del contenuto di servizi multimediali tramite l'API .NET di servizi multimediali consente di configurare la crittografia dinamica FairPlay e il recapito di licenze FairPlay in servizi multimediali.
* Un lettore di iOS in base l'esempio di SDK FPS Server consente di impostare un lettore di iOS in grado di riprodurre FPS contenuto in modalità flusso in linea o in modalità offline.
* Video di esempio FPS viene utilizzati per eseguire il test in modalità offline e flussi online.
* Un risposte alle domande sulla modalità non in linea di frequenza dei Fotogrammi.
