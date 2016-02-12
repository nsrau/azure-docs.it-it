<properties 
	pageTitle="Panoramica della rete CDN di Azure" 
	description="Informazioni sulla rete per la distribuzione di contenuti (rete CDN) di Azure e su come usarla per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione nella cache di BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>

# Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure memorizza nella cache il contenuto statico e i BLOB di Azure usati dai servizi cloud in posizioni strategiche per offrire livelli massimi di larghezza di banda per la distribuzione del contenuto agli utenti.

I clienti esistenti della rete CDN possono ora gestire gli endpoint della rete CDN attraverso il [portale di Microsoft Azure](https://portal.azure.com).


La rete CDN offre agli sviluppatori una soluzione globale per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici ubicati in tutto il mondo. Per un elenco aggiornato delle località in cui si trovano i nodi della rete CDN, vedere [Località POP della rete per la distribuzione di contenuti (CDN) di Azure](cdn-pop-locations.md).

L'uso della rete CDN per memorizzare nella cache dati di Azure offre diversi vantaggi, inclusi i seguenti:

- Prestazioni ed esperienza utente migliori per utenti finali distanti da un'origine contenuto e che usano applicazioni in cui sono necessari molti passaggi in Internet per caricare il contenuto.
- Distribuzione su larga scala per gestire al meglio un carico elevato immediato, come all'inizio di un evento per il lancio di un prodotto. 


>[AZURE.IMPORTANT] Quando si crea o abilita un endpoint della rete CDN, la propagazione nel mondo può richiedere fino a 90 minuti.
 
Quando la richiesta di un oggetto viene prima effettuata alla rete CDN, l'oggetto viene recuperato direttamente dal percorso di origine dell’origine dell’oggetto. Questa origine può essere un account di archiviazione di Azure, un’app web, un servizio cloud o qualsiasi origine personalizzata che accetta le richieste web pubbliche. Quando viene effettuata una richiesta usando la sintassi della rete CDN, la richiesta viene reindirizzata all'endpoint della rete CDN più vicino alla località da cui è stata avviata la richiesta per fornire accesso all'oggetto. Se l'oggetto non viene trovato nell'endpoint, viene recuperato dal servizio e memorizzato nella cache nell'endpoint, in cui viene configurata un'impostazione di durata (TTL) per l'oggetto memorizzato nella cache.

## Funzionalità standard

Il livello della rete CDN Standard comprende le seguenti caratteristiche:

- Servizi di integrazione facile con Azure, come ad esempio [Archiviazione](cdn-create-a-storage-account-with-cdn.md), App Web e Servizi multimediali
- [Memorizzazione nella cache della stringa di query](cdn-query-string.md)
- [Supporto del nome di dominio personalizzato.](cdn-map-content-to-custom-domain.md)
- [Filtro di paese](cdn-restrict-access-by-country.md)
- [Analisi del core](cdn-analyze-usage-patterns.md)
- [Origini personalizzate del contenuto](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [Supporto di HTTPS](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Bilanciamento del carico
- Protezione DDOS
- [Eliminazione veloce](cdn-purge-endpoint.md)
- [Precaricamento Asset](cdn-preload-endpoint.md)
- [Gestione tramite l'API REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Funzionalità Premium

Il livello Premium della rete CDN comprende tutte le funzionalità del livello Standard oltre alle funzionalità aggiuntive seguenti:

- [Motore di distribuzione di contenuti personalizzabile, basato su regole](cdn-rules-engine.md)
- [Report HTTP avanzati](cdn-advanced-http-reports.md)
- [Statistiche in tempo reale](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0204_2016-->