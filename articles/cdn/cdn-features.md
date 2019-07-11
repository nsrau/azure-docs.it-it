---
title: Confronto tra funzionalità dei prodotti della rete per la distribuzione di contenuti di Azure (rete CDN) | Microsoft Docs
description: Informazioni sulle funzionalità supportate da ogni prodotto della rete per la distribuzione di contenuti di Azure (rete CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e34fbe200420fef1653e21bb44ac2f7e0de90fb7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593861"
---
# <a name="compare-azure-cdn-product-features"></a>Confronto tra funzionalità dei prodotti della rete CDN di Azure

La rete CDN di Azure include quattro prodotti: **rete CDN Standard di Azure con tecnologia Microsoft**, **rete CDN Standard di Azure con tecnologia Akamai**, **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**. Per informazioni sulla migrazione di un profilo di **rete CDN di Azure Standard di Verizon** a un profilo di **rete CDN di Azure Premium di Verizon**, vedere [Migrare un profilo di rete CDN di Azure da Standard Verizon a Premium Verizon](cdn-migrate.md).

La tabella seguente confronta le funzionalità disponibili con ogni prodotto.

| **Ottimizzazioni e funzionalità per le prestazioni** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Accelerazione sito dinamico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Offerto dal [servizio Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Compressione di immagini adattiva](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Prelettura degli oggetti](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Ottimizzazione distribuzione Web generale](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , selezionare questo tipo di ottimizzazione se le dimensioni medie dei file sono inferiori a 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Ottimizzazione dello streaming video](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | tramite Distribuzione Web generale | **&#x2713;**  | tramite Distribuzione Web generale |  tramite Distribuzione Web generale |
| [Ottimizzazione di file di grandi dimensioni](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | tramite Distribuzione Web generale | **&#x2713;** , selezionare questo tipo di ottimizzazione se le dimensioni medie dei file sono superiori a 10 MB   | tramite Distribuzione Web generale |  tramite Distribuzione Web generale |
| Cambia tipo di ottimizzazione | |**&#x2713;** | | |
| Porta dell'origine |Tutte le porte TCP |[Porte di origine consentite](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Tutte le porte TCP |Tutte le porte TCP |
| [Bilanciamento del carico del server globale](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Eliminazione veloce](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , le funzioni Elimina tutti e Wildcard purge (Eliminazione dei caratteri jolly) non sono attualmente supportate dalla rete CDN di Azure fornita da Akamai |**&#x2713;** |**&#x2713;** |
| [Precaricamento Asset](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Impostazioni cache/intestazioni (con [regole di memorizzazione nella cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Motore di distribuzione di contenuti personalizzabile, basato su regole (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Impostazioni cache/intestazioni (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Riscrittura/reindirizzamento URL (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regole per dispositivi mobili (con il [motore regole](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Memorizzazione nella cache della stringa di query](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Supporto HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sicurezza** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Supporto per HTTPS con endpoint della rete CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS dominio personalizzato](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , per l'abilitazione è necessario un record CNAME diretto |**&#x2713;** |**&#x2713;** |
| [Supporto del nome di dominio personalizzato.](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geografico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticazione tramite token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protezione DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Usare un certificato personale](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Analisi e report** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Report principali da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report personalizzati da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report HTTP avanzati](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiche in tempo reale](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestazioni del nodo perimetrale](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Avvisi in tempo reale](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Semplicità d'uso** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Integrazione semplice con i servizi di Azure, come [Archiviazione](cdn-create-a-storage-account-with-cdn.md), [App Web](cdn-add-to-web-app.md) e [Servizi multimediali](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestione tramite [API REST](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oppure [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipi MIME con compressione](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Solo predefinito |Configurabile |Configurabile  |Configurabile  |
| Codifiche di compressione  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






