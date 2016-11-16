# [Panoramica](media-services-overview.md)
## [Concetti](media-services-concepts.md)


# Introduzione
## [Creare e gestire gli account](media-services-portal-create-account.md)
## [Configurare l'ambiente di sviluppo](media-services-set-up-computer.md)
## Distribuire video su richiesta
### [Portale](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Eseguire lo streaming live
### [Portale](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedure
## Gestire
### [Gestire gli endpoint di streaming nel portale](media-services-portal-manage-streaming-endpoints.md)
### Gestire le entità
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Gestire gli account con PowerShell](media-services-manage-with-powershell.md)
### [Ritagliare video con Media Encoder Standard](media-services-crop-video.md)
### [Procedura: Aggiornare Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione](media-services-roll-storage-access-keys.md)
### [Quote e limitazioni](media-services-quotas-and-limitations.md)
### Filtri
#### [Creazione di filtri con Azure Media Services .NET SDK](media-services-dotnet-dynamic-manifest.md)
#### [Come codificare un asset mediante Media Encoder Standard](media-services-rest-encode-asset.md)
### Connessione a livello di codice
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST](media-services-rest-connect-programmatically.md)

## Caricare contenuti
### Caricare file in un account
#### [Portale](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Copiare BLOB esistenti](media-services-copying-existing-blob.md)

## Codificare
### [Contenuto](media-services-encode-asset.md)
#### Codificare un asset con Media Encoder Standard
##### [Portale](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [Come generare anteprime usando Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [Codifica avanzata](media-services-advanced-encoding-with-mes.md)
##### [Flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md)
##### [Esercitazioni sul flusso di lavoro Premium del codificatore multimediale](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Creare flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro](media-services-workflow-designer.md)
##### [Flusso di lavoro Premium con più input](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### Schemi 
#####[Codificatore multimediale standard](media-services-mes-schema.md)
#####[Metadati di input](media-services-input-metadata-schema.md)
#####[Metadati di output](media-services-output-metadata-schema.md)

#### Codificatori legacy
##### [Uso di Azure Media Packager](media-services-static-packaging.md)

### [Flussi live](media-services-manage-channels-overview.md)
#### [Codificatori locali](media-services-live-streaming-with-onprem-encoders.md)
#### Esercitazioni sui codificatori locali
##### [Portale](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [Streaming live con il codificatore cloud](media-services-manage-live-encoder-enabled-channels.md)
#### Esercitazioni sul codificatore cloud
##### [Portale](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [Configurare codificatori locali per l'uso con il codificatore cloud](media-services-live-encoders-overview.md)
#### [Gestire operazioni a esecuzione prolungata](media-services-dotnet-long-operations.md)
#### [Specifica per l'inserimento live di un flusso MP4 frammentato](media-services-fmp4-live-ingest-overview.md)
#### [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md)

### Elaborazione di contenuti multimediali
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)

### Configurare i codificatori per un flusso live a bitrate singolo
#### [Codificatore Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Codificatore FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificatore NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificatore Wirecast](media-services-configure-wirecast-live-encoder.md)

## [Proteggere](media-services-content-protection-overview.md)
### [Configurare la protezione del contenuto nel portale](media-services-portal-protect-content.md)
### [Configurare una chiave non crittografata AES-128 per il flusso](media-services-protect-with-aes128.md)
### [Crittografare i contenuti con la crittografia di archiviazione tramite API REST di AMS](media-services-rest-storage-encryption.md)
### [Panoramica del modello di licenza PlayReady di Servizi multimediali](media-services-playready-license-template-overview.md)
### [Distribuzione di licenze DRM](media-services-deliver-keys-and-licenses.md)
### [Uso di partner per distribuire licenze Widevine a Servizi multimediali di Azure](media-services-licenses-partner-integration.md)
### [Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md)
### [Usare Servizi multimediali di Azure per trasmettere il contenuto HLS in modo protetto con Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [CENC con DRM multiplo e controllo di accesso: progettazione di riferimento e implementazione in Azure e in Servizi multimediali di Azure](media-services-cenc-with-multidrm-access-control.md)

### Distribuzione degli asset
#### Configurare i criteri di distribuzione degli asset
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Creare entità ContentKey
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Configurare i criteri di autorizzazione della chiave simmetrica
#### [Portale](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Analizzare](media-services-analytics-overview.md)
### [Elaborare con Indexer 2](media-services-process-content-with-indexer2.md)
### [Elaborare con Indexer](media-services-index-content.md)
### [Elaborare con Hyperlapse](media-services-hyperlapse-content.md)
### [Elaborare con Face Detector](media-services-face-and-emotion-detection.md)
### [Elaborare con Motion Detector](media-services-motion-detection.md)
### [Elaborare con lo strumento di offuscamento dei volti](media-services-face-redaction.md)
### [Elaborare con Video Thumbnails](media-services-video-summarization.md)
### [Elaborare con OCR](media-services-video-optical-character-recognition.md)

## Ridimensionare
### [Elaborazione di contenuti multimediali](media-services-scale-media-processing-overview.md)
#### [Portale](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### Endpoint di streaming
#### [Portale](media-services-portal-scale-streaming-endpoints.md)

## [Distribuire contenuti](media-services-deliver-content-overview.md)
### [Panoramica dei filtri e dei manifesti dinamici](media-services-dynamic-manifest-overview.md)
### Creare filtri
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST](media-services-rest-dynamic-manifest.md)
### Pubblicare contenuti
#### [Portale](media-services-portal-publish.md)
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

## Integrare
### [Criterio di memorizzazione nella cache della rete CDN nell'estensione Servizi multimediali](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [Licenza per Microsoft†" Smooth Streaming Client Porting Kit](media-services-sspk.md)
### [Gestire asset tra più account di archiviazione](meda-services-managing-multiple-storage-accounts.md)
### [Uso di Axinom per fornire licenze Widevine ai Servizi multimediali di Azure](media-services-axinom-integration.md)
### [Uso di castLabs per distribuire licenze Widevine a Servizi multimediali di Azure](media-services-castlabs-integration.md)
### [Panoramica del modello di licenza Widevine](media-services-widevine-license-template-overview.md)

## Monitorare
### Controllare lo stato dei processi
#### [REST](media-services-rest-check-job-progress.md)
#### [Portale](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Archiviazione code per monitorare le notifiche dei processi](media-services-dotnet-check-job-progress-with-queues.md)

## Risolvere i problemi
### [Domande frequenti](media-services-frequently-asked-questions.md)
### [Guida per la risoluzione dei problemi relativi allo streaming live](media-services-troubleshooting-live-streaming.md)
###[Codici di errore](media-services-error-codes.md)
###[Logica di ripetizione dei tentativi](media-services-retry-logic-in-dotnet-sdk.md)

# riferimento
## [Note sulla versione](media-services-release-notes.md)
## [.NET](media-services-dotnet-how-to-use.md)
## [REST](media-services-rest-how-to-use.md)
## [Codec e formati del flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md)
## [Formati e codec Media Encoder Standard](media-services-media-encoder-standard-formats.md)

# Risorse
## [Prezzi](https://azure.microsoft.com/pricing/details/media-services/)
## [Community di Servizi multimediali di Azure](media-services-community.md)











<!--HONumber=Nov16_HO2-->


