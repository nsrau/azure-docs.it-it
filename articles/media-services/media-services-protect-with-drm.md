<properties
	pageTitle="Uso della crittografia comune dinamica PlayReady e/o Widevine"
	description="Servizi multimediali di Microsoft Azure consente di distribuire flussi MPEG-DASH, Smooth Streaming e Http-Live-Streaming (HLS) protetti con Microsoft PlayReady DRM. Consente inoltre di recapitare contenuti DASH crittografati con Widevine DRM. In questo argomento viene illustrato come crittografare in modo dinamico PlayReady e Widevine DRM."
	services="media-services"
	documentationCenter=""
	authors="Juliako,Mingfeiy"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
 	ms.date="05/03/2016" 
	ms.author="juliako"/>


#Uso della crittografia comune dinamica PlayReady e/o Widevine

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Servizi multimediali di Microsoft Azure consente di distribuire flussi MPEG-DASH, Smooth Streaming e HTTP-Live-Streaming (HLS) protetti con il DRM di [Microsoft PlayReady](https://www.microsoft.com/playready/overview/). Consente anche di distribuire contenuti DASH crittografati tramite licenze DRM Widevine. PlayReady e Widewine vengono crittografati in base alle specifiche della crittografia comune (ISO/IEC 23001-7 CENC). È possibile utilizzare [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (inizia con la versione 3.5.1) o l'API REST per configurare il AssetDeliveryConfiguration per utilizzare Widevine.

Servizi multimediali rende disponibili servizi per la distribuzione di licenze DRM Widevine e PlayReady. Servizi multimediali fornisce anche le API che consentono di configurare i diritti e le restrizioni che il runtime DRM di Widevine o PlayReady deve imporre quando l'utente riproduce contenuto protetto. Quando un utente richiede contenuto protetto tramite DRM, il lettore richiede una licenza dal servizio licenze AMS. Il servizio licenze AMS rilascia una licenza al lettore, se questo è autorizzato. Una licenza PlayReady o Widevine contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere il contenuto.


È anche possibile usare i partner AMS seguenti per facilitare la distribuzione di licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Per altre informazioni, vedere gli articoli relativi all'integrazione con [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Servizi multimediali supporta più modalità di autenticazione degli utenti che richiedono le chiavi. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open o Token. I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Per altre informazioni, vedere l'argomento Configurare i criteri di autorizzazione della chiave simmetrica.

Per sfruttare la crittografia dinamica, è necessario disporre di un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. È anche necessario configurare i criteri di distribuzione dell'asset (descritti più avanti in questo argomento). Quindi, in base al formato specificato nell'URL del flusso, il server di streaming on demand garantirà che il flusso sia distribuito nel protocollo scelto. Di conseguenza, è necessario archiviare e pagare solo per i file in un singolo formato di archiviazione e Servizi multimediali creerà e gestirà la risposta HTTP appropriata in base alle singole richieste dei client.

Questo argomento è utile per gli sviluppatori che creano applicazioni per la distribuzione di contenuto multimediale protetto tramite più DRM, ad esempio PlayReady e Widevine. L'argomento illustra come configurare il servizio di distribuzione della licenza PlayReady con criteri di autorizzazione, in modo che solo i client autorizzati possano ricevere licenze PlayReady o Widevine. Descrive anche come usare la crittografia dinamica con PlayReady o Widevine DRM tramite DASH.

>[AZURE.NOTE]Per iniziare a utilizzare la crittografia dinamica, è prima necessario ottenere almeno un'unità di scala (nota anche come unità di streaming). Per altre informazioni, vedere la sezione relativa al [ridimensionamento di un servizio multimediale](media-services-manage-origins.md#scale_streaming_endpoints).


##Scaricare un esempio

È possibile scaricare l'esempio descritto in questo articolo [qui](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##Configurazione della crittografia comune dinamica e dei servizi di distribuzione delle licenze DRM

Di seguito sono indicati i passaggi generali da eseguire quando si proteggono gli asset con PlayReady, tramite il servizio di distribuzione delle licenze di Servizi multimediali e tramite la crittografia dinamica.

1. Creare un asset e caricare file al suo interno. 
1. Codificare l'asset contenente il file per il set di file MP4 con velocità in bit adattiva.
1. Creare una chiave simmetrica e associarla all'asset codificato. In Servizi multimediali, la chiave simmetrica contiene la chiave di crittografia dell'asset. 
1. Configurare i criteri di autorizzazione della chiave simmetrica. I criteri di autorizzazione della chiave simmetrica devono essere configurati dall'utente e soddisfatti dal client affinché la chiave simmetrica possa essere distribuita al client.

	Quando si creano i criteri di autorizzazione della chiave simmetrica, è necessario specificare il metodo di distribuzione (PlayReady o Widevine), le restrizioni (Open o Token) e le informazioni specifiche del tipo di distribuzione della chiave che definisce la modalità di distribuzione della chiave al client (modello di licenza [PlayReady](media-services-playready-license-template-overview.md) o [Widevine](media-services-widevine-license-template-overview.md)). 
1. Configurare i criteri di distribuzione di un asset. La configurazione dei criteri di distribuzione include: protocollo di recapito (ad esempio, MPEG DASH, HLS, HDS, Smooth Streaming o tutti), tipo di crittografia dinamica (ad esempio, crittografia comune), URL di acquisizione della licenza PlayReady o Widevine. 
 
	È possibile applicare criteri diversi per ogni protocollo allo stesso asset. Ad esempio, è possibile applicare la crittografia PlayReady a Smooth/DASH e AES Envelope ad HLS. Gli eventuali protocolli non definiti nei criteri di distribuzione (ad esempio quando si aggiunge un singolo criterio che specifica soltanto HLS come protocollo) verranno esclusi dallo streaming. Questo comportamento non si verifica quando non è presente alcun criterio di distribuzione degli asset. In tal caso, sono consentiti tutti i protocolli in chiaro.
1. Creare un localizzatore OnDemand per ottenere un URL di streaming.

Si noterà un esempio .NET completo alla fine dell'argomento.

Nell'immagine seguente viene illustrato il flusso di lavoro descritto in precedenza. Di seguito viene utilizzato il token per l'autenticazione.

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Nella parte rimanente di questo argomento vengono fornite spiegazioni dettagliate, esempi di codice e collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.

##Limitazioni correnti

Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare l'eventuale localizzatore associato (se presente) e crearne uno nuovo.

Limitazione per la crittografia tramite Widevine con Servizi multimediali di Azure: attualmente non sono supportate più chiavi simmetriche.

##Creare un asset e caricare file nell'asset

Per gestire, codificare e trasmettere i video, è innanzitutto necessario caricare il contenuto in Servizi multimediali di Microsoft Azure. Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

Per informazioni dettagliate, vedere [Caricare file in un account di Servizi multimediali](media-services-dotnet-upload-files.md).

##Codificare l'asset contenente il file per il set di file MP4 con velocità in bit adattiva

Con la crittografia dinamica, è necessario solamente creare un asset che contenga un set di file MP4 con velocità in bit multipla o di file di origine Smooth Streaming con velocità in bit multipla. In base al formato specificato nella richiesta del manifesto e del frammento, il server di streaming on demand garantirà che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client. Per altre informazioni, vedere l'argomento [Panoramica della creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

Per istruzioni su come eseguire la codifica, vedere [Come codificare un asset mediante Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).
	

##<a id="create_contentkey"></a>Creare una chiave simmetrica e associarla all'asset codificato

In Servizi multimediali, la chiave simmetrica contiene la chiave con cui si desidera crittografare un asset.

Per informazioni dettagliate, vedere [Creare una chiave simmetrica](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave simmetrica.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione della chiave simmetrica devono essere configurati dall'utente e soddisfatti dal client (lettore) affinché la chiave possa essere distribuita al client. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo Open o Token.

Per informazioni dettagliate, vedere [Configurare i criteri di autorizzazione della chiave simmetrica](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurare i criteri di distribuzione dell'asset 

Configurare i criteri di distribuzione dell'asset. Alcuni aspetti inclusi nella configurazione dei criteri di distribuzione dell’asset:

- L'URL di acquisizione della licenza DRM. 
- Il protocollo di recapito dell’asset (ad esempio, MPEG DASH, HLS, HDS, Smooth Streaming o tutti). 
- Il tipo di crittografia dinamica (in questo caso, crittografia comune). 

Per informazioni dettagliate, vedere [Configurare i criteri di distribuzione degli asset](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Creare un localizzatore di streaming on demand per ottenere un URL di streaming

È necessario fornire all'utente l'URL del flusso per Smooth, DASH o HLS.

>[AZURE.NOTE]Se si aggiungono o si aggiornano i criteri di distribuzione dell'asset, è necessario eliminare l'eventuale localizzatore esistente e creare un nuovo localizzatore.

Per istruzioni su come pubblicare un asset e creare un URL di streaming, vedere la sezione [Creare URL di streaming](media-services-deliver-streaming-content.md).

##Ottenere un token di test

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

##<a id="example"></a>Esempio


L'esempio seguente illustra la funzionalità introdotta in Azure Media Services SDK per .Net versione 3.5.2, in particolare la possibilità di definire un modello di licenza Widevine e richiedere una licenza Widevine da Servizi multimediali di Azure. Per installare il pacchetto NuGet, è stato usato il comando del pacchetto seguente:

	PM> Install-Package windowsazure.mediaservices -Version 3.5.2


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
		using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
		using Newtonsoft.Json;
		
		namespace DynamicEncryptionWithDRM
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
		
		            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
		
		            // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
		            // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
		             
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
		
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
		
		
		        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
		        {
		            // Create envelope encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryption);
		
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
		
		            // Configure PlayReady and Widevine license templates.
		            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, PlayReadyLicenseTemplate);
		
		            IContentKeyAuthorizationPolicyOption WidevinePolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("", 
		                    ContentKeyDeliveryType.Widevine, 
		                    restrictions, WidevineLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with no restrictions").
		                        Result;
		
		
		            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
		            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
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
		
		            // Configure PlayReady and Widevine license templates.
		            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, PlayReadyLicenseTemplate);
		
		            IContentKeyAuthorizationPolicyOption WidevinePolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.Widevine,
		                        restrictions, WidevineLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
		            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
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
		
		        static private string ConfigurePlayReadyLicenseTemplate()
		        {
		            // The following code configures PlayReady License Template using .NET classes
		            // and returns the XML string.
		
		            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
		            //It contains a field for a custom data string between the license server and the application 
		            //(may be useful for custom app logic) as well as a list of one or more license templates.
		            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
		
		            // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
		            // to be returned to the end users. 
		            //It contains the data on the content key in the license and any rights or restrictions to be 
		            //enforced by the PlayReady DRM runtime when using the content key.
		            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
		            //Configure whether the license is persistent (saved in persistent storage on the client) 
		            //or non-persistent (only held in memory while the player is using the license).  
		            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
		
		            // AllowTestDevices controls whether test devices can use the license or not.  
		            // If true, the MinimumSecurityLevel property of the license
		            // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
		            licenseTemplate.AllowTestDevices = true;
		
		            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
		            // It grants the user the ability to playback the content subject to the zero or more restrictions 
		            // configured in the license and on the PlayRight itself (for playback specific policy). 
		            // Much of the policy on the PlayRight has to do with output restrictions 
		            // which control the types of outputs that the content can be played over and 
		            // any restrictions that must be put in place when using a given output.
		            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
		            //then the DRM runtime will only allow the video to be displayed over digital outputs 
		            //(analog video outputs won’t be allowed to pass the content).
		
		            //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
		            // If the output protections are configured too restrictive, 
		            // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
		
		            // For example:
		            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
		
		            responseTemplate.LicenseTemplates.Add(licenseTemplate);
		
		            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
		        }
		
		
		        private static string ConfigureWidevineLicenseTemplate()
		        {
		            var template = new WidevineMessage
		            {
		                allowed_track_types = AllowedTrackTypes.SD_HD,
		                content_key_specs = new[]
		                {
		                    new ContentKeySpecs
		                    {
		                        required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
		                        security_level = 1,
		                        track_type = "SD"
		                    }
		                },
		                policy_overrides = new
		                {
		                    can_play = true,
		                    can_persist = true,
		                    can_renew = false
		                }
		            };
		
		            string configuration = JsonConvert.SerializeObject(template);
		            return configuration;
		        }
		
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
		                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
		
		                };
		
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryption,
		                AssetDeliveryProtocol.Dash,
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


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Vedere anche

[CENC con Multi-DRM e controllo di accesso](media-services-cenc-with-multidrm-access-control.md)

[Configurare i pacchetti Widevine con AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Annuncio dei servizi di distribuzione delle licenze Google Widevine in Servizi multimediali di Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)

<!----HONumber=AcomDC_0504_2016-->