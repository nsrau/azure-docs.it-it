---
title: Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi | Microsoft Docs
description: Distribuire i contenuti crittografati con chiavi di crittografia AES a 128 bit mediante Servizi multimediali di Microsoft Azure. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. In questo argomento viene illustrato come crittografare con AES-128 e utilizzare il servizio di distribuzione delle chiavi in modo dinamico.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 013c14c00096c9958a732d1f0eaacc9248f57da9
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Per ottenere la versione più recente di Java SDK e iniziare a sviluppare con Java, vedere [Introduzione a Java Client SDK per Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Per scaricare la versione più recente di PHP SDK per Servizi multimediali, cercare la versione 0.5.7 del pacchetto Microsoft/WindowAzure nel [repository Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Panoramica
> [!NOTE]
> Per informazioni su come crittografare contenuto con la crittografia Advanced Encryption Standard (AES) per la distribuzione a Safari su macOS, vedere [questo post di blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> Per una panoramica di come proteggere il contenuto multimediale con la crittografia AES, vedere [questo video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 È possibile usare Servizi multimediali per distribuire flussi HTTP Live Streaming (HLS) e Smooth Streaming crittografati con AES usando chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia all'asset e configurare anche i criteri di autorizzazione per la chiave. Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente il contenuto mediante AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati per la chiave.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione della chiave simmetrica possono avere una o più restrizioni di autorizzazione, ad esempio restrizione aperta o di tipo token. I criteri con restrizione di tipo token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (Simple Web Token, token Web semplice) e [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JSON Web Token, token JSON Web). Per altre informazioni, vedere [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy).

Per sfruttare la crittografia dinamica, è necessario disporre di un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. È inoltre necessario configurare i criteri di distribuzione dell'asset (descritti più avanti in questo articolo). Quindi, in base al formato specificato nell'URL di streaming, il server di streaming on demand garantisce che il flusso venga distribuito nel protocollo scelto. Di conseguenza, è necessario archiviare e pagare solo i file in un singolo formato di archiviazione. Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Questo articolo è utile per gli sviluppatori che usano applicazioni che forniscono contenuti protetti. L'articolo illustra come configurare il servizio di distribuzione delle chiavi con criteri di autorizzazione, in modo che solo i client autorizzati possano ricevere le chiavi di crittografia. Viene inoltre illustrato come utilizzare la crittografia dinamica.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Flusso di lavoro della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi

Eseguire i passaggi generali illustrati di seguito quando si crittografano gli asset con AES usando il servizio di distribuzione delle chiavi di Servizi multimediali e la crittografia dinamica:

1. [Creare un asset e caricarvi i file](media-services-protect-with-aes128.md#create_asset).

2. [Codificare l'asset contenente il file nel set MP4 a bitrate adattivo](media-services-protect-with-aes128.md#encode_asset).

3. [Creare una chiave simmetrica e associarla all'asset codificato](media-services-protect-with-aes128.md#create_contentkey). In Servizi multimediali la chiave simmetrica contiene la chiave di crittografia dell'asset.

4. [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy). È necessario configurare i criteri di autorizzazione della chiave simmetrica. Il client deve soddisfare i criteri prima che la chiave simmetrica venga distribuita al client stesso.

5. [Configurare i criteri di distribuzione di un asset](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configurazione di criteri di distribuzione include l'URL di acquisizione della chiave e un vettore di inizializzazione (IV). AES-128 richiede lo stesso IV per la crittografia e la decrittografia. La configurazione include anche il protocollo di recapito (ad esempio, MPEG DASH, HLS, Smooth Streaming o tutti) e il tipo di crittografia dinamica (ad esempio, crittografia envelope o non dinamica).

    È possibile applicare criteri diversi per ogni protocollo nello stesso asset. È ad esempio possibile applicare la crittografia PlayReady a Smooth/DASH e AES Envelope ad HLS. Il flusso di eventuali protocolli che non sono definiti in un criterio di distribuzione viene bloccato. Ad esempio, se si aggiunge un singolo criterio che specifica solo HLS come protocollo. Questo comportamento non si verifica quando non sono definiti criteri di distribuzione degli asset. In tal caso, sono consentiti tutti i protocolli in chiaro.

6. [Creare un localizzatore OnDemand](media-services-protect-with-aes128.md#create_locator) per ottenere un URL di streaming.

L'articolo inoltre indica [come un'applicazione client può richiedere una chiave dal servizio di distribuzione delle chiavi](media-services-protect-with-aes128.md#client_request).

Alla fine dell'articolo è disponibile un [esempio .NET](media-services-protect-with-aes128.md#example) completo.

L'immagine seguente illustra il flusso di lavoro descritto in precedenza. Per l'autenticazione viene usato il token.

![Proteggere con AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

La parte rimanente di questo articolo fornisce spiegazioni, esempi di codice e collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.

## <a name="current-limitations"></a>Limitazioni correnti
Se si aggiungono o si aggiornano i criteri di distribuzione degli asset, è necessario eliminare qualsiasi localizzatore esistente e crearne uno nuovo.

## <a id="create_asset"></a>Creare un asset e caricare file nell'asset
Per gestire, codificare e trasmettere in streaming i video, è prima di tutto necessario caricare il contenuto in Servizi multimediali. Dopo il caricamento, il contenuto viene archiviato in modo sicuro nel cloud per consentire altre operazioni di elaborazione e streaming. 

Per altre informazioni, vedere [Caricare file in un account di Servizi multimediali](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificare l'asset contenente il file nel set MP4 a bitrate adattivo
Con la crittografia dinamica, si crea un asset contenente un set di file MP4 a bitrate multipli o di file di origine Smooth Streaming a bitrate multipli. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantisce che il flusso venga ricevuto nel protocollo scelto. Quindi è solo necessario archiviare e pagare i file in un singolo formato di archiviazione. Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client. Per altre informazioni, vedere [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Quando viene creato l'account di Servizi multimediali, viene aggiunto all'account un endpoint di streaming predefinito con stato "Arrestato". Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato "In esecuzione". 
>
>Per usare la creazione dinamica dei pacchetti e la crittografia dinamica, l'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva.

Per istruzioni su come eseguire la codifica, vedere [Codificare un asset con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Creare una chiave simmetrica e associarla all'asset codificato
In Servizi multimediali, la chiave simmetrica contiene la chiave con cui si desidera crittografare un asset.

Per altre informazioni, vedere [Creare una chiave simmetrica](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave simmetrica
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. È necessario configurare i criteri di autorizzazione della chiave simmetrica. Il client (lettore) deve soddisfare i criteri prima che la chiave possa essere distribuita al client stesso. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open, Token o IP.

Per altre informazioni, vedere [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configurare i criteri di distribuzione degli asset
Configurare i criteri di distribuzione dell'asset. La configurazione dei criteri di distribuzione degli asset include:

* L'URL di acquisizione della chiave. 
* Il vettore di inizializzazione (IV) da usare per la crittografia envelope. AES-128 richiede lo stesso IV per la crittografia e la decrittografia. 
* Protocollo di recapito degli asset (ad esempio, MPEG-DASH, HLS, Smooth Streaming o tutti).
* Il tipo di crittografia dinamica (ad esempio, envelope AES) o nessuna crittografia dinamica. 

Per altre informazioni, vedere [Configurare i criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Creare un localizzatore di streaming on demand per ottenere un URL di streaming
È necessario fornire all'utente l'URL di streaming per Smooth Streaming, DASH o HLS.

> [!NOTE]
> Se si aggiungono o si aggiornano i criteri di distribuzione degli asset, è necessario eliminare qualsiasi localizzatore esistente e crearne uno nuovo.
> 
> 

Per istruzioni su come pubblicare un asset e creare un URL di streaming, vedere la sezione [Creare URL di streaming](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Ottenere un token di test
Ottenere un token di test basato sulla restrizione Token usata per i criteri di autorizzazione della chiave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Per testare il flusso, è possibile usare il [lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a id="client_request"></a>In che modo il client può richiedere una chiave dal servizio di distribuzione delle chiavi?
Nel passaggio precedente, è stato realizzato l'URL che punta a un file manifesto. Il client deve estrarre le informazioni necessarie dai file manifesto del flusso per effettuare una richiesta al servizio di distribuzione delle chiavi.

### <a name="manifest-files"></a>File manifesto
Il client deve estrarre il valore URL (che contiene anche l'ID della chiave simmetrica [kid]) dal file manifesto. Il client tenta quindi di ottenere la chiave di crittografia dal servizio di distribuzione delle chiavi. Inoltre, il client deve estrarre il valore IV e usarlo per decrittografare il flusso. Il frammento di codice seguente illustra l'elemento <Protection> del manifesto Smooth Streaming:

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

Nel caso di HLS, il manifesto radice viene suddiviso in file di segmento. 

Ad esempio, il manifesto radice è: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) e contiene un elenco di nomi di file di segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se si apre uno dei file del segmento in un editor di testo (ad esempio, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), è presente #EXT-X-KEY a indicare che il file è crittografato.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Se si prevede di eseguire un flusso HLS crittografato con AES in Safari, vedere [questo blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Richiedere la chiave dal servizio di distribuzione delle chiavi

Il codice seguente indica come inviare una richiesta al servizio di distribuzione delle chiavi di Servizi multimediali usando un Uri di distribuzione delle chiavi (che è stato estratto dal manifesto) e un token (questo articolo non spiega come ottenere token SWT da un servizio token di sicurezza).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteggere i contenuti con AES-128 tramite .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md).

2. Aggiungere gli elementi seguenti alla sezione appSettings definita nel file app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Esempio

Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
 
>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri di Servizi multimediali (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criteri se si usano sempre gli stessi giorni/le stesse autorizzazioni di accesso. Ad esempio, usare lo stesso ID per i criteri dei localizzatori che devono rimanere sul posto per molto tempo (criteri di non caricamento). Per altre informazioni, vedere la sezione "Limitare i criteri di accesso" dell'articolo [Gestire asset ed entità correlate con l'SDK di Servizi multimediali per .NET](media-services-dotnet-manage-entities.md#limit-access-policies).

Assicurarsi di aggiornare le variabili in modo da puntare alle cartelle in cui si trovano i file di input.

    [!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

