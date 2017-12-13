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
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>FairPlay Streaming offline
Servizi multimediali di Microsoft Azure include un set di [servizi di protezione dei contenuti](https://azure.microsoft.com/services/media-services/content-protection/) ben progettati, che riguardano:
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Crittografia AES-128

La crittografia DRM/AES del contenuto viene eseguita in modo dinamico su richiesta per vari protocolli di streaming. Servizi multimediali di Azure offre anche servizi di distribuzione di chiavi di licenza DRM e decrittografia EAS.

Oltre alla protezione del contenuto per lo streaming online su vari protocolli, anche la modalità offline per il contenuto protetto è una funzionalità molto richiesta. Il supporto della modalità offline è necessaria per gli scenari seguenti:
1. Riproduzione quando la connessione Internet non è disponibile, ad esempio in viaggio.
2. Alcuni provider di contenuti potrebbero disattivare la distribuzione di licenze DRM oltre i confini di un paese. Se un utente vuole consultare il contenuto durante una trasferta all'estero, è necessario il download offline.
3. In alcuni paesi la disponibilità di Internet e/o la larghezza di banda sono limitate. Gli utenti possono scegliere di scaricare a priori il contenuto per poterlo visualizzare in una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente. In questo caso, più spesso, il problema non è la disponibilità della rete, ma una limitazione nella larghezza di banda. I provider OTT/OVP richiedono il supporto della modalità offline.

Questo articolo è incentrato sul supporto della modalità offline di FairPlay Streaming (FPS) per dispositivi che eseguono iOS 10 o versioni successive. Questa funzionalità non è supportata per altre piattaforme Apple, ad esempio watchOS, tvOS o Safari su Mac OS.

## <a name="preliminary-steps"></a>Operazioni preliminari
Prima di implementare DRM offline per FairPlay in un dispositivo iOS 10 +, è necessario prima:
1. Acquisire familiarità con la protezione del contenuto online per FairPlay. Questo aspetto è illustrato in dettaglio negli esempi/articoli seguenti:
- [Apple FairPlay Streaming for Azure Media Services generally available](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/) (Apple FairPlay Streaming per Servizi multimediali di Azure disponibile a livello generale)
- [Proteggere il contenuto HLS con Apple FairPlay o Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [A sample for online FPS streaming](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/) (Esempio di streaming FPS online)
2. Ottenere FPS SDK da Apple Developer Network. FPS SDK contiene due componenti:
- FPS Server SDK, che contiene KSM (Key Security Module), esempi di client, una specifica e un set di vettori di test;
- FPS Deployment Pack, che contiene D Function, una specifica e istruzioni su come generare il certificato FPS, una chiave privata specifica del cliente e la chiave privata dell'applicazione. Apple concede gli FPS Deployment Pack ai soli provider di contenuti con licenza.

## <a name="configuration-in-azure-media-services"></a>Configurazione in Servizi multimediali di Azure
Per la configurazione della modalità offline di FPS tramite [SDK di Servizi multimediali di Azure per .NET](https://www.nuget.org/packages/windowsazure.mediaservices) è necessario usare l'SDK di Servizi multimediali di Azure per .NET 4.0.0.4 o versioni successive, che includono l'API necessaria per configurare la modalità offline di FPS.
Come indicato in precedenza, si presuppone di avere il codice di lavoro per configurare la protezione del contenuto FPS in modalità online. Dopo aver creato il codice per la configurazione della protezione del contenuto in modalità online per FPS, è sufficiente apportare le due modifiche seguenti.

## <a name="code-change-in-fairplay-configuration"></a>Modifica del codice nella configurazione FairPlay
Definiamo un valore booleano per abilitare la modalità offline, denominato objDRMSettings.EnableOfflineMode booleano, che è true quando si abilita lo scenario DRM offline. In base a questo indicatore, apportiamo la modifica seguente alla configurazione FairPlay:

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

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Modifica del codice nella configurazione dei criteri di distribuzione degli asset
La seconda modifica consiste nell'aggiungere la terza chiave nel dizionario Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
Il terzo AssetDeliveryPolicyConfigurationKey deve essere aggiunto come indicato di seguito: 
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

Dopo questo passaggio, il dizionario Dictionary<AssetDeliveryPolicyConfigurationKey, string> nei criteri di distribuzione degli asset FPS conterrà le tre voci seguenti:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl o AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, a seconda di fattori come il server chiavi/KSM FPS usato e l'intenzione o meno di usare di nuovo gli stessi criteri di distribuzione tra più asset
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Ora l'account di servizi multimediali è configurato per la distribuzione di licenze FairPlay offline.

## <a name="sample-ios-player"></a>Esempio di lettore iOS
Osserviamo innanzitutto che il supporto della modalità offline di FPS è disponibile solo in iOS 10 e versioni successive. Scarichiamo FPS Server SDK (3.0 o versioni successive), che contiene le istruzioni e un esempio per la modalità offline di FPS. In particolare, FPS Server SDK (3.0 o versioni successive) contiene i due elementi seguenti correlati alla modalità offline:
1. Documento: Offline Playback with FairPlay Streaming and HTTP Live Streaming (Riproduzione offline con FairPlay Streaming e HTTP Live Streaming). Apple, 14/09/2016. In FPS Server SDK v 4.0 questo documento è stato integrato nella documentazione principale relativa allo streaming FPS.
2. Codice di esempio: esempio HLSCatalog per la modalità offline di FPS in \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Nell'app HLSCatalog di esempio i file di codice seguenti sono destinatati all'implementazione delle funzionalità della modalità offline:
- File di codice AssetPersistenceManager.swift: AssetPersistenceManager è la classe principale in questo esempio che dimostra:
    - Come gestire i download dei flussi HLS, ad esempio le API per l'avvio e l'annullamento dei download e l'eliminazione degli asset esistenti dal dispositivo dell'utente.
    - Come monitorare lo stato del download.
- File di codice AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController è l'interfaccia principale di questo esempio. Fornisce un elenco degli asset che l'esempio può riprodurre, scaricare, eliminare o di cui può annullare il download. 

Di seguito sono indicati i passaggi dettagliati per la configurazione di un lettore iOS in esecuzione. Si supponga di iniziare dall'esempio HLSCatalog in FPS Server SDK v 4.0.1.  È necessario apportare le modifiche seguenti al codice:

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando il codice seguente: drmUr deve essere una variabile assegnata all'URL di streaming HLS.

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

In HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementare il metodo `requestApplicationCertificate()`. Questa implementazione varia a seconda del fatto che si incorpori o meno il certificato (solo chiave pubblica) nel dispositivo o si ospiti il certificato sul Web. Di seguito è riportata un'implementazione che usa il certificato dell'applicazione ospitato degli esempi di test precedenti. certUrl deve essere una variabile contenente l'URL del certificato dell'applicazione.

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

Per il test integrato finale, l'URL del video e l'URL del certificato dell'applicazione saranno entrambi disponibili nella sezione Test integrato.

In HLSCatalog\Shared\Resources\Streams.plist aggiungere l'URL del video di test e, per l'ID della chiave simmetrica, è sufficiente usare l'URL di acquisizione della licenza FairPlay con il protocollo skd come valore univoco.

![Flussi dell'app iOS FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Per l'URL del video di test, l'URL di acquisizione della licenza FairPlay e l'URL del certificato dell'applicazione è possibile usare URL personalizzati, se configurati, o procedere alla sezione successiva, che contiene esempi di test.

## <a name="integrated-test"></a>Test integrato
In Servizi multimediali di Azure sono stati configurati tre test di esempio che riguardano questi tre scenari:
1.  FPS protetto, con traccia video, audio e audio alternativo.
2.  FPS protetto, con traccia video e audio, ma senza audio alternativo.
3.  FPS solo con traccia video, senza audio.

Questi esempi sono disponibili in nel [sito di demo](http://aka.ms/poc#22), con il certificato dell'applicazione corrispondente ospitato in un'app Web di Azure.
Abbiamo notato che, con l'esempio v3 o v4 di FPS Server SDK, se una playlist master contiene audio alternativo, durante la modalità offline riproduce solo l'audio. È pertanto necessario rimuovere la traccia audio alternativa. In altre parole, tra i tre esempi precedenti, (2) e (3) funzionano in modalità online e offline. L'esempio (1) riprodurrà invece solo l'audio durante la modalità offline, mentre lo streaming online funzionerà perfettamente.

## <a name="faq"></a>domande frequenti
Altre domande frequenti sulla risoluzione dei problemi:
- **Perché viene riprodotto solo l'audio senza il video durante la modalità offline?** Questo comportamento sembra essere predefinito nell'app di esempio. Se è presente una traccia audio alternativa (come nel caso di HLS), durante la modalità offline verrà riprodotta in iOS 10 e iOS 11 per impostazione predefinita. Per compensare questo comportamento per la modalità offline di FPS, è necessario rimuovere la traccia audio alternativa dal flusso. A tale scopo, sul lato Servizi multimediali di Azure è sufficiente aggiungere il filtro di manifesto dinamico "audio-only=false". In altre parole, un URL HLS terminerebbe con .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Perché, anche dopo aver aggiunto audio-only=false, viene ancora riprodotto solo l'audio senza video?** A seconda della progettazione della chiave di cache della rete CDN, il contenuto potrebbe essere memorizzato nella cache. È necessario ripulire la cache.
- **La modalità offline di FPS è anche supportata in iOS 11 oltre che in iOS 10?** Sì, la modalità offline di FPS è supportata in iOS 10 e iOS 11.
- **Non riesco a trovare il documento Offline Playback with FairPlay Streaming and HTTP Live Streaming in FPS Server SDK. Perché?** A partire da FPS Server SDK v 4, questo documento è stato unito nel documento FairPlay Streaming Programming Guide.
- **Che cosa rappresenta l'ultimo parametro nell'API seguente per la modalità offline di FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

La documentazione per questa API è disponibile [qui](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Il parametro rappresenta la durata del periodo di noleggio offline con l'ora specificata come unità.
- **Qual è la struttura del file scaricato/offline nei dispositivi iOS?** La struttura del file scaricato in un dispositivo iOS è simile a quanto riportato di seguito (screenshot). La cartella `_keys` archivia le licenze FPS scaricate, un file di archivio per ogni host del servizio licenze. La cartella `.movpkg` archivia il contenuto audio e video. La prima cartella con il nome che termina con un trattino seguito da un valore numerico include il contenuto video. Il valore numerico è il valore "PeakBandwidth" del rendering del video. La seconda cartella con il nome che termina con un trattino seguito da 0 include contenuto audio. La terza cartella denominata "Data" contiene la playlist master del contenuto FPS. Boot.xml include una descrizione completa del contenuto della cartella `.movpkg` (vedere di seguito un file boot.xml di esempio).

![Struttura del file dell'app di esempio iOS di FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>Riepilogo
In questo documento sono stati illustrati i passaggi dettagliati e le informazioni per implementare la modalità offline di FPS, tra cui:
1. Configurazione della protezione del contenuto di Servizi multimediali Azure tramite l'API AMS .NET. Questa procedura configura la crittografia dinamica di FairPlay e la distribuzione di licenze FairPlay in Servizi multimediali di Azure.
2. Lettore iOS basato sull'esempio di Apple FPS Server SDK. Configura un lettore iOS in grado di riprodurre contenuto FPS in modalità di streaming online o in modalità offline.
3. Video FPS di esempio per test della modalità offline e dello streaming online.
4. Domande frequenti sulla modalità offline di FPS.
