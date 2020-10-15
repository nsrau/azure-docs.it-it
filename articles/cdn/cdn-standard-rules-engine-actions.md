---
title: Azioni nel motore delle regole standard per la rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le azioni nel motore delle regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760125"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azioni nel motore delle regole standard per la rete CDN di Azure

Nel [motore delle regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure), una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle azioni che è possibile usare nel motore delle regole standard per la rete CDN di Azure.

La seconda parte di una regola è un'azione. Un'azione definisce il comportamento applicato al tipo di richiesta identificata da una o più condizioni di corrispondenza.

## <a name="actions"></a>Azioni

Le azioni seguenti sono disponibili per l'uso nel motore delle regole standard per la rete CDN di Azure. 

### <a name="cache-expiration"></a>Ora di scadenza della cache

Usare questa azione per sovrascrivere il valore TTL (time to Live) dell'endpoint per le richieste specificate dalle condizioni di corrispondenza delle regole.

#### <a name="required-fields"></a>Required fields

Comportamento cache |  Descrizione              
---------------|----------------
Ignora cache | Quando questa opzione è selezionata e la regola corrisponde, il contenuto non viene memorizzato nella cache.
Sostituisci | Quando questa opzione è selezionata e la regola corrisponde, il valore TTL restituito dall'origine viene sovrascritto con il valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Per l'intestazione della risposta Cache-Control con i valori "no-cache", "private", "No-Store", l'azione non sarà applicabile.
Imposta se mancante | Quando questa opzione è selezionata e la regola corrisponde, se non è stato restituito alcun valore TTL dall'origine, la regola imposta la durata (TTL) sul valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Per l'intestazione della risposta Cache-Control con i valori "no-cache", "private", "No-Store", l'azione non sarà applicabile.

#### <a name="additional-fields"></a>Altri campi

Giorni | Ore | Minuti | Secondi
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Stringa di query della chiave della cache

Usare questa azione per modificare la chiave della cache in base alle stringhe di query.

#### <a name="required-fields"></a>Required fields

Comportamento | Descrizione
---------|------------
Includi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono incluse quando viene generata la chiave di cache. 
Memorizza nella cache tutti gli URL univoci | Quando questa opzione è selezionata e la regola corrisponde, ogni URL univoco ha una propria chiave di cache. 
Escludi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono escluse quando viene generata la chiave di cache.
Ignora stringhe di query | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query non vengono considerate quando viene generata la chiave di cache. 

### <a name="modify-request-header"></a>Modifica intestazione richiesta

Usare questa azione per modificare le intestazioni presenti nelle richieste inviate all'origine.

#### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Se questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto a quello esistente. | string
Overwrite | Se questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive quello esistente. | string
Delete | Se questa opzione è selezionata, la regola corrisponde e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla richiesta. | string

### <a name="modify-response-header"></a>Modifica intestazione risposta

Usare questa azione per modificare le intestazioni presenti nelle risposte restituite ai client.

#### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Se questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta con il **valore** specificato. Se l'intestazione è già presente, il **valore** viene aggiunto a quello esistente. | string
Overwrite | Se questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta con il **valore** specificato. Se l'intestazione è già presente, il **valore** sovrascrive quello esistente. | string
Delete | Se questa opzione è selezionata, la regola corrisponde e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla risposta. | string

### <a name="url-redirect"></a>Reindirizzamento URL

Usare questa azione per reindirizzare i client a un nuovo URL. 

#### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Type | Selezionare il tipo di risposta da restituire al richiedente: Trovato (302), Spostato (301), Reindirizzamento temporaneo (307) e Reindirizzamento permanente (308).
Protocollo | Corrispondenza richiesta, HTTP, HTTPS.
nomehost | Selezionare il nome host a cui reindirizzare la richiesta. Lasciare vuoto questo campo per mantenere l'host in ingresso.
Percorso | Definire il percorso da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il percorso in ingresso.  
Stringa di query | Definire la stringa di query usata nel reindirizzamento. Lasciare vuoto questo campo per mantenere la stringa di query in ingresso. 
Frammento | Definire il frammento da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il frammento in ingresso. 

Si consiglia vivamente di utilizzare un URL assoluto. L'uso di un URL relativo può reindirizzare gli URL della rete CDN di Azure a un percorso non valido 

### <a name="url-rewrite"></a>Riscrittura URL

Usare questa azione per riscrivere il percorso di una richiesta instradata all'origine.

#### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare una barra ( **/** ) come valore del modello di origine.
Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine.
Mantieni percorso senza corrispondenza | Se impostato su **Sì**, il percorso rimanente dopo il modello di origine viene aggiunto al nuovo percorso di destinazione. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
