---
title: Report principali da Verizon | Microsoft Docs
description: "È possibile visualizzare i modelli di utilizzo per la rete CDN usando i report seguenti: Larghezza di banda, Dati trasferiti, Riscontri, Stati della cache, Percentuale riscontri cache, Dati trasferiti IPv4/IPv6."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9ee0041061296e313b3372dce13b5b86b2369c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="core-reports-from-verizon"></a>Report principali da Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Usando i report principali da Verizon tramite il portale di gestione per i profili Verizon, è possibile visualizzare i modelli di utilizzo per la rete CDN con i report seguenti:

* Larghezza di banda
* Dati trasferiti
* Riscontri
* Stati della cache
* Percentuale riscontri cache
* Dati trasferiti IPv4/IPV6

## <a name="accessing-verizon-core-reports"></a>Accesso ai Report principali da Verizon
1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **Analisi** e quindi sul riquadro a comparsa **Core Reports** (Report di base).  Fare clic sul report desiderato nel menu.
   
    ![Portale di gestione della rete CDN, menu Core Reports (Report di base)](./media/cdn-reports/cdn-core-reports.png)

## <a name="bandwidth"></a>Larghezza di banda
Il report della larghezza di banda è costituito da un grafico e da una tabella dati che indicano l'uso della larghezza di banda per HTTP e HTTPS in un periodo di tempo specifico. È possibile visualizzare l'uso della larghezza di banda per tutti i POP della rete CDN o un particolare POP della rete CDN. Questo report consente di visualizzare i picchi di traffico e la distribuzione nei POP della rete CDN in Mbps.

* Selezionare Tutti i nodi Edge per visualizzare il traffico proveniente da tutti i nodi o scegliere una regione/nodo specifico dall'elenco a discesa.
* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate, quindi scegliere **Vai** per assicurarsi che la selezione venga aggiornata.
* È possibile esportare e scaricare i dati facendo clic sull'icona del foglio di Excel che si trova accanto a **Vai**.

Il report viene aggiornato ogni 5 minuti.

![Report della larghezza di banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dati trasferiti
Il report è costituito da un grafico e da una tabella dati che indicano l'uso del traffico per HTTP e HTTPS in un periodo di tempo specifico. È possibile visualizzare l'utilizzo del traffico per tutti i POP CDN o un particolare POP. Questo report consente di visualizzare i picchi di traffico e la distribuzione nei POP della rete CDN in GB.

* Selezionare Tutti i nodi Edge per visualizzare il traffico proveniente da tutti i nodi o scegliere una regione/nodo specifico dall'elenco a discesa.
* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate, quindi scegliere **Vai** per assicurarsi che la selezione venga aggiornata.
* È possibile esportare e scaricare i dati facendo clic sull'icona del foglio di Excel che si trova accanto a **Vai**.

Il report viene aggiornato ogni 5 minuti.

![Report dati trasferiti](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Riscontri (codici di stato)
Questo report descrive la distribuzione dei codici di stato delle richieste per il contenuto. Tutte le richieste di contenuto generano un codice di stato HTTP. Il codice di stato descrive come i POP edge hanno gestito la richiesta. Ad esempio, un codice di stato 2xx indica che la richiesta è stata servita correttamente a un client, mentre un codice di stato 4xx indica che si è verificato un errore. Per altre informazioni sui codici di stato HTTP, vedere [Codici di stato](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate, quindi scegliere **Vai** per assicurarsi che la selezione venga aggiornata.
* È possibile esportare e scaricare i dati facendo clic sul foglio di Excel che si trova accanto a **Vai**.

![Report riscontri](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stati della cache
Questo report descrive la distribuzione dei riscontri nella cache e i mancati riscontri nella cache per la richiesta del client. Poiché le prestazioni più veloci provengono dai riscontri nella cache, è possibile ottimizzare le velocità di recapito dei dati riducendo al minimo i mancati riscontri nella cache e i riscontri nella cache scaduti. I mancati riscontri nella cache possono essere ridotti mediante la configurazione del server di origine per evitare di assegnare le intestazioni di risposta "no-cache", evitando la memorizzazione nella cache della stringa di query eccetto quando è strettamente necessario ed evitando codici di risposta non memorizzabili nella cache. I riscontri nella cache scaduti possono essere evitati creando una durata max. dell'asset per ridurre al minimo il numero di richieste al server di origine.

![Report stati della cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Gli stati della cache principali includono:
* TCP_HIT: servito dall’Edge. L'oggetto è stato nella cache e non ha superato la durata max.
* TCP_MISS: servito dall'origine. L'oggetto non era presente nella cache e la risposta è stata inviata nuovamente all'origine.
* TCP_EXPIRED _MISS: servito dall'origine dopo la riconvalida con l’origine. L'oggetto era nella cache, ma ha superato la durata max. Una riconvalida con l’origine ha causato la sostituzione dell'oggetto nella cache con una nuova risposta dall'origine.
* TCP_EXPIRED _HIT: servito dall’Edge dopo la riconvalida con l’origine. L'oggetto era nella cache, ma ha superato la durata max. Una riconvalida con il server di origine ha causato la non modifica dell'oggetto nella cache.
* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate, quindi scegliere **Vai** per assicurarsi che la selezione venga aggiornata.
* È possibile esportare e scaricare i dati facendo clic sull'icona del foglio di Excel che si trova accanto a **Vai**.

### <a name="full-list-of-cache-statuses"></a>Elenco completo degli stati della cache
* TCP_HIT: questo stato viene segnalato quando una richiesta viene gestita direttamente dal POP al client. Un asset viene servito immediatamente da un POP quando viene memorizzato nella cache nel POP più vicino al client e ha un valore TTL (time-to-live) valido. Il TTL viene determinato tramite le seguenti intestazioni di risposta:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Expires
* TCP_MISS: questo stato indica che una versione memorizzata nella cache dell’asset richiesto non è stata trovata nel POP vicino al client. La risorsa viene richiesta da un server di origine o da un server shield di origine. Se il server di origine o il server shield di origine restituiscono un asset, esso viene fornito al client e memorizzato nella cache sul client e sul server perimetrale. In caso contrario, viene restituito un codice di stato diverso da 200 (ad esempio, 403 - Accesso negato o 404 - Non trovato).
* TCP_EXPIRED_HIT: questo stato viene segnalato quando una richiesta avente per destinazione un asset con un tempo TTL scaduto è stata servita direttamente dal POP al client. Ad esempio, quando la durata max. della risorsa è scaduta. 
  
    Una richiesta scaduta in genere comporta una richiesta di riconvalida al server di origine. Affinché si verifichi un TCP_EXPIRED _HIT, il server di origine deve indicare che non esiste una versione più recente dell'asset. Questa situazione comporta in genere un aggiornamento delle intestazioni Cache-Control e Expires della risorsa.
* _MISS TCP_EXPIRED: questo stato viene segnalato quando una versione più recente di un asset memorizzato nella cache scaduto viene servita dal POP al client. Ciò si verifica quando il valore TTL per un asset memorizzato nella cache è scaduto (ad esempio, durata max. scaduta) e il server di origine restituisce una versione più recente dell'asset. Questa nuova versione dell'asset viene servita al client al posto della versione memorizzata nella cache. Inoltre, viene memorizzata nel server perimetrale e nel client.
* CONFIG_NOCACHE: questo stato indica che una configurazione specifici del cliente sul nostro POP edge ha impedito la memorizzazione nella cache dell'asset.
* NONE: questo stato indica che un controllo dell’aggiornamento del contenuto della cache non è stato eseguito.
* TCP_ CLIENT_REFRESH _MISS: questo stato viene segnalato quando un client HTTP (ad esempio un browser) forza un POP edge a recuperare una nuova versione di un asset non aggiornato dal server di origine.
  
    Per impostazione predefinita, i nostri server impediscono a un client HTTP di forzare i nostri server perimetrali per recuperare una nuova versione dell'asset dal server di origine.
* PARTIAL_HIT TCP_: questo stato viene segnalato quando un richiesta di un intervallo di byte genera un riscontro per un asset parzialmente memorizzata nella cache. L'intervallo di byte richiesto viene immediatamente servito dal POP al client.
* UNCACHEABLE: questo stato viene segnalato quando le intestazioni  Cache-Control ed Expires di un asset indicano che non deve essere memorizzato nella cache in un POP o dal client HTTP. Questi tipi di richieste vengono gestiti dal server di origine.

## <a name="cache-hit-ratio"></a>Percentuale riscontri cache
Questo report indica la percentuale di richieste memorizzate nella cache che sono state servite direttamente dalla cache.

Il report fornisce i dettagli seguenti:

* Il contenuto richiesto è stato memorizzato nella cache nel POP più vicino al richiedente.
* La richiesta è stata gestita direttamente dall’edge della nostra rete.
* La richiesta non ha richiesto la riconvalida con il server di origine.

Il report non include:

* Richieste rifiutate a causa di opzioni di filtro di paese.
* Richieste di asset le cui intestazioni indicano che non devono essere memorizzate nella cache. Ad esempio, le intestazioni Cache-Control: private, Cache-Control: no-cache o Pragma: no-cache impediscono la memorizzazione nella cache di un asset.
* Richieste di intervallo di byte di contenuti parzialmente memorizzati nella cache.

La formula è: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate, quindi scegliere **Vai** per assicurarsi che la selezione venga aggiornata.
* È possibile esportare e scaricare i dati facendo clic sull'icona del foglio di Excel che si trova accanto a **Vai**.

![Report percentuale riscontri cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Dati trasferiti IPv4/IPV6
Questo report mostra la distribuzione dell’utilizzo del traffico in IPV4 e IPV6.

![Dati trasferiti IPv4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Selezionare l'intervallo di date per visualizzare i dati di oggi/questa settimana/questo mese e così via, oppure immettere date personalizzate.
* Quindi, fare clic su **Vai** per assicurarsi che la selezione venga aggiornata.

## <a name="considerations"></a>Considerazioni
I report possono essere generati solo negli ultimi 18 mesi.

