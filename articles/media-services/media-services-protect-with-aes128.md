---
title: Utilizzare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi | Documenti Microsoft
description: Distribuire il contenuto crittografato con chiavi di crittografia AES a 128 bit con servizi multimediali di Microsoft Azure. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. In questo argomento viene illustrato come crittografare con AES-128 e utilizzare il servizio di distribuzione delle chiavi in modo dinamico.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Utilizzare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Per ottenere la versione più recente di Java SDK e introduzione allo sviluppo con Java, vedere [iniziare con il client di Java SDK per servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Per scaricare il SDK più recente di PHP per servizi multimediali, cercare versione 0.5.7 del pacchetto Microsoft/Azure nel [repository Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Panoramica
> [!NOTE]
> Per informazioni su come crittografare contenuto con la crittografia AES (Advanced Standard) per il recapito a Safari macOS, vedere [questo post di blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> Per una panoramica di come proteggere i contenuti multimediali con crittografia AES, vedere [questo video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 Servizi multimediali consente di fornire HTTP Live Streaming (HLS) e Smooth Streaming crittografato con AES usando le chiavi di crittografia a 128 bit. Servizi multimediali fornisce anche il servizio di distribuzione delle chiavi che distribuisce chiavi di crittografia agli utenti autorizzati. Se si desidera servizi multimediali di crittografare un asset, associare una chiave di crittografia all'asset e inoltre configurare criteri di autorizzazione per la chiave. Quando un flusso è richiesto da un lettore, servizi multimediali Usa la chiave specificata per crittografare dinamicamente il contenuto mediante la crittografia AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione per la chiave specificata.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione chiave del contenuto possono avere uno o più restrizioni, le restrizioni aperte o token. I criteri di token con restrizioni devono essere accompagnato da un token rilasciato da un servizio token di sicurezza (STS). Servizi multimediali supporta i token nel [token web semplice](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formati (JWT). Per ulteriori informazioni, vedere [configurare criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy).

Per sfruttare la crittografia dinamica, è necessario disporre di un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. È inoltre necessario configurare i criteri di distribuzione dell'asset (descritti più avanti in questo articolo). Quindi, il server di flusso su richiesta in base al formato specificato nell'URL di streaming, assicura che il flusso venga distribuito nel protocollo prescelto. Di conseguenza, è necessario archiviare e pagare solo per i file in formato di archiviazione singolo. Servizi multimediali compila e viene utilizzata la risposta appropriata in base alle richieste da un client.

In questo articolo è utile per gli sviluppatori che lavorano per le applicazioni che distribuiscono contenuti multimediali protetti. L'articolo viene illustrato come configurare il servizio di distribuzione delle chiavi con criteri di autorizzazione in modo che solo i client autorizzati possano ricevere le chiavi di crittografia. Viene inoltre illustrato come utilizzare la crittografia dinamica.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Crittografia dinamica AES-128 e flusso di lavoro di distribuzione delle chiavi del servizio

Quando si sceglie di crittografare gli asset con AES utilizzando il servizio di distribuzione delle chiavi di servizi multimediali e la crittografia dinamica, eseguire i passaggi generali seguenti:

1. [Creare un asset e caricare i file nell'asset](media-services-protect-with-aes128.md#create_asset).

2. [Codificare l'asset che contiene il file a velocità in bit adattiva set MP4](media-services-protect-with-aes128.md#encode_asset).

3. [Creare una chiave simmetrica e associarlo all'asset codificato](media-services-protect-with-aes128.md#create_contentkey). In servizi multimediali, la chiave simmetrica contiene la chiave di crittografia dell'asset.

4. [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-protect-with-aes128.md#configure_key_auth_policy). È necessario configurare i criteri di autorizzazione chiave del contenuto. Il client deve soddisfare i criteri prima che la chiave simmetrica viene recapitata al client.

5. [Configurare i criteri di distribuzione di un asset](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configurazione di criteri di distribuzione include l'URL di acquisizione chiave e un vettore di inizializzazione (IV). (AES-128 richiede lo stesso IV per la crittografia e decrittografia). La configurazione include inoltre il protocollo di recapito (ad esempio, MPEG-DASH, HLS, Smooth Streaming o tutti) e il tipo di crittografia dinamica (ad esempio, envelope o Nessuna crittografia dinamica).

    È possibile applicare criteri diversi per ogni protocollo in uno stesso asset. Ad esempio, è possibile applicare la crittografia PlayReady a Smooth/DASH e una busta AES a HLS. Tutti i protocolli che non sono definiti in un criterio di recapito vengono bloccati dal flusso. (Un esempio è se si aggiunge un singolo criterio che specifica solo HLS come protocollo). L'eccezione è se non sono presenti criteri di recapito di asset definito in alcun modo. In tal caso, sono consentiti tutti i protocolli in chiaro.

6. [Creare un localizzatore OnDemand](media-services-protect-with-aes128.md#create_locator) per ottenere un URL di streaming.

L'articolo inoltre indica [come un'applicazione client può richiedere una chiave dal servizio di distribuzione delle chiavi](media-services-protect-with-aes128.md#client_request).

È possibile trovare completa [esempio .NET](media-services-protect-with-aes128.md#example) alla fine dell'articolo.

Nella figura seguente viene illustrato il flusso di lavoro descritto in precedenza. In questo caso, il token viene usato per l'autenticazione.

![Proteggere con AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Il resto di questo articolo vengono fornite spiegazioni, esempi di codice e collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.

## <a name="current-limitations"></a>Limitazioni correnti
Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare qualsiasi indicatore di posizione esistente e crearne uno nuovo.

## <a id="create_asset"></a>Creare un asset e caricare file nell'asset
Per gestire, codificare e trasmettere in flusso i video, è innanzitutto necessario caricare il contenuto in servizi multimediali. Una volta caricato, il contenuto è archiviato in modo sicuro nel cloud per un'ulteriore elaborazione e il flusso. 

Per ulteriori informazioni, vedere [caricare file in un account di servizi multimediali](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificare l'asset che contiene il file a velocità in bit adattiva che set MP4
Con la crittografia dinamica, per creare un asset che contiene un set di file MP4 a più velocità in bit o file di origine Smooth Streaming con più velocità in bit. Quindi, il server di flusso su richiesta in base al formato specificato nella richiesta del manifesto o del frammento, assicura che si ricevano il flusso del protocollo selezionato. Quindi, è sufficiente archiviare e pagare i file in formato di archiviazione singolo. Servizi multimediali compila e viene utilizzata la risposta appropriata in base alle richieste da un client. Per ulteriori informazioni, vedere [Cenni preliminari sulla creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Quando viene creato l'account di servizi multimediali, endpoint di streaming predefinito viene aggiunto al tuo account nello stato "Stopped". Per avviare lo streaming del contenuto e sfruttare i vantaggi di creazione dinamica dei pacchetti e la crittografia dinamica, l'endpoint di streaming da cui si desidera il contenuto di flusso deve essere nello stato "Running". 
>
>Inoltre, per l'utilizzo di creazione dinamica dei pacchetti e la crittografia dinamica, l'asset deve contenere un set di velocità in bit adattiva MP4s o file Smooth Streaming a velocità in bit adattiva.

Per istruzioni su come codificare, vedere [codificare un asset con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Creare una chiave simmetrica e associarla all'asset codificato
In Servizi multimediali, la chiave simmetrica contiene la chiave con cui si desidera crittografare un asset.

Per ulteriori informazioni, vedere [creare una chiave simmetrica](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave simmetrica
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. È necessario configurare i criteri di autorizzazione chiave del contenuto. Il client (lettore) deve soddisfare i criteri prima che la chiave può essere recapitata al client. I criteri di autorizzazione chiave del contenuto possono avere una o più restrizioni di autorizzazione, di aprire, token o restrizioni IP.

Per ulteriori informazioni, vedere [configurare un criterio di autorizzazione chiave del contenuto](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configurare un criterio di recapito di asset
Configurare i criteri di distribuzione dell'asset. Le operazioni che include la configurazione del criterio di recapito di asset sono:

* L'URL di acquisizione chiave. 
* Il vettore di inizializzazione (IV) da utilizzare per la crittografia envelope. AES-128 richiede lo stesso IV per la crittografia e decrittografia. 
* Protocollo di recapito di asset (ad esempio, MPEG-DASH, HLS, Smooth Streaming o tutti).
* Il tipo di crittografia dinamica (ad esempio, envelope AES) o nessuna crittografia dinamica. 

Per ulteriori informazioni, vedere [configurare un criterio di recapito di asset](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Creare un OnDemand streaming localizzatore per ottenere un URL di streaming
È necessario fornire all'utente l'URL di streaming per Smooth Streaming, DASH o HLS.

> [!NOTE]
> Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare qualsiasi indicatore di posizione esistente e crearne uno nuovo.
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

È possibile utilizzare il [lettore servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per testare il flusso.

## <a id="client_request"></a>In che modo il client può richiedere una chiave dal servizio di distribuzione delle chiavi?
Nel passaggio precedente, è stato realizzato l'URL che punta a un file manifesto. Il client deve estrarre le informazioni necessarie dal file manifesto dello streaming per effettuare una richiesta al servizio di distribuzione delle chiavi.

### <a name="manifest-files"></a>File manifesto
Il client deve estrarre l'URL (che include anche contenuto [kid] ID della chiave) valore dal file manifesto. Il client tenta quindi di ottenere la chiave di crittografia dal servizio di distribuzione delle chiavi. Inoltre, il client deve estrarre il valore IV e usarlo per decrittografare il flusso. Il frammento di codice seguente viene illustrato il <Protection> elemento del manifesto Smooth Streaming:

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

Ad esempio, il manifesto radice è: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Contiene un elenco di nomi di file di segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se si apre uno dei file di segmenti in un editor di testo (ad esempio, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it contains # EXT-X-KEY, che indica che il file è crittografato.

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
>Se si prevede di riprodurre un HLS crittografato con AES in Safari, vedere [questo blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Richiedere la chiave dal servizio di distribuzione delle chiavi

Il codice seguente viene illustrato come inviare una richiesta per il servizio di distribuzione delle chiavi di servizi multimediali usando un Uri (che è stato estratto dal manifesto) di recapito e un token. (In questo articolo non viene illustrato come ottenere i token SWT da un servizio token di sicurezza).

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteggere i contenuti tramite AES-128 usando .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Configurare l'ambiente di sviluppo e popolare il file app. config con informazioni di connessione, come descritto in [lo sviluppo di servizi multimediali con .NET](media-services-dotnet-how-to-use.md).

2. Aggiungere i seguenti elementi per appSettings, come definito nel file app. config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Esempio

Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
 
>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri di Servizi multimediali (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Se si utilizzano sempre le stesse autorizzazioni giorni/accesso, utilizzare lo stesso ID di criteri. Un esempio è criteri per i localizzatori che devono rimanere sul posto per un lungo periodo (non-caricamento criteri). Per ulteriori informazioni, vedere la sezione "Criteri di accesso limite" in [gestire risorse e le relative entità con Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Assicurarsi di aggiornare le variabili in modo da puntare alle cartelle in cui si trovano i file di input.

    [!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

