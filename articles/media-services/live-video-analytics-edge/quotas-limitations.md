---
title: Analisi video in tempo reale su IoT Edge quote e limitazioni-Azure
description: Questo articolo descrive le analisi video in tempo reale su IoT Edge quote e limitazioni.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529231"
---
# <a name="quotas-and-limitations"></a>Quote e limitazioni

In questo articolo vengono enumerate le quote e le limitazioni dell'analisi video in tempo reale sul modulo IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Periodo massimo di utilizzo disconnesso

Il modulo Edge può sostenere la perdita temporanea della connettività di rete. Se il modulo rimane disconnesso per più di 36 ore, disattiverà tutte le istanze del grafo in esecuzione e le chiamate ai metodi diretti verranno bloccate.

Per riprendere il modulo perimetrale in uno stato operativo, è necessario ripristinare la connettività di rete e il modulo deve essere in grado di comunicare correttamente con l'account di servizi multimediali di Azure.

## <a name="maximum-number-of-graph-instances"></a>Numero massimo di istanze del grafo

È possibile avere al massimo 1000 istanze del grafo per ogni modulo (create tramite GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Numero massimo di topologie del grafo

È possibile avere al massimo 50 topologie di grafo per modulo (create tramite GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitazioni sulle topologie Graph in anteprima

Con la versione di anteprima, è possibile connettere in una topologia del grafico multimediale alcune limitazioni relative a nodi diversi.

* Origine RTSP
   * È consentita una sola origine RTSP per ogni topologia Graph.
* Processore filtro frequenza frame
   * Deve essere immediatamente a valle dal processore RTSP source o Motion Detection.
   * Non può essere usato a valle di un processore di estensione HTTP o gRPC.
   * Non può essere upstream da un processore di rilevamento del movimento.
* Processore di estensione HTTP
   * Può essere presente al massimo un processore per ogni topologia Graph.
* Processore di estensione gRPC
   * Può essere presente al massimo un processore per ogni topologia Graph.
* Processore rilevamento movimento
   * Deve essere immediatamente a valle dall'origine RTSP.
   * Può essere presente al massimo un processore per ogni topologia Graph.
   * Non può essere usato a valle di un processore di estensione HTTP o gRPC.
* Processore Gate Signal
   * Deve essere immediatamente a valle dall'origine RTSP.
* Sink di asset 
   * Deve essere immediatamente downstream dal processore RTSP source o Signal Gate.
* Sink di file
   * Deve essere immediatamente downstream dal processore del Gate Signal.
   * Non può essere immediatamente downstream di un processore di estensione HTTP o gRPC o di un processore di rilevamento del movimento
* Sink hub tutto
   * Non può essere immediatamente a valle di un'origine hub Internet.

Se vengono usati sia il rilevamento del movimento che i nodi del processore della frequenza dei filtri, devono trovarsi nella stessa catena di nodi che conduce al nodo di origine RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitazioni relative alle operazioni del servizio multimediale in anteprima

Al momento della versione di anteprima, l'analisi video live su IoT Edge non supporta quanto segue:

* Possibilità di eseguire la migrazione dell'account del servizio multimediale da una sottoscrizione a un'altra senza interruzioni.
* Possibilità di usare più di un account di archiviazione con l'account del servizio multimediale.
* Possibilità di modificare le informazioni sull'entità servizio nelle proprietà desiderate del modulo in modo dinamico, senza un riavvio.

È possibile utilizzare solo le fotocamere IP che supportano il protocollo RTSP. È possibile trovare videocamere IP che supportano RTSP nella pagina dei [prodotti conformi a ONVIF](https://www.onvif.org/conformant-products). Cercare dispositivi conformi ai profili G, S o T.

Inoltre, è necessario configurare queste fotocamere per l'uso di video H. 264 e audio AAC. Non sono attualmente supportati altri codec. 

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](overview.md)
