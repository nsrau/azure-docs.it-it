# [Panoramica](media-services-overview.md)
## [Scenari e disponibilità](scenarios-and-availability.md)
## [Concetti](media-services-concepts.md)

# Attività iniziali
## [Creare e gestire gli account](media-services-portal-create-account.md)
## [Configurare l'ambiente di sviluppo](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Usare l'autorizzazione AAD per accedere all'API](media-services-use-aad-auth-to-access-ams-api.md)
### [Usare il portale per gestire l'autorizzazione AAD](media-services-portal-get-started-with-aad.md)
### [Accedere all'API con .NET](media-services-dotnet-get-started-with-aad.md)
### [Accedere all'API con REST](media-services-rest-connect-with-aad.md)
### [Usare l'interfaccia della riga di comando di Azure per creare e configurare l'app AAD](media-services-cli-create-and-configure-aad-app.md)
### [Usare Azure PowerShell per creare e configurare l'app AAD](media-services-powershell-create-and-configure-aad-app.md)

## Distribuire video su richiesta
### [Portale di Azure](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Eseguire lo streaming live
### [Portale di Azure](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedure
## Gestisci
### Entità
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Endpoint di streaming](media-services-streaming-endpoints-overview.md)
#### [Portale di Azure](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Archiviazione
#### [Aggiornare Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione](media-services-roll-storage-access-keys.md)
#### [Gestire asset tra più account di archiviazione](meda-services-managing-multiple-storage-accounts.md)
### [Quote e limitazioni](media-services-quotas-and-limitations.md)

## Caricare contenuti
### Caricare file in un account
#### [Portale di Azure](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Caricare file di grandi dimensioni con Aspera](media-services-upload-files-with-aspera.md)
### [Caricare file con StorSimple](media-services-upload-files-from-storsimple.md)
### [Copiare BLOB esistenti](media-services-copying-existing-blob.md)

## [Codificare contenuti](media-services-encode-asset.md)
### [Confronto tra i codificatori](media-services-compare-encoders.md)
### [Gestire la velocità e la concorrenza della codifica](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Formati e codec Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Usare MES per generare automaticamente una tabella di coppie bitrate-risoluzione](media-services-autogen-bitrate-ladder-with-mes.md)
#### Codificare con Media Encoder Standard
##### [Portale di Azure](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Codifica avanzata con MES](media-services-advanced-encoding-with-mes.md)
##### [Personalizzare i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Come generare anteprime usando Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Ritagliare video con Media Encoder Standard](media-services-crop-video.md)
#### Schemi MES
##### [Schema Media Encoder Standard](media-services-mes-schema.md)
##### [Metadati di input](media-services-input-metadata-schema.md)
##### [Metadati di output](media-services-output-metadata-schema.md)
#### [Impostazioni predefinite MES](media-services-mes-presets-overview.md) 
##### [Codec video H.264 a bitrate multiplo con risoluzione 1080p e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 16x9 SD e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 16x9 per iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 4K e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 4x3 SD e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 4x3 per iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 720p e audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [Codec video H.264 a bitrate multiplo con risoluzione 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 1080p e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 16x9 SD e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 4K e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 4x3 SD e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 720p e audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [Codec video H.264 a bitrate singolo con risoluzione 720p per Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [Codec video H.264 a bitrate singolo con risoluzione SD di alta qualità per Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [Codec video H.264 a bitrate singolo con risoluzione SD di bassa qualità per Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Flusso di lavoro Premium del codificatore multimediale
#### [Codec e formati del flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md)
#### Codificare con il flusso di lavoro Premium del codificatore multimediale
##### [Flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md)
##### [Esercitazioni sul flusso di lavoro Premium del codificatore multimediale](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Creare flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro](media-services-workflow-designer.md)
##### [Flusso di lavoro Premium con più input](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Creare un'attività che genera blocchi fMP4](media-services-generate-fmp4-chunks.md)
### Processori di contenuti multimediali
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Codici di errore](media-services-encoding-error-codes.md)
### Funzionalità deprecate
#### [Creazione statica dei pacchetti e crittografia](media-services-static-packaging.md)

## [Streaming live](media-services-manage-channels-overview.md)
### [Codificatori locali](media-services-live-streaming-with-onprem-encoders.md)
#### [Codificatori locali consigliati](media-services-recommended-encoders.md)
#### [Portale di Azure](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Streaming live con il codificatore cloud](media-services-manage-live-encoder-enabled-channels.md)
#### [Portale di Azure](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Configurare codificatori locali per l'uso con il codificatore cloud](media-services-live-encoders-overview.md)
#### [Codificatore Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Codificatore FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificatore NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificatore Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Gestire operazioni a esecuzione prolungata](media-services-dotnet-long-operations.md)
### [Specifica per l'inserimento live di un flusso MP4 frammentato](media-services-fmp4-live-ingest-overview.md)

## [Ritagliare contenuti](media-services-azure-media-clipper-overview.md)
### [Attività iniziali](media-services-azure-media-clipper-getting-started.md)
### [Caricare video](media-services-azure-media-clipper-load-assets.md)
### [Configurare le scelte rapide da tastiera](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Configurare la localizzazione](media-services-azure-media-clipper-localization.md)
### [Inviare processi di ritaglio](media-services-azure-media-clipper-submit-job.md)
### [Portale di Azure](media-services-azure-media-clipper-portal.md)

## [Proteggere i contenuti](media-services-content-protection-overview.md)
### [Crittografia di archiviazione](media-services-rest-storage-encryption.md)
### [Crittografia AES-128](media-services-protect-with-aes128.md)
### [PlayReady/Widevine per lo streaming](media-services-protect-with-playready-widevine.md)
### [FairPlay per lo streaming](media-services-protect-hls-with-fairplay.md)
### [PlayReady offline](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [FairPlay offline](media-services-protect-hls-with-offline-fairplay.md)
### [Configurare il portale di Azure](media-services-portal-protect-content.md)
### [Distribuire licenze DRM](media-services-deliver-keys-and-licenses.md)
### Creare entità ContentKey
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Panoramiche dei modelli di licenza
#### [Modello di licenza PlayReady](media-services-playready-license-template-overview.md)
#### [Modello di licenza Widevine](media-services-widevine-license-template-overview.md)
### Configurare i criteri di distribuzione degli asset
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Configurare i criteri di autorizzazione della chiave simmetrica
#### [Portale di Azure](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Passare i token di autenticazione a Servizi multimediali di Azure](media-services-pass-authentication-tokens.md)
### Progettazioni di riferimento
#### [Progettazione ibrida di sistemi DRM](hybrid-design-drm-sybsystem.md)
#### [Progettazione con DRM multiplo di riferimento](media-services-cenc-with-multidrm-access-control.md)

## [Analizzare](media-services-analytics-overview.md)
### [Analizzare i file multimediali usando il portale di Azure](media-services-portal-analyze.md)
### [Elaborare con Indexer 2](media-services-process-content-with-indexer2.md)
### [Elaborare con Indexer](media-services-index-content.md)
#### [Set di impostazioni per le attività](indexer-task-preset.md)
### [Elaborare con Hyperlapse](media-services-hyperlapse-content.md)
### [Elaborare con Face Detector](media-services-face-and-emotion-detection.md)
### [Elaborare con Motion Detector](media-services-motion-detection.md)
### [Elaborare con lo strumento di offuscamento dei volti](media-services-face-redaction.md)
#### [Procedura dettagliata per lo strumento di offuscamento dei volti](media-services-redactor-walkthrough.md)
### [Elaborare con Video Thumbnails](media-services-video-summarization.md)
### [Elaborare con OCR](media-services-video-optical-character-recognition.md)

## [Configurare i dati di telemetria](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Scalabilità
### [Elaborazione di contenuti multimediali](media-services-scale-media-processing-overview.md)
#### [Portale di Azure](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Endpoint di streaming
#### [Portale di Azure](media-services-portal-scale-streaming-endpoints.md)

## [Distribuire contenuti](media-services-deliver-content-overview.md)
### [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md)
### [Panoramica dei filtri e dei manifesti dinamici](media-services-dynamic-manifest-overview.md)
#### [Creare filtri con .NET](media-services-dotnet-dynamic-manifest.md)
#### [Creare filtri con REST](media-services-rest-dynamic-manifest.md)
### [Criterio di memorizzazione nella cache della rete CDN nell'estensione Servizi multimediali](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Pubblicare contenuti
#### [Portale di Azure](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Distribuire tramite download](media-services-deliver-asset-download.md)
### [Scenario di streaming con failover](media-services-implement-failover.md)

## Utilizzo
### [Riprodurre contenuti multimediali con i lettori esistenti](media-services-playback-content-with-existing-players.md)
### [Riprodurre contenuti multimediali con Media Player](media-services-develop-video-players.md)
### Altre opzioni di riproduzione
#### [Applicazione Smooth Streaming di Windows Store](media-services-build-smooth-streaming-apps.md)
#### [Applicazione HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Lettori Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Inserire annunci sul lato client](media-services-inserting-ads-on-client-side.md)
### [Licenza per Microsoft Smooth Streaming Client Porting Kit](media-services-sspk.md)

## Integrare
### [Usare Funzioni di Azure con Servizi multimediali](media-services-dotnet-how-to-use-azure-functions.md)
### [Esempi di Funzioni di Azure con Servizi multimediali](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Monitorare
### Controllare lo stato dei processi
#### [REST](media-services-rest-check-job-progress.md)
#### [Portale di Azure](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Monitorare le notifiche dei processi con l'archiviazione code](media-services-dotnet-check-job-progress-with-queues.md)
### [Monitorare le notifiche dei processi con webhook](media-services-dotnet-check-job-progress-with-webhooks.md)

## Risolvere problemi
### [Domande frequenti](media-services-frequently-asked-questions.md)
### [Guida per la risoluzione dei problemi relativi allo streaming live](media-services-troubleshooting-live-streaming.md)
### [Codici di errore](media-services-error-codes.md)
### [Logica di ripetizione dei tentativi](media-services-retry-logic-in-dotnet-sdk.md)

# riferimento
## [Esempi di codice](https://azure.microsoft.com/en-us/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (gestione dei servizi)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)  

# Risorse
## [Community di Servizi multimediali di Azure](media-services-community.md)
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Prezzi](https://azure.microsoft.com/pricing/details/media-services/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Note sulla versione](media-services-release-notes.md)
## [Video](https://azure.microsoft.com/resources/videos/index/?services=media-services)
