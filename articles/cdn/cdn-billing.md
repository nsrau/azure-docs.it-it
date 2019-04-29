---
title: Informazioni sulla fatturazione per la rete CDN di Azure | Microsoft Docs
description: Queste domande frequenti descrivono il funzionamento della fatturazione per la rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782061"
---
# <a name="understanding-azure-cdn-billing"></a>Informazioni sulla fatturazione per la rete CDN di Azure

Queste domande frequenti descrivono la struttura di fatturazione per il contenuto ospitato dalla rete CDN di Azure.

## <a name="what-is-a-billing-region"></a>Che cos'è un'area di fatturazione?
Un'area di fatturazione è un'area geografica usata per determinare la frequenza di addebito per il recapito di oggetti dalla rete CDN di Azure. Le zone di fatturazione correnti e le rispettive aree sono le seguenti:

- Zona 1: America del Nord, Europa, Medio Oriente e Africa

- Zona 2: Asia Pacifico (Giappone incluso)

- Zona 3: America del Sud

- Zona 4: Australia e Nuova Zelanda

- Zona 5: India

Per informazioni sulle aree point-of-presence (POP), vedere [Località POP della rete CDN per area](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Ad esempio, una località POP situata in Messico si trova nell'area dell'America del Nord e pertanto è inclusa nella zona 1. 

Per informazioni sui prezzi della rete CDN di Azure, vedere [Prezzi della rete per la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Come vengono calcolate le spese di spedizione per area?
L'area di fatturazione della rete CDN di Azure è basata sulla posizione del server di origine che fornisce il contenuto all'utente finale. La destinazione (posizione fisica) del client non viene considerata l'area di fatturazione.

Ad esempio, se un utente in Messico emette una richiesta che viene servita da un server situato in una località POP negli Stati Uniti a causa delle condizioni di traffico o peering, l'area di fatturazione sarà quella degli Stati Uniti.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Che cos'è una transazione fatturabile sulla rete CDN di Azure?
Tutte le richieste HTTP(S) che terminano sulla rete CDN sono un evento fatturabile, che include tutti i tipi di risposta: esito positivo, negativo o altro. Tuttavia, diverse risposte possono generare quantità di traffico differenti. Ad esempio, *304 Not Modified* e altre risposte di sola intestazione generano un traffico ridotto perché sono risposte di entità ridotta; in modo analogo, le risposte di errore, come ad esempio *404 Not Found*, sono fatturabili ma implicano un costo minimo dato il payload di risposta ridotto.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Quali altri costi di Azure sono associati all'uso della rete CDN di Azure?
L'uso della rete CDN di Azure comporta anche alcuni addebiti di utilizzo sui servizi usati come origine per gli oggetti. Tali costi rappresentano in genere una percentuale esigua del costo di utilizzo complessivo della rete CDN.

Quando si usa l'archiviazione BLOB di Azure come origine per il contenuto, è necessario considerare anche i seguenti addebiti di archiviazione per il riempimento della cache:

- GB di spazio effettivo usato: spazio di archiviazione effettivo degli oggetti di origine.

- Trasferimenti in GB: quantità di dati trasferiti per riempire la cache della rete CDN.

- Transazioni: in base alle esigenze per riempire la cache.

Per altre informazioni sulla fatturazione di Archiviazione di Azure, vedere [Informazioni sulla fatturazione di archiviazione di Azure (larghezza di banda, transazioni e capacità)](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Quando si usa una *consegna tramite servizio ospitato*, è necessario includere anche le spese seguenti:

- Tempo delle risorse di calcolo di Azure: istanze di calcolo che fungono da origine.

- Trasferimento di calcolo di Azure: trasferimenti di dati dalle istanze di calcolo per riempire le cache della rete CDN di Azure.

Se il client usa richieste di intervallo di byte (indipendentemente dal servizio di origine), si applicano le considerazioni seguenti:

- Una *richiesta di intervallo di byte* è una transazione fatturabile per la rete CDN. Quando un client invia una richiesta di intervallo di byte, la richiesta è per un subset (intervallo) dell'oggetto. La rete CDN risponde solo con una parte del contenuto richiesto. Questa risposta parziale è una transazione fatturabile e la quantità di trasferimento è limitata alle dimensioni della risposta di intervallo (oltre alle intestazioni).

- Quando arriva una richiesta solo per una parte di un oggetto (specificando un'intestazione di intervallo di byte), la rete CDN può recuperare l'intero oggetto nella propria cache. Di conseguenza, anche se la transazione fatturabile dalla rete CDN è per una risposta parziale, la transazione fatturabile dall'origine può includere le dimensioni totali dell'oggetto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Quanta attività di trasferimento viene generata per supportare la cache?
Ogni volta che un POP della rete CDN deve riempire la cache, presenta una richiesta all'origine affinché l'oggetto venga memorizzato nella cache. Di conseguenza, l'origine genera una transazione fatturabile a ogni mancato riscontro nella cache. Il numero di mancati riscontri nella cache dipende da svariati fattori:

- La misura in cui il contenuto è memorizzabile nella cache: se il contenuto presenta valori TTL (time-to-live)/di scadenza elevati ed è usato di frequente nella cache dato l'elevato numero di accessi, la maggior parte del carico viene gestita dalla rete CDN. Un tipico rapporto cache/accesso corretto è ben oltre il 90%, vale a dire che meno del 10% delle richieste del client devono tornare all'origine per un mancato riscontro nella cache o un aggiornamento dell'oggetto.

- Il numero di nodi che devono caricare l'oggetto: ogni volta che un nodo carica un oggetto dall'origine, viene generata una transazione fatturabile. Di conseguenza, contenuti più globali (accesso da più nodi) si traducono in più transazioni fatturabili.

- Influenza (TTL): un TTL maggiore per l'oggetto indica che questo deve essere recuperato dall'origine con una frequenza minore. Esso indica anche che i client, come ad esempio i browser, possono memorizzare nella cache l'oggetto per un tempo più lungo, riducendo le transazioni nella rete CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Ricerca per categorie su come gestire più efficacemente i costi
Impostare la durata TTL massima possibile per il contenuto. 
