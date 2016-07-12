<properties 
	pageTitle="Usare Servizi multimediali di Azure per trasmettere il contenuto HLS in modo protetto con Apple FairPlay" 
	description="Questo argomento offre una panoramica su come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HTTP Live Streaming (HLS) con Apple FairPlay. Viene anche illustrato come usare il servizio di distribuzione delle licenze di Servizi multimediali per distribuire le licenze FairPlay ai client." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016"
	ms.author="juliako"/>

#Usare Servizi multimediali di Azure per trasmettere il contenuto HLS in modo protetto con Apple FairPlay 

Servizi multimediali di Azure consente di crittografare dinamicamente i contenuti di HTTP Live Streaming (HLS) usando i formati seguenti:

- **AES-128 envelope clear key**: l'intero blocco viene crittografato usando la modalità **AES-128 CBC**. La decrittografia del flusso è supportata dai lettori iOS e OSX in modo nativo. Per altre informazioni, vedere [questo articolo](media-services-protect-with-aes128.md).

- **Apple FairPlay**: i singoli campioni audio e video vengono crittografati con la modalità **AES-128 CBC**. **FairPlay Streaming** (FPS) è integrato nei sistemi operativi dei dispositivi, con supporto nativo per iOS e Apple TV. Safari su OS X abilita FPS con il supporto dell'interfaccia EME (Encrypted Media Extensions).

	>[AZURE.NOTE]
	L'uso di AMS per distribuire HLS crittografato con FairPlay è attualmente in anteprima.


L'immagine seguente illustra il flusso di lavoro "crittografia dinamica FairPlay".

![Proteggere con FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Questo argomento illustra come usare Servizi multimediali di Azure per crittografare dinamicamente il contenuto HLS con Apple FairPlay. Viene anche illustrato come usare il servizio di distribuzione delle licenze di Servizi multimediali per distribuire le licenze FairPlay ai client.

	
## Problemi e considerazioni

- Se si usa AMS per distribuire contenuti HLS crittografati con FairPlay e licenze FairPlay, è necessario:

	- Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
	- Account di Servizi multimediali. Per creare un account di Servizi multimediali, vedere l'argomento relativo alla [creazione di un account](media-services-create-account.md).
	- Effettuare l'iscrizione all'[Apple Development Program](https://developer.apple.com/).
	- Apple richiede che il proprietario del contenuto ottenga il [pacchetto di distribuzione](https://developer.apple.com/contact/fps/). Nella richiesta indicare che il KSM (Key Security Module) è già stato implementato con Servizi multimediali di Azure e che si richiede il pacchetto FPS finale. Nel pacchetto FPS finale saranno presenti istruzioni per generare la certificazione e ottenere la chiave privata dell'applicazione (ASK) che verrà usata per configurare FairPlay.

	- Azure Media Services SDK per .NET versione **3.6.0** o successiva.

- È necessario impostare quanto segue sul lato della distribuzione delle chiavi AMS:
	- **App Cert (AC)**: file con estensione pfx che contiene la chiave privata. Questo file viene creato dal cliente e crittografato con una password di sua scelta.
		
	 	Quando l'utente configura i criteri di distribuzione delle chiavi, deve specificare la password e il file PFX in formato Base64.

		La procedura seguente descrive come generare un certificato pfx per FairPlay.
		
		1. Installare OpenSSL da https://slproweb.com/products/Win32OpenSSL.html
		
			Passare alla cartella contenente il certificato FairPlay e altri file forniti da Apple.
		
		2. Riga di comando per convertire il file con estensione cer in un file con estensione pem:
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
		
		3. Riga di comando per convertire il file con estensione pem in un file con estensione pfx con la chiave privata (la password per il file pfx viene quindi richiesto da OpenSSL).
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
		
	- **App Cert password**: password del cliente per creare il file con estensione pfx.
	- **App Cert password ID**: il cliente deve caricare la password con una procedura simile a quella per il caricamento delle altre chiavi di AMS e usando il valore di enumerazione **ContentKeyType.FairPlayPfxPassword**. Nel risultato viene visualizzato l'ID AMS da usare nell'opzione dei criteri di distribuzione delle chiavi.
	- **iv**: valore casuale da 16 byte che deve corrispondere a iv nei criteri di distribuzione dell'asset. Il cliente genera IV e lo inserisce sia nei criteri di distribuzione dell'asset che nell'opzione dei criteri di distribuzione delle chiavi.
	- **ASK**: la chiave privata dell'applicazione (Application Secret Key, ASK) viene ricevuta quando si genera la certificazione usando il portale Apple Developer. Ogni team di sviluppo riceve una chiave ASK univoca. Salvare una copia della chiave ASK e archiviarla in un luogo sicuro. Successivamente sarà necessario configurare ASK come FairPlayAsk in Servizi multimediali di Azure.
	-  **ID ASK**: si ottiene quando il cliente carica la chiave privata dell'applicazione in AMS. Il cliente deve caricare la chiave privata dell'applicazione usando il valore di enumerazione **ContentKeyType.FairPlayASk**. Verrà restituito l'ID AMS che dovrà essere usato per impostare l'opzione dei criteri di distribuzione delle chiavi.

- Sul lato client FPS è necessario impostare quanto segue:
 	- **App Cert (AC)**: file con estensione cer/der che contiene la chiave pubblica usata dal sistema operativo per crittografare alcuni payload. È necessario che AMS lo riconosca perché è richiesto dal lettore. Il servizio di distribuzione delle chiavi lo decrittografa usando la chiave privata corrispondente.

- Per riprodurre un flusso crittografato FairPlay, è necessario ottenere prima la chiave ASK reale, quindi generare un certificato reale. Questo processo crea tutte e tre le parti:

	-  DER,
	-  PFX e
	-  la password per PFX.
 
- Client che supportano il formato HLS con crittografia **AES-128 CBC**: Safari in OS X, Apple TV e iOS.

##Passaggi per la configurazione della crittografia dinamica FairPlay e dei servizi di distribuzione delle licenze

Di seguito sono indicati i passaggi generali da eseguire quando si proteggono gli asset con FairPlay usando il servizio di distribuzione delle licenze di Servizi multimediali e la crittografia dinamica.

1. Creare un asset e caricare file al suo interno.
1. Codificare l'asset contenente il file per il set di file MP4 con velocità in bit adattiva.
1. Creare una chiave simmetrica e associarla all'asset codificato.
1. Configurare i criteri di autorizzazione della chiave simmetrica. Quando si creano i criteri di autorizzazione chiave del contenuto, è necessario specificare quanto segue:
	
	- metodo di distribuzione (in questo caso FairPlay),
	- configurazione delle opzioni dei criteri FairPlay. Per informazioni dettagliate sulla configurazione di FairPlay, vedere il metodo ConfigureFairPlayPolicyOptions() seguente.
	
		>[AZURE.NOTE] Nella maggior parte dei casi sarà opportuno configurare le opzioni dei criteri FairPlay una sola volta, dato che sarà presente un solo set di certificazione e ASK.
	- restrizioni (aperte o token)
	- e le informazioni specifiche per il tipo di distribuzione delle chiavi che definisce la modalità di distribuzione della chiave al client.
	
2. Configurare i criteri di distribuzione dell'asset. La configurazione dei criteri di distribuzione include:

	- protocollo di recapito (HLS),
	- il tipo di crittografia dinamica (crittografia CBC comune),
	- URL di acquisizione delle licenze.
	
	>[AZURE.NOTE]Per distribuire un flusso crittografato con FairPlay e un altro DRM, è necessario configurare criteri di distribuzione separati:
	>
	>- Un criterio IAssetDeliveryPolicy per configurare DASH con CENC (PlayReady e WideVine) e Smooth con PlayReady.
	>- Un altro criterio IAssetDeliveryPolicy per configurare FairPlay per HLS

1. Creare un localizzatore OnDemand per ottenere un URL di streaming.

##Uso della distribuzione delle chiavi FairPlay da app lettore/client

I clienti possono sviluppare applicazioni lettore tramite l'SDK di iOS. Per poter riprodurre contenuti FairPlay, i clienti sono devono implementare il protocollo di scambio delle licenze. Il protocollo di scambio delle licenze non è specificato da Apple: spetta a ogni app scegliere come inviare le richieste di distribuzione delle chiavi. Il servizio di distribuzione delle chiavi FairPlay di AMS prevede che SPC venga fornito in un messaggio codificato inviato come www-form-url nella forma seguente:

	spc=<Base64 encoded SPC>

>[AZURE.NOTE] Per impostazione predefinita, Azure Media Player non supporta la riproduzione FairPlay. I clienti devono ottenere il lettore di esempio dall'account per sviluppatori di Apple per poter eseguire la riproduzione FairPlay in MAC OSX.
 

##Esempio .NET


L'esempio seguente illustra la funzionalità introdotta in Azure Media Services SDK per .NET versione 3.6.0, che consente di usare Servizi multimediali di Azure per distribuire contenuti crittografati con FairPlay. Per installare il pacchetto NuGet, è stato usato il comando del pacchetto seguente:

	PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Creare un nuovo progetto Console.
1. Usare NuGet per installare e aggiungere Azure Media Services .NET SDK.
2. Aggiungere altri riferimenti: System.Configuration.
2. Aggiungere il file di configurazione che contiene il nome dell'account e le informazioni sulla chiave:
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Ottenere almeno un'unità di streaming per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere [Configurare gli endpoint di streaming](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
			
		
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
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
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
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
		        {
		            var encodingPreset = "H264 Multiple Bitrate 720p";
		
		            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
		                                    inputAsset.Name,
		                                    encodingPreset));
		
		            var mediaProcessors =
		                _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
		
		            var latestMediaProcessor =
		                mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
		
		            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
		            encodeTask.InputAssets.Add(inputAsset);
		            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
		
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


##Passaggi successivi: Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0629_2016-->