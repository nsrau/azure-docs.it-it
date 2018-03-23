---
title: Configurare i criteri di distribuzione degli asset con .NET SDK | Microsoft Docs
description: Questo argomento illustra come configurare criteri di distribuzione degli asset differenti con Servizi multimediali di Azure .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: 8fe78d2d8cef1ee5484bcdcd83b00afe8e5b30ce
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurare i criteri di distribuzione degli asset con .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Panoramica
Se si prevede di distribuire dinamicamente asset crittografati in modo dinamico, uno dei passaggi del flusso di lavoro di distribuzione dei contenuti in Servizi multimediali consiste nella configurazione dei criteri di distribuzione degli asset. Questi criteri indicano a Servizi multimediali la modalità di distribuzione di un asset, ovvero il protocollo di streaming da usare per la creazione dinamica dei pacchetti (ad esempio, MPEG DASH, HLS, Smooth Streaming o tutti), se l'asset deve essere crittografato dinamicamente e l'eventuale modalità di crittografia (envelope o common).

Questo articolo illustra perché e come creare i criteri di distribuzione degli asset.

>[!NOTE]
>Quando l'account AMS viene creato, all'account viene aggiunto un endpoint di streaming **predefinito** con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 
>
>Per usare la creazione dinamica dei pacchetti e la crittografia dinamica, l'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva.

È possibile applicare criteri differenti allo stesso asset. È ad esempio possibile applicare la crittografia PlayReady a Smooth Streaming e la crittografia envelope AES (Advanced Encryption Standard) a MPEG DASH e HLS. Gli eventuali protocolli non definiti nei criteri di distribuzione (ad esempio quando si aggiunge un singolo criterio che specifica soltanto HLS come protocollo) verranno esclusi dallo streaming. Questo comportamento non si verifica quando non sono definiti criteri di distribuzione degli asset. In tal caso, sono consentiti tutti i protocolli in chiaro.

Se si desidera distribuire un asset con memoria crittografata, è necessario configurare i criteri di distribuzione appropriati. Prima di trasmettere in streaming l'asset in base ai criteri specificati, il server rimuove la crittografia di archiviazione. Ad esempio, per distribuire l'asset crittografato con una chiave di crittografia envelope AES (Advanced Encryption Standard), impostare il tipo di criteri su **DynamicEnvelopeEncryption**. Per rimuovere la crittografia di archiviazione e trasmettere l'asset in chiaro, impostare il tipo di criteri su **NoDynamicEncryption**. I seguenti esempi mostrano come configurare questi tipi di criteri.

A seconda della modalità di configurazione dei criteri di distribuzione degli asset, sarà possibile creare dinamicamente i pacchetti, applicare la crittografia e trasmettere i protocolli di streaming seguenti: Smooth Streaming, HLS e MPEG DASH.

L'elenco seguente mostra i formati usati per i flussi Smooth, HLS e DASH.

Smooth Streaming:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest

HLS:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Considerazioni
* Prima di eliminare l'oggetto AssetDeliveryPolicy, è necessario eliminare tutti i localizzatori di streaming associati all'asset. Sarà possibile creare nuovi localizzatori di streaming in un secondo momento, se lo si desidera, con un nuovo oggetto AssetDeliveryPolicy.
* Non è possibile creare un localizzatore di streaming in un asset crittografato per l’archiviazione se non è impostato alcun criterio di distribuzione degli asset.  Se l'asset non è crittografato per l'archiviazione, il sistema consentirà di creare un localizzatore ed eseguire in streaming l'asset in chiaro senza un criterio di distribuzione degli asset.
* È possibile avere più criteri di distribuzione degli asset associati a un singolo asset, ma è possibile specificare solo un modo per gestire un determinato AssetDeliveryProtocol.  Ciò significa che se si tenta di collegare due criteri di distribuzione che specificano il protocollo AssetDeliveryProtocol.SmoothStreaming, verrà generato un errore perché il sistema non sa quale applicare quando un client effettua una richiesta di Smooth Streaming.
* Se si dispone di un asset con un localizzatore di streaming esistente, non è possibile collegare un nuovo criterio all'asset (è possibile scollegare un criterio esistente dall'asset, o aggiornare un criterio di distribuzione associato all'asset).  È innanzitutto necessario rimuovere il localizzatore di streaming, modificare i criteri e quindi creare nuovamente il localizzatore di streaming.  È possibile utilizzare lo stesso ID quando si ricrea il localizzatore di streaming, ma è necessario assicurarsi che questo non causi problemi per i client poiché il contenuto può essere memorizzato nella cache per l'origine o una rete CDN a valle.

## <a name="clear-asset-delivery-policy"></a>Criteri di distribuzione degli asset Clear

Il metodo **ConfigureClearAssetDeliveryPolicy** seguente specifica di non applicare la crittografia dinamica e di distribuire il flusso con uno dei protocolli seguenti: MPEG DASH, HLS e Smooth Streaming. È possibile applicare questo criterio alle risorse crittografate di archiviazione.

Per informazioni sui valori che è possibile specificare quando si crea un oggetto AssetDeliveryPolicy, vedere la sezione [Tipi usati durante la definizione di AssetDeliveryPolicy](#types) .

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Criteri di distribuzione degli asset DynamicCommonEncryption

Il metodo **CreateAssetDeliveryPolicy** seguente crea l'oggetto **AssetDeliveryPolicy** configurato in modo da applicare la crittografia dinamica di tipo common (**DynamicCommonEncryption**) a un protocollo Smooth Streaming (gli altri protocolli vengono esclusi dallo streaming). Il metodo accetta due parametri: **Asset**, cioè l'asset a cui applicare i criteri di distribuzione, e **IContentKey**, cioè la chiave simmetrica del tipo **CommonEncryption**. Per altre informazioni, vedere l'articolo relativo alla [creazione di una chiave simmetrica](media-services-dotnet-create-contentkey.md#common_contentkey).

Per informazioni sui valori che è possibile specificare quando si crea un oggetto AssetDeliveryPolicy, vedere la sezione [Tipi usati durante la definizione di AssetDeliveryPolicy](#types) .

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Servizi multimediali di Azure consente inoltre di aggiungere la crittografia Widevine. Nell'esempio seguente viene illustrato sia PlayReady che Widevine da aggiungere al criterio di recapito di asset.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Durante la crittografia con Widevine, si sarebbe in grado di recapitare utilizzando DASH. Assicurarsi di specificare il protocollo di recapito asset DASH.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Criteri di distribuzione degli asset DynamicEnvelopeEncryption
Il metodo **CreateAssetDeliveryPolicy** seguente crea l'oggetto **AssetDeliveryPolicy** configurato in modo da applicare la crittografia envelope dinamica (**DynamicEnvelopeEncryption**) ai protocolli Smooth Streaming, HLS e DASH (se si decide di non specificare alcuni protocolli, questi saranno esclusi dallo streaming). Il metodo accetta due parametri: **Asset**, cioè l'asset a cui applicare i criteri di distribuzione, e **IContentKey**, cioè la chiave simmetrica del tipo **EnvelopeEncryption**. Per altre informazioni, vedere l'articolo relativo alla [creazione di una chiave simmetrica](media-services-dotnet-create-contentkey.md#envelope_contentkey).

Per informazioni sui valori che è possibile specificare quando si crea un oggetto AssetDeliveryPolicy, vedere la sezione [Tipi usati durante la definizione di AssetDeliveryPolicy](#types) .   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

L'enumerazione seguente descrive i valori che è possibile impostare per il protocollo di recapito di risorse.

```csharp
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }
```
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

L'enumerazione seguente descrive i valori che è possibile impostare per il tipo di criterio di recapito.  
```csharp
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }
```
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

L'enumerazione seguente descrive i valori che è possibile usare per configurare il metodo di recapito della chiave simmetrica al client.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

L'enumerazione seguente descrive i valori che è possibile impostare per configurare le chiavi usate per ottenere una configurazione specifica per un criterio di recapito di risorse.
```csharp
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }
```
## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

