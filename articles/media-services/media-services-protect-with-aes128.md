---
title: Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi | Microsoft Docs
description: Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati con AES mediante chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. In questo argomento viene illustrato come crittografare con AES-128 e utilizzare il servizio di distribuzione delle chiavi in modo dinamico.
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
ms.openlocfilehash: 04c015a6fb6f9398e83b8717e869ba1d8e32a702
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

## <a name="overview"></a>Panoramica
> [!NOTE]
> Vedere questo [post di blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) per crittografare i contenuti tramite AES per il recapito a **Safari su macOS**.
> Vedere [questo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) video per una panoramica di come proteggere il contenuto multimediale con la crittografia AES.
> 
> 

Servizi multimediali di Microsoft Azure consente di distribuire Http-Live-Streaming (HLS) e Smooth Streams crittografati con AES (Advanced Encryption Standard) mediante chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia all'asset e configurare anche i criteri di autorizzazione per la chiave. Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open o Token. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Per altre informazioni, vedere l'argomento [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy).

Per sfruttare la crittografia dinamica, è necessario disporre di un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. È inoltre necessario configurare i criteri di distribuzione dell'asset (descritti più avanti in questo articolo). Quindi, in base al formato specificato nell'URL di streaming, il server di streaming on demand garantisce che il flusso sia distribuito nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Questo articolo potrebbe essere utile per gli sviluppatori che usano applicazioni che forniscono contenuti protetti. L'articolo illustra come configurare il servizio di distribuzione delle chiavi con criteri di autorizzazione, in modo che solo i client autorizzati possano ricevere le chiavi di crittografia. Viene inoltre illustrato come utilizzare la crittografia dinamica.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Flusso di lavoro della crittografia dinamica AES-128 e servizio di distribuzione delle chiavi

Di seguito sono indicati i passaggi generali da eseguire quando si esegue la crittografia degli asset con AES, tramite il servizio di distribuzione delle chiavi di Servizi multimediali e tramite la crittografia dinamica.

1. [Creare un asset e caricare file nell'asset](media-services-protect-with-aes128.md#create_asset).
2. [Codificare l'asset contenente il file per il set di file MP4 con velocità in bit adattiva](media-services-protect-with-aes128.md#encode_asset).
3. [Creare una chiave simmetrica e associarla all'asset codificato](media-services-protect-with-aes128.md#create_contentkey). In Servizi multimediali, la chiave simmetrica contiene la chiave di crittografia dell'asset.
4. [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy). I criteri di autorizzazione della chiave simmetrica devono essere configurati dall'utente e soddisfatti dal client affinché la chiave simmetrica possa essere distribuita al client.
5. [Configurare i criteri di distribuzione di un asset](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configurazione dei criteri di distribuzione include: l'URL di acquisizione della chiave e il vettore di inizializzazione, AES 128 richiede che venga fornito lo stesso vettore di inizializzazione per la crittografia e la decrittografia; il protocollo di recapito, ad esempio MPEG-DASH, HLS, Smooth Streaming o tutti; il tipo di crittografia dinamica, ad esempio envelope o nessuna crittografia dinamica.

    È possibile applicare criteri diversi per ogni protocollo allo stesso asset. Ad esempio, è possibile applicare la crittografia PlayReady a Smooth/DASH e AES Envelope ad HLS. Gli eventuali protocolli non definiti nei criteri di distribuzione (ad esempio quando si aggiunge un singolo criterio che specifica soltanto HLS come protocollo) verranno esclusi dallo streaming. Questo comportamento non si verifica quando non è presente alcun criterio di distribuzione degli asset. In tal caso, sono consentiti tutti i protocolli in chiaro.

6. [Creare un localizzatore OnDemand](media-services-protect-with-aes128.md#create_locator) per ottenere un URL di streaming.

L'articolo inoltre indica [come un'applicazione client può richiedere una chiave dal servizio di distribuzione delle chiavi](media-services-protect-with-aes128.md#client_request).

Notare un [esempio](media-services-protect-with-aes128.md#example) .NET completo alla fine dell'articolo.

Nell'immagine seguente viene illustrato il flusso di lavoro descritto in precedenza. Di seguito viene utilizzato il token per l'autenticazione.

![Proteggere con AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Nella parte rimanente di questo articolo vengono fornite spiegazioni dettagliate, esempi di codice e collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.

## <a name="current-limitations"></a>Limitazioni correnti
Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare l'eventuale localizzatore esistente e creare un nuovo localizzatore.

## <a id="create_asset"></a>Creare un asset e caricare file nell'asset
Per gestire, codificare e trasmettere i video, è innanzitutto necessario caricare il contenuto in Servizi multimediali di Microsoft Azure. Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming. 

Per informazioni dettagliate, vedere [Carica file in un account di servizi multimediali](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificare l'asset contenente il file per il set di file MP4 con velocità in bit adattiva
Con la crittografia dinamica è necessario solamente creare un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantisce che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client. Per altre informazioni, vedere l'articolo [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 
>
>Per usare la creazione dinamica dei pacchetti e la crittografia dinamica, l'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva.

Per istruzioni su come eseguire la codifica, vedere [Come codificare un asset mediante Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Creare una chiave simmetrica e associarla all'asset codificato
In Servizi multimediali, la chiave simmetrica contiene la chiave con cui si desidera crittografare un asset.

Per informazioni dettagliate, vedere [Creare una chiave simmetrica](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave simmetrica.
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione della chiave simmetrica devono essere configurati dall'utente e soddisfatti dal client (lettore) affinché la chiave possa essere distribuita al client. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open, Token o IP.

Per informazioni dettagliate, vedere l'argomento [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configurare i criteri di distribuzione dell'asset
Configurare i criteri di distribuzione dell'asset. Alcuni aspetti inclusi nella configurazione dei criteri di distribuzione dell’asset:

* URL di acquisizione della chiave. 
* Vettore di inizializzazione (IV) da utilizzare per la crittografia envelope. AES 128 richiede che venga fornito lo stesso IV per la crittografia e la decrittografia. 
* Il protocollo di recapito dell'asset (ad esempio, MPEG DASH, HLS, Smooth Streaming o tutti).
* Il tipo di crittografia dinamica (ad esempio, envelope AES) o nessuna crittografia dinamica. 

Per informazioni dettagliate, vedere [Configurare i criteri di distribuzione degli asset ](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Creare un localizzatore di streaming on demand per ottenere un URL di streaming
È necessario fornire all'utente l'URL di streaming per Smooth, DASH o HLS.

> [!NOTE]
> Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare l'eventuale localizzatore esistente e creare un nuovo localizzatore.
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
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

È possibile utilizzare il [lettore AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per testare il flusso.

## <a id="client_request"></a>In che modo il client può richiedere una chiave dal servizio di distribuzione delle chiavi?
Nel passaggio precedente, è stato realizzato l'URL che punta a un file manifesto. Il client deve estrarre le informazioni necessarie dai file manifesto del flusso per effettuare una richiesta al servizio di distribuzione delle chiavi.

### <a name="manifest-files"></a>File manifesto
Il client deve estrarre il valore URL (che contiene anche l'ID della chiave simmetrica kid) dal file manifesto. Il client tenterà quindi di ottenere la chiave di crittografia dal servizio di distribuzione delle chiavi. Inoltre, il client deve estrarre il valore IV e usarlo per decrittografare il flusso. Il frammento di codice seguente illustra l'elemento <Protection> del manifesto Smooth Streaming.

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

Se si apre uno dei file del segmento nell'editor di testo (ad esempio, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), deve essere presente #EXT-X-KEY che indica che il file è crittografato.

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

Il codice seguente indica come inviare una richiesta al servizio di distribuzione delle chiavi di Servizi multimediali usando un Uri di distribuzione della chiave (che è stato estratto dal manifesto) e un token (in questo articolo non viene discusso come ottenere token Web semplici da un servizio Secure Token).

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

## <a name="protect-your-content-with-aes-128-using-net"></a>Proteggere i contenuti con AES-128 tramite .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 
2. Aggiungere i seguenti elementi alla sezione **appSettings** definita nel file app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Esempio

Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
 
>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) articolo.

Assicurarsi di aggiornare le variabili in modo da puntare alle cartelle in cui si trovano i file di input.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

