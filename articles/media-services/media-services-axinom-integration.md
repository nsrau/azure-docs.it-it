---
title: Uso di Axinom per distribuire licenze Widevine a Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze Axinom.
services: media-services
documentationcenter: 
author: willzhan
manager: SyntaxC4
editor: 
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c02b75dc2c783b96dc9e1256051b8c6df290d425
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Uso di Axinom per fornire licenze Widevine ai Servizi multimediali di Azure
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Panoramica
Servizi multimediali di Azure (AMS) ha aggiunto la protezione dinamica Google Widevine. Per altre informazioni, vedere il [blog di Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/). Azure Media Player (AMP) ha aggiunto anche il supporto per Widevine. Per informazioni dettagliate, vedere il [documento su AMP](http://amp.azure.net/libs/amp/latest/docs/). Ciò offre molti vantaggi per lo streaming di contenuto DASH protetto da CENC con DRM multi-native (PlayReady e Widevine) in browser moderni dotati di MSE ed EME.

A partire da Servizi Multimediali .NET SDK versione 3.5.2, Servizi multimediali consente di configurare il modello di licenza Widevine e ottenere licenze Widevine. È anche possibile usare i partner AMS seguenti per facilitare la distribuzione di licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

Questo articolo illustra come integrare e testare il server licenze Widevine gestito da Axinom. In particolare, illustra le operazioni seguenti:  

* Configurazione della Common Encryption dinamica con DRM multiplo (PlayReady e Widevine) con URL di acquisizione licenze corrispondenti.
* Generazione di un token JWT per soddisfare i requisiti del server licenze.
* Sviluppo di un'app Azure Media Player che gestisce l'acquisizione di licenze con l'autenticazione con token JWT.

Il sistema completo e il flusso di chiavi simmetriche, ID della chiave, semi chiave, token JTW e relative attestazioni può essere descritto in modo ottimale tramite il diagramma seguente.

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Protezione del contenuto
Per configurare la protezione dinamica e i criteri di distribuzione delle chiavi, vedere il blog di Mingfei relativo a [come configurare la creazione di pacchetti Widevine con Servizi multimediali di Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

È possibile configurare la protezione CENC dinamica con DRM multiplo per lo streaming DASH in modo che includa gli elementi seguenti:

1. Protezione PlayReady per Microsoft Edge e IE11, che potrebbero presentare restrizioni relative all'autorizzazione con token. I criteri con restrizione token devono essere associati a un token emesso da un servizio token di sicurezza, ad esempio Azure Active Directory.
2. Protezione Widevine per Chrome. Può richiedere l'autenticazione con token tramite token emessi da un altro servizio token di sicurezza. 

Per informazioni sui motivi per cui non è possibile usare Azure Active Directory come servizio token di sicurezza per il server licenze Widevine di Axinom, vedere [Generazione di token JWT](media-services-axinom-integration.md#jwt-token-generation) .

### <a name="considerations"></a>Considerazioni
1. È necessario usare il seme chiave specificato da Axinom (8888000000000000000000000000000000000000) e l'ID chiave generato o selezionato per generare la chiave simmetrica per la configurazione del servizio di distribuzione delle chiavi. Il server licenze Axinom emetterà tutte le licenze contenenti chiavi simmetriche basate sullo stesso seme chiave, valido per il test e la produzione.
2. URL di acquisizione di licenze Widevine per i test: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Sono consentiti sia HTTP che HTTS.

## <a name="azure-media-player-preparation"></a>Preparazione di Azure Media Player
Azure Media Player v1.4.0 supporta la riproduzione di contenuto AMS incluso dinamicamente in pacchetti con PlayReady e Widevine DRM.
Se il server licenze Widevine non richiede l'autenticazione tramite token, non sono necessarie altre operazioni per testare un contenuto DASH protetto da Widevine. Per un esempio, il team AMP fornisce un semplice [esempio](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), dove è possibile vederlo funzionante nel bordo e in IE11 con PlayReady e Chrome con Widevine.
Il server licenze Widevine fornito da Axinom richiede l'autenticazione tramite token JWT. Il token JWT deve essere inviato con la richiesta di licenza tramite un'intestazione HTTP "X-AxDRM-Message". Per questo scopo è necessario aggiungere il codice javascript seguente nella pagina Web che ospita AMP prima di configurare l'origine:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Il resto del codice di Azure Media Player è un'API di Azure Media Player standard, come illustrato in [questo](http://amp.azure.net/libs/amp/latest/docs/)documento su Azure Media Player.

Si noti che il codice javascript precedente per la configurazione dell'intestazione di autorizzazione personalizzata costituisce ancora un approccio a breve termine, prima del rilascio dell'approccio ufficiale a lungo termine in Azure Media Player.

## <a name="jwt-token-generation"></a>Generazione di token JWT
Il server licenze Axinom Widevine per il test richiede l'autenticazione tramite token JWT. Una delle attestazioni nel token JWT, inoltre, è di tipo oggetto complesso, invece di tipo di dati primitivi.

Sfortunatamente, Azure AD può emettere solo token JWT con tipi primitivi. Analogamente, l'API .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) consente solo di inserire tipi di oggetto complessi come attestazioni. Le attestazioni vengono tuttavia serializzate comunque come stringa. Non è quindi possibile usarle per generare il token JWT per la richiesta di licenza Widevine.

Il [pacchetto NuGet JWT](https://www.nuget.org/packages/JWT) di John Sheehan soddisfa le esigenze specifiche, quindi verrà usato.

Il codice seguente consente di generare il token JWT con le attestazioni necessarie, in base a quanto richiesto dal server licenze Axinom Widevine per i test:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Server licenze Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerazioni
1. Anche se il servizio di distribuzione licenze PlayReady dei Servizi multimediali di Azure richiede il prefisso "Bearer=" per il token di autenticazione, il server licenze Axinom Widevine non lo usa.
2. La chiave di comunicazione Axinom viene usata come chiave di firma. Si noti che la chiave è una stringa esadecimale, ma deve essere considerata come una serie di byte, non una stringa, durante la codifica. Questo risultato viene ottenuto tramite il metodo ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Recupero dell'ID chiave
Come si può notare, l'ID chiave è necessario nel codice per la generazione di un token JWT. Poiché il token JWT deve essere pronto prima di caricare il lettore di Azure Media Player, è necessario recuperare l'ID chiave per generare il token JWT.

È ovviamente possibile ottenere l'ID chiave in molti modi. Ad esempio, è possibile archiviare l'ID chiave insieme ai metadati dei contenuti in un database. In alternativa, è possibile recuperare l'ID chiave dal file DASH MPD (Media Presentation Description), usando il codice seguente.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Riepilogo
La recente aggiunta del supporto per Widevine nella protezione del contenuto di Servizi multimediali di Azure e in Azure Media Player consente di implementare lo streaming di DASH + DRM multi-native (PlayReady + Widevine) con il server licenze PlayReady nei Servizi multimediali di Azure e un server licenze Widevine di Axinom per i browser moderni seguenti:

* Chrome
* Microsoft Edge in Windows 10
* IE 11 in Windows 8.1 e Windows 10
* Anche Firefox (Desktop) e Safari on Mac (non iOS) sono supportati tramite Silverlight e lo stesso URL con Azure Media Player

I parametri seguenti sono necessari nella soluzione minima che sfrutta il server licenze Axinom Widevine. Ad eccezione dell'ID chiave, il resto dei parametri viene fornito da Axinom in base alla configurazione del server Widevine.

| Parametro | Modalità di utilizzo |
| --- | --- |
| ID chiave di comunicazione |Deve essere incluso come valore di attestazione "com_key_id" nel token JWT. Vedere [questa](media-services-axinom-integration.md#jwt-token-generation) sezione. |
| Chiave di comunicazione |Deve essere utilizzato come chiave di firma di token JWT (vedere [questa](media-services-axinom-integration.md#jwt-token-generation) sezione). |
| Seme chiave |Deve essere usato per generare la chiave simmetrica con qualsiasi ID della chiave simmetrica. Vedere [questa](media-services-axinom-integration.md#content-protection) sezione. |
| L'URL di acquisizione della licenza Widevine. |È necessario usare la configurazione dei criteri di recapito asset per il flusso DASH. Vedere [questa](media-services-axinom-integration.md#content-protection) sezione. |
| ID della chiave del contenuto |Deve essere incluso come parte del valore del reclamo del messaggio di attestazione del diritto di token JWT (vedere [questa](media-services-axinom-integration.md#jwt-token-generation) sezione). |

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Ringraziamenti
Microsoft è lieta di conferire un riconoscimento alle persone seguenti che hanno contribuito alla realizzazione di questo documento: Kristjan Jõgi di Axinom, Mingfei Yan e Amit Rajput.


