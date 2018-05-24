---
title: Confronto tra funzionalità dei prodotti della rete per la distribuzione di contenuti di Azure (rete CDN) | Microsoft Docs
description: Informazioni sulle funzionalità supportate da ogni prodotto della rete per la distribuzione di contenuti di Azure (rete CDN).
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3368a8a14a3d1314e4c7ecae9256071f1fe646f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257870"
---
# <a name="compare-azure-cdn-product-features"></a>Confronto tra funzionalità dei prodotti della rete CDN di Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure include quattro prodotti: **rete CDN Standard di Azure con tecnologia Microsoft** (in anteprima), **rete CDN Standard di Azure con tecnologia Akamai**, **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**. 

La tabella seguente confronta le funzionalità disponibili con ogni prodotto.

| **Ottimizzazioni e funzionalità per le prestazioni** | **Standard Microsoft (anteprima)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Accelerazione sito dinamico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Compressione di immagini adattiva](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Prelettura degli oggetti](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Ottimizzazione dello streaming video](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Ottimizzazione di file di grandi dimensioni](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Bilanciamento del carico del server globale](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Eliminazione veloce](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Precaricamento Asset](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Impostazioni cache/intestazioni (con [regole di memorizzazione nella cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Impostazioni cache/intestazioni (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Memorizzazione nella cache della stringa di query](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Memorizzazione nella cache a livello di area  |**&#x2713;** |  |  |  |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Supporto HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sicurezza** | **Standard Microsoft (anteprima)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Supporto per HTTPS con endpoint della rete CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS dominio personalizzato](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Supporto del nome di dominio personalizzato.](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geografico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticazione tramite token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protezione DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Usare un certificato personale](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Analisi e report** | **Standard Microsoft (anteprima)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Report principali da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report personalizzati da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report HTTP avanzati](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiche in tempo reale](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestazioni del nodo perimetrale](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Avvisi in tempo reale](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Semplicità d'uso** | **Standard Microsoft (anteprima)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Integrazione semplice con i servizi di Azure, come [Archiviazione](cdn-create-a-storage-account-with-cdn.md), [App Web](cdn-add-to-web-app.md) e [Servizi multimediali](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestione tramite [API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oppure [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Motore di distribuzione di contenuti personalizzabile, basato su regole](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Riscrittura/reindirizzamento URL (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regole per dispositivi mobili (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* Microsoft e Verizon supportano la distribuzione di file di grandi dimensioni e di file multimediali direttamente tramite l'ottimizzazione di distribuzione Web generale.



