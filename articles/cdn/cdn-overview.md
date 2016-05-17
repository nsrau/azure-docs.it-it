<properties
	pageTitle="Panoramica della rete CDN di Azure"
	description="Informazioni sulla rete per la distribuzione di contenuti (rete CDN) di Azure e su come usarla per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione nella cache di BLOB e contenuto statico."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure memorizza nella cache il contenuto Web statico in località strategiche per offrire la massima velocità effettiva per la distribuzione del contenuto agli utenti. La rete CDN offre agli sviluppatori una soluzione globale per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici ubicati in tutto il mondo. Per un elenco aggiornato delle posizioni dei nodi della rete CDN, vedere [Località POP della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-pop-locations.md).

L'uso della rete CDN per memorizzare nella cache gli asset dei siti Web offre diversi vantaggi, inclusi i seguenti:

- Una migliore esperienza utente e migliori prestazioni per gli utenti finali, specialmente quando usano applicazioni in cui sono necessari più round trip per caricare il contenuto.
- Grande scalabilità per gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto.
- Grazie alla distribuzione delle richieste utente e alla gestione del contenuto da server perimetrali, viene inviata una minore quantità di traffico all'origine.


## Funzionamento

![Panoramica della rete CDN](./media/cdn-overview/cdn-overview.png)

1. Un utente (Alice) richiede un file, detto anche un asset, usando un URL con un nome di dominio particolare, ad esempio `<endpointname>.azureedge.net`. Il servizio DNS instrada la richiesta alla località POP (Point of Presence) che offre le migliori prestazioni. In genere questo è il POP geograficamente più vicino all'utente.

2. Se nella cache dei server perimetrali del POP il file non è disponibile, verrà richiesto automaticamente all'origine. L'origine può essere un'app Web di Azure, il servizio Cloud di Azure, un account di archiviazione di Azure o qualsiasi server Web accessibile pubblicamente.

3. L'origine restituisce il file al server perimetrale, comprese le intestazioni HTTP facoltative che descrivono la durata (TTL) del file.

4. Il server perimetrale memorizza il file nella cache e lo restituisce al richiedente originale (Alice). Il file rimarrà nella cache del server perimetrale fino alla scadenza del valore TTL. Se l'origine ha specificato un valore TTL, il valore predefinito è 7 giorni.

5. Altri utenti (ad esempio Bob) possono quindi richiedere lo stesso file usando lo stesso URL e anche essere indirizzati allo stesso POP.

6. Se il valore TTL per il file non è ancora scaduto, il server perimetrale restituisce il file dalla cache, offrendo quindi un'esperienza utente più veloce ed efficiente.


## Funzionalità della rete CDN di Azure

Per la rete CDN di Azure sono disponibili tre prodotti: **Rete CDN di Azure Standard da Akamai**, **Rete CDN di Azure Standard da Verizon** e **Rete CDN di Azure Premium da Verizon**. La tabella seguente include l'elenco delle funzionalità disponibili con ogni prodotto.

| | Standard Akamai | Standard Verizon | Premium Verizon |
|-------|-----------------|------------------|-----------------|
| Facile integrazione con i servizi di Azure, ad esempio [Archiviazione](cdn-create-a-storage-account-with-cdn.md), [Servizi cloud](cdn-cloud-service-with-cdn.md), [App Web](../app-service-web/cdn-websites-with-cdn.md) e [Servizi multimediali](../media-services/media-services-manage-origins.md#enable_cdn) | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| Supporto di HTTPS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Bilanciamento del carico. | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Protezione DDOS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Supporto del nome di dominio personalizzato.](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Memorizzazione nella cache della stringa di query](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Filtro di paese](cdn-restrict-access-by-country.md) | | **&#x2713;** | **&#x2713;** |
| [Eliminazione veloce](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Precaricamento Asset](cdn-preload-endpoint.md) | | **&#x2713;** | **&#x2713;** |
| [Analisi del core](cdn-analyze-usage-patterns.md) | | **&#x2713;** | **&#x2713;** |
| [Gestione tramite l'API REST](https://msdn.microsoft.com/library/mt634456.aspx) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Motore di distribuzione di contenuti personalizzabile, basato su regole](cdn-rules-engine.md) | | | **&#x2713;** |
| [Report HTTP avanzati](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [Statistiche in tempo reale](cdn-real-time-stats.md) | | | **&#x2713;** |


## Passaggi successivi

Per iniziare con la rete CDN, vedere [Uso della rete CDN di Azure](./cdn-create-new-endpoint.md).

I clienti esistenti della rete CDN possono ora gestire gli endpoint della rete CDN attraverso il [portale di Microsoft Azure](https://portal.azure.com).

Per vedere come funziona la rete CDN, guardare il [video della sessione Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Per informazioni sui prezzi, vedere [Prezzi del servizio Rete di distribuzione dei contenuti (rete CDN)](https://azure.microsoft.com/pricing/details/cdn/).

<!---HONumber=AcomDC_0511_2016-->