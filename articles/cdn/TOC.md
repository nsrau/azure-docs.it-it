# [Documentazione sulla rete CDN](index.md)

# [Panoramica](cdn-overview.md)
## [Che cos'è la rete CDN di Azure?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)

# Attività iniziali
## [Abilitare la rete CDN di Azure](cdn-create-new-endpoint.md)

# Procedure
## Integrare
### [App Web](../app-service/app-service-web-tutorial-content-delivery-network.md?toc=%2fazure%2fcdn%2ftoc.json)
### [Servizi cloud](cdn-cloud-service-with-cdn.md)
### Archiviazione
#### [Integrare un account di archiviazione](cdn-create-a-storage-account-with-cdn.md)
#### [Supporto per l'archiviazione della firma di accesso condiviso](cdn-sas-storage-support.md)
### [Condivisione di risorse tra le origini](cdn-cors.md)
### [Aggiungere un dominio personalizzato all'endpoint della rete CDN](cdn-map-content-to-custom-domain.md)
### [Configurare HTTPS in un dominio personalizzato](cdn-custom-ssl.md)
## Ottimizzare il contenuto
### [Panoramica dell'ottimizzazione](cdn-optimization-overview.md)
####[Ottimizzazione di file di grandi dimensioni](cdn-large-file-optimization.md)
####[Ottimizzazione dello streaming multimediale](cdn-media-streaming-optimization.md)
####[Accelerazione sito dinamico](cdn-dynamic-site-acceleration.md)
 
## Gestisci
### [Gestire con Azure PowerShell](cdn-manage-powershell.md)
### [Limitare l'accesso in base al paese](cdn-restrict-access-by-country.md)
### [Compressione dei file per migliorare le prestazioni](cdn-improve-performance.md)
### Controllare il comportamento di memorizzazione nella cache
#### [Funzionamento della memorizzazione nella cache](cdn-how-caching-works.md)
#### [Controllare il comportamento di memorizzazione nella cache con regole di memorizzazione nella cache](cdn-caching-rules.md)
#### Memorizzare il contenuto nella cache per stringhe di query
##### [Livello Standard](cdn-query-string.md)
##### [Livello Premium](cdn-query-string-premium.md)
#### [Ripulire asset memorizzati nella cache](cdn-purge-endpoint.md)
#### [Precaricare asset memorizzati nella cache](cdn-preload-endpoint.md)
### Configurare la durata (TTL)
#### [Contenuto Web di Azure](cdn-manage-expiration-of-cloud-service-content.md)
#### [Archivio BLOB di Azure](cdn-manage-expiration-of-blob-content.md)
### [Autenticazione tramite token](cdn-token-auth.md)
### [Monitorare le risorse](cdn-resource-health.md)
### [Eseguire l'override del comportamento con regole](cdn-rules-engine.md)
### [Ricevere avvisi in tempo reale](cdn-real-time-alerts.md)
### [Supporto HTTP/2](cdn-http2.md)

## Analizzare
### [Analizzare i modelli di utilizzo della rete CDN di Azure](cdn-log-analysis.md)
#### [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md)
#### Strumenti di analisi per la rete CDN di Azure da Verizon
##### [Report principali da Verizon](cdn-analyze-usage-patterns.md)
##### [Report personalizzati da Verizon](cdn-verizon-custom-reports.md)
#### Strumenti di analisi per la rete CDN Premium di Azure da Verizon
##### [Generare report HTTP avanzati](cdn-advanced-http-reports.md)
##### [Visualizzare statistiche in tempo reale](cdn-real-time-stats.md)
##### [Analizzare le prestazioni del nodo perimetrale](cdn-edge-performance.md)

## Sviluppo
### [.NET](cdn-app-dev-net.md)
### [Node.js](cdn-app-dev-node.md)

## Risolvere problemi
### [Stato 404](cdn-troubleshoot-endpoint.md)
### [Compressione dei file](cdn-troubleshoot-compression.md)

# riferimento
##  [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
### [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
### [Condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md)
### [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
## [Esempi di codice](https://azure.microsoft.com/en-us/resources/samples/?service=cdn)
## [Azure PowerShell](/powershell/module/azurerm.cdn)
## [.NET](/dotnet/api/microsoft.azure.management.cdn)
## [Java](/java/api/com.microsoft.azure.management.cdn)
## [REST](/rest/api/cdn/)

# Risorse
## Località POP della rete CDN di Azure
### [Località POP per area](cdn-pop-locations.md)
### [Località POP per abbreviazione](cdn-pop-abbreviations.md)
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecdn)
## [Prezzi](https://azure.microsoft.com/pricing/details/cdn/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=cdn)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cdn)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=cdn)

