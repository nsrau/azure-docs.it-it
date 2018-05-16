---
title: Definizione di rete per la distribuzione di contenuti (rete CDN) - Azure | Microsoft Docs
description: Informazioni sulla rete per la distribuzione di contenuti (rete CDN) di Azure e su come usarla per distribuire contenuto con esigenze di larghezza di banda elevata.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 36de1011e48f274759c0aeebde74bd26c6604e19
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-a-content-delivery-network-on-azure"></a>Definizione di rete per la distribuzione di contenuti
Una rete per la distribuzione di contenuti (CDN) è una rete di server distribuita in grado di fornire contenuti Web agli utenti in modo efficiente. Per ridurre al minimo la latenza, le reti CDN archiviano il contenuto memorizzato nella cache in server perimetrali presenti in località POP (Point of Presence) prossime agli utenti finali. 

La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo. La rete CDN di Azure consente anche di accelerare contenuto dinamico, non memorizzabile nella cache, sfruttando diverse ottimizzazioni di rete tramite POP della rete CDN, ad esempio l'ottimizzazione delle route per ignorare il BGP (Border Gateway Protocol).

L'uso di una rete CDN di Azure per la distribuzione di asset dei siti Web offre diversi vantaggi, inclusi i seguenti:

* Prestazioni migliori e un'esperienza migliore per gli utenti finali, specialmente quando usano applicazioni in cui sono necessari più round trip per caricare il contenuto.
* Grande scalabilità per gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto.
* Distribuzione di richieste utente e di contenuti direttamente dai server perimetrali, in modo da inviare una quantità minore di traffico al server di origine.

Per un elenco aggiornato delle posizioni dei nodi della rete CDN, vedere [Località POP della rete CDN](cdn-pop-locations.md).

## <a name="how-it-works"></a>Funzionamento
![Panoramica della rete CDN](./media/cdn-overview/cdn-overview.png)

1. Un utente (Alice) richiede un file, detto anche asset, usando un URL con un nome di dominio particolare, ad esempio _&lt;nome endpoint&gt;_.azureedge.net. Questo nome può essere un nome host dell'endpoint o un dominio personalizzato. Il servizio DNS instrada la richiesta alla località POP (Point of Presence) che offre le migliori prestazioni, in genere quella geograficamente più vicina all'utente.
    
2. Se nella cache dei server perimetrali del POP il file non è disponibile, il POP richiede il file al server di origine. Il server di origine può essere un'app Web di Azure, il servizio Cloud di Azure, un account di archiviazione di Azure o qualsiasi server Web accessibile pubblicamente.
   
3. Il server di origine restituisce il file a un server perimetrale nel POP.
    
4. Un server perimetrale nel POP memorizza il file nella cache e lo restituisce al richiedente originale (Alice). Il file rimane nella cache del server perimetrale nel POP fino al termine della durata (valore TTL) specificata dalle intestazioni HTTP. Se il server di origine non ha specificato un valore TTL, il valore predefinito è di sette giorni.
    
5. Altri utenti possono quindi richiedere lo stesso file usando lo stesso URL usato da Alice ed essere indirizzati allo stesso POP.
    
6. Se il valore TTL per il file non è ancora scaduto, il server perimetrale restituisce il file direttamente dalla cache. Questo processo offre un'esperienza utente più veloce ed efficiente.

## <a name="requirements"></a>Requisiti
Per usare la rete CDN di Azure è necessario avere almeno una sottoscrizione di Azure. È anche necessario creare almeno un profilo di rete CDN, che è una raccolta di endpoint di rete CDN. Ogni endpoint di rete CDN rappresenta una configurazione specifica del comportamento di distribuzione dei contenuti e dell'accesso. Per organizzare gli endpoint di rete CDN per dominio Internet, applicazione Web o altri criteri, è possibile usare più profili. A livello di profilo di rete CDN vengono applicati i [prezzi della rete CDN di Azure](https://azure.microsoft.com/pricing/details/cdn/), quindi è necessario creare più profili di rete CDN se si vuole usare una combinazione di piani tariffari. Per informazioni sulla struttura di fatturazione della rete CDN di Azure, vedere [Informazioni sulla fatturazione per la rete CDN di Azure](cdn-billing.md).

### <a name="limitations"></a>Limitazioni
Ogni sottoscrizione di Azure ha limiti predefiniti per le risorse seguenti:
 - Numero di profili CDN che possono essere creati.
 - Numero di endpoint che possono essere creati in un profilo CDN. 
 - Numero di domini personalizzati di cui è possibile eseguire il mapping a un endpoint.

Per altre informazioni sui limiti delle sottoscrizioni CDN, vedere [Limiti relativi alla rete CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
    
## <a name="azure-cdn-features"></a>Funzionalità della rete CDN di Azure
La rete CDN di Azure offre le funzionalità chiave seguenti:

- [Accelerazione sito dinamico](cdn-dynamic-site-acceleration.md)
- [Regole di memorizzazione nella cache della rete CDN](cdn-caching-rules.md)
- [Supporto per domini personalizzati HTTPS](cdn-custom-ssl.md)
- [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md)
- [Compressione dei file](cdn-improve-performance.md)
- [Filtro geografico](cdn-restrict-access-by-country.md)

Per un elenco completo delle funzionalità supportate da ogni prodotto della rete CDN di Azure, vedere [Funzionalità del prodotto rete CDN di Azure](cdn-features.md).

## <a name="next-steps"></a>Passaggi successivi
- Per iniziare a usare la rete CDN, vedere [Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).
- Gestire gli endpoint della rete CDN con il [portale di Microsoft Azure](https://portal.azure.com) o [PowerShell](cdn-manage-powershell.md).
- Informazioni su come automatizzare la rete CDN di Azure con [.NET](cdn-app-dev-net.md) o [Node.js](cdn-app-dev-node.md).
- Per vedere la rete CDN di Azure in azione, guardare i [video della rete CDN di Azure](https://azure.microsoft.com/resources/videos/index/?services=cdn&sort=newest).
- Per informazioni sulle funzionalità più recenti della rete CDN di Azure, vedere il [blog della rete CDN Azure](https://azure.microsoft.com/blog/tag/azure-cdn/).

