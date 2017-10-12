---
title: Proteggere il contenuto HLS con Microsoft PlayReady o con Apple FairPlay | Documentazione Microsoft
description: Questo argomento offre una panoramica su come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HTTP Live Streaming (HLS) con Apple FairPlay. Viene anche illustrato come usare il servizio di distribuzione delle licenze di Servizi multimediali per distribuire le licenze FairPlay ai client.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 895d6307b1cef74e195cc2ffd8dbef4196e97b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteggere il contenuto HLS con Apple FairPlay o Microsoft PlayReady
Servizi multimediali di Azure consente di crittografare dinamicamente il contenuto di HTTP Live Streaming (HLS) usando i formati seguenti:  

* **Chiave envelope non crittografata AES-128**

    L'intero blocco viene crittografato usando la modalità **AES-128 CBC**. La decrittografia del flusso è supportata dai lettori iOS e OSX in modo nativo. Per altre informazioni, vedere [Uso della crittografia dinamica AES-128 e del servizio di distribuzione delle chiavi](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    I singoli campioni audio e video vengono crittografati con la modalità **AES-128 CBC**. **FairPlay Streaming** (FPS) è integrato nei sistemi operativi dei dispositivi, con supporto nativo per iOS e Apple TV. Safari su OS X abilita FPS con il supporto dell'interfaccia EME (Encrypted Media Extensions).
* **Microsoft PlayReady**

L'immagine seguente illustra il flusso di lavoro della **crittografia dinamica HLS + FairPlay o PlayReady**.

![Diagramma del flusso di lavoro della crittografia dinamica](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Questo argomento illustra come usare Servizi multimediali per crittografare dinamicamente il contenuto HLS con Apple FairPlay. Viene anche illustrato come usare il servizio di distribuzione delle licenze di Servizi multimediali per distribuire le licenze FairPlay ai client.

> [!NOTE]
> Se si vuole crittografare anche il contenuto HLS con PlayReady, è necessario creare una chiave di contenuto comune e associarla all'asset. È anche necessario configurare i criteri di autorizzazione della chiave simmetrica, come descritto in [Uso della crittografia comune dinamica PlayReady](media-services-protect-with-drm.md).
>
>

## <a name="requirements-and-considerations"></a>Problemi e considerazioni

Se si usa Servizi multimediali per distribuire contenuto HLS crittografato con FairPlay e per distribuire licenze FairPlay, è necessario:

  * Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Account di Servizi multimediali. Per crearne uno, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
  * Eseguire l'iscrizione all' [Apple Development Program](https://developer.apple.com/).
  * Apple richiede che il proprietario del contenuto ottenga il [pacchetto di distribuzione](https://developer.apple.com/contact/fps/). Indicare che è già stato implementato il modulo KSM (Key Security Module) con Servizi multimediali e che si sta richiedendo il pacchetto FPS finale. Il pacchetto FPS finale contiene istruzioni per generare la certificazione e ottenere la chiave privata dell'applicazione, che verrà usata per configurare FairPlay.
  * Azure Media Services .NET SDK versione **3.6.0** o successiva.

È necessario impostare quanto segue in aggiunta alla distribuzione delle chiavi di Servizi multimediali:

  * **App Cert (AC)**: file con estensione pfx contenente la chiave privata. Creare il file e crittografarlo con una password.

       Quando si configurano i criteri di distribuzione delle chiavi, è necessario specificare la password e il file pfx in formato Base64.

      La procedura seguente descrive come generare un file di certificato pfx per FairPlay:

    1. Installare OpenSSL da https://slproweb.com/products/Win32OpenSSL.html.

        Passare alla cartella contenente il certificato FairPlay e altri file forniti da Apple.
    2. Eseguire il comando seguente dalla riga di comando. Il comando converte il file con estensione cer in un file con estensione pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
    3. Eseguire il comando seguente dalla riga di comando. Questo comando converte il file con estensione pem in un file con estensione pfx con la chiave privata. La password per il file con estensione pfx viene quindi richiesta da OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
  * **App Cert password**: password del cliente per creare il file con estensione pfx.
  * **App Cert password ID**: è necessario caricare la password con una procedura simile a quella usata per caricare le altre chiavi di Servizi multimediali. Usare il valore di enumerazione **ContentKeyType.FairPlayPfxPassword** per ottenere l'ID di Servizi multimediali. Questo valore è necessario nell'opzione dei criteri di distribuzione delle chiavi.
  * **iv**: valore casuale di 16 byte che deve corrispondere al valore iv nei criteri di distribuzione dell'asset. Si genera l'iv e lo inserisce sia nei criteri di distribuzione dell'asset che nell'opzione dei criteri di distribuzione delle chiavi.
  * **ASK**: chiave ricevuta quando si genera la certificazione usando il portale Apple Developer. Ogni team di sviluppo riceve una chiave ASK univoca. Salvare una copia della chiave ASK e archiviarla in un luogo sicuro. Successivamente sarà necessario configurare la chiave ASK come FairPlayAsk in Servizi multimediali.
  * **ID ASK**: ID ottenuto quando si carica la chiave privata dell'applicazione in Servizi multimediali. È necessario caricare la chiave privata dell'applicazione usando il valore di enumerazione **ContentKeyType.FairPlayASk**. Verrà restituito l'ID di Servizi multimediali che dovrà essere usato per impostare l'opzione dei criteri di distribuzione delle chiavi.

Sul lato client FPS è necessario impostare quanto segue:

  * **App Cert (AC)**: file con estensione cer/der contenente la chiave pubblica usata dal sistema operativo per crittografare alcuni payload. È necessario che Servizi multimediali lo riconosca perché è richiesto dal lettore. Il servizio di distribuzione delle chiavi lo decrittografa usando la chiave privata corrispondente.

Per riprodurre un flusso crittografato FairPlay, ottenere prima una chiave privata dell'applicazione reale, quindi generare un certificato reale. Questo processo crea tutte le 3 parti:

  * file con estensione der
  * file con estensione pfx
  * password per il file pfx

I client seguenti supportano il formato HLS con la crittografia **AES-128 CBC**: Safari in OS X, Apple TV e iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configurare la crittografia dinamica FairPlay e i servizi di distribuzione delle licenze
Di seguito sono indicati i passaggi generali per la protezione degli asset con FairPlay usando il servizio di distribuzione delle licenze di Servizi multimediali e la crittografia dinamica.

1. Creare un asset e caricare file al suo interno.
2. Codificare l'asset contenente il file nel set MP4 con velocità in bit adattiva.
3. Creare una chiave simmetrica e associarla all'asset codificato.  
4. Configurare i criteri di autorizzazione della chiave simmetrica. Specificare quanto segue:

   * metodo di distribuzione, in questo caso FairPlay,
   * configurazione delle opzioni dei criteri FairPlay. Per informazioni dettagliate sulla configurazione di FairPlay, vedere il metodo **ConfigureFairPlayPolicyOptions()** nell'esempio seguente.

     > [!NOTE]
     > In genere è opportuno configurare le opzioni dei criteri FairPlay una sola volta, dato che sarà presente un solo set di certificazione e chiave privata dell'applicazione.
     >
     >
   * Restrizioni aperte o token.
   * Informazioni specifiche per il tipo di distribuzione delle chiavi che definiscono la modalità di distribuzione della chiave al client.
5. Configurare i criteri di distribuzione dell'asset. La configurazione dei criteri di distribuzione include:

   * Il protocollo di recapito (HLS).
   * Il tipo di crittografia dinamica (crittografia CBC comune).
   * L'URL di acquisizione delle licenze.

     > [!NOTE]
     > Per distribuire un flusso crittografato con FairPlay e un altro sistema Digital Rights Management (DRM), è necessario configurare criteri di distribuzione separati:
     >
     > * Una norma IAssetDeliveryPolicy per configurare Dynamic Adaptive Streaming over HTTP (DASH) con Common Encryption (CENC) (PlayReady e Widevine) e Smooth con PlayReady
     > * Un altro criterio IAssetDeliveryPolicy per configurare FairPlay per HLS
     >
     >
6. Creare un localizzatore OnDemand per ottenere un URL di streaming.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Usare la distribuzione delle chiavi FairPlay con applicazioni lettore
È possibile sviluppare applicazioni lettore usando l'SDK per iOS. Per riprodurre contenuto FairPlay, è necessario implementare il protocollo di scambio delle licenze. Questo protocollo non è specificato da Apple. Spetta a ogni app scegliere come inviare le richieste di distribuzione delle chiavi. Il servizio di distribuzione delle chiavi FairPlay di Servizi multimediali prevede che SPC venga indicato in un messaggio codificato come www-form-url nel formato seguente:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Per impostazione predefinita, Azure Media Player non supporta la riproduzione FairPlay. Per poter eseguire la riproduzione FairPlay in MAC OS X, ottenere il lettore di esempio dall'account per sviluppatori di Apple.
>
>

## <a name="streaming-urls"></a>URL di streaming
Se l'asset è stato crittografato con più soluzioni DRM, è necessario usare un tag di crittografia nell'URL di streaming (format='m3u8-aapl', encryption='xxx').

Si applicano le considerazioni seguenti:

* Può essere specificato solo un tipo di crittografia oppure nessuno.
* Il tipo di crittografia non deve essere specificato nell'URL se all'asset è stata applicata una sola crittografia.
* Il tipo di crittografia non fa distinzione tra maiuscole e minuscole.
* Possono essere specificati i seguenti tipi di crittografia:  
  * **cenc**: crittografia comune (PlayReady o Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: crittografia busta AES

## <a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto di Visual Studio

1. Configurare l'ambiente di sviluppo e popolare il file app.config con le informazioni di connessione, come descritto in [Sviluppo di applicazioni di Servizi multimediali con .NET](media-services-dotnet-how-to-use.md). 
2. Aggiungere gli elementi seguenti alla sezione **appSettings** definita nel file app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

## <a name="example"></a>Esempio

L'esempio seguente illustra la funzionalità che consente di usare Servizi multimediali per distribuire contenuto crittografato con FairPlay. Questa funzionalità è stata introdotta in Azure Media Services SDK for .NET, versione 3.6.0. 

Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.

>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) argomento.

Assicurarsi di aggiornare le variabili in modo da puntare alle cartelle in cui si trovano i file di input.

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
    using Newtonsoft.Json;
    using System.Security.Cryptography.X509Certificates;

    namespace DynamicEncryptionWithFairPlay
    {
        class Program
        {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
            tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
            AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
            // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
            // back into a TokenRestrictionTemplate class instance.
            TokenRestrictionTemplate tokenTemplate =
                TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

            // Generate a test token based on the the data in the given TokenRestrictionTemplate.
            // Note, you need to pass the key id Guid because we specified
            // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
            Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
            string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                        DateTime.UtcNow.AddDays(365));
            Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
            Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
            Console.WriteLine("File does not exist.");
            return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
            rng.GetBytes(returnValue);
            }

            return returnValue;
        }
        }
    }


## <a name="next-steps-media-services-learning-paths"></a>Passaggi successivi: Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
