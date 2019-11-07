---
title: Azioni del motore della rete CDN di Azure dalle regole standard Microsoft | Microsoft Docs
description: Documentazione di riferimento per le azioni del motore per la rete CDN di Azure da Microsoft standard.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615988"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azioni del motore della rete CDN di Azure dalle regole standard Microsoft

Questo articolo elenca le descrizioni dettagliate delle azioni disponibili per la rete per la distribuzione di contenuti (CDN) di Azure del [motore delle regole standard](cdn-standard-rules-engine.md)di Microsoft.

La seconda parte di una regola è un'azione. Un'azione definisce il comportamento applicato al tipo di richiesta identificato da un set di condizioni di corrispondenza.

## <a name="actions"></a>Azioni

Le azioni seguenti sono disponibili per l'utilizzo. 

## <a name="cache-expiration"></a>Scadenza cache

Questa azione consente di sovrascrivere la durata (TTL) dell'endpoint per le richieste specificate dalle condizioni di corrispondenza delle regole.

**Campi obbligatori**

Comportamento della cache |                
---------------|----------------
Ignora cache | Quando questa opzione è selezionata e la regola corrisponde, il contenuto non verrà memorizzato nella cache.
Override | Quando questa opzione è selezionata e la regola corrisponde, il valore TTL restituito dall'origine verrà sovrascritto con il valore specificato nell'azione.
Imposta se mancante | Quando questa opzione è selezionata e la regola corrisponde, se non è stato restituito alcun valore TTL dall'origine, la regola imposterà la durata (TTL) sul valore specificato nell'azione.

**Campi aggiuntivi**

Days | Ore | Minuti | Secondi
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Stringa di query della chiave della cache

Questa azione consente di modificare la chiave della cache in base alle stringhe di query.

**Campi obbligatori**

Comportamento | Descrizione
---------|------------
Includi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri verranno incluse durante la generazione della chiave di cache. 
Memorizza nella cache tutti gli URL univoci | Quando questa opzione è selezionata e la regola corrisponde, ogni URL univoco avrà una propria chiave di cache. 
Escludi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri verranno escluse durante la generazione della chiave di cache.
Ignora stringhe di query | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query non verranno prese in considerazione durante la generazione della chiave di cache. 

## <a name="modify-request-header"></a>Modificare l'intestazione della richiesta

Questa azione consente di modificare le intestazioni presenti nelle richieste inviate all'origine.

**Campi obbligatori**

Azione | Nome intestazione HTTP | Valore
-------|------------------|------
Append | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in nome intestazione verrà aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore verrà aggiunto al valore esistente. | String
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in nome intestazione verrà aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore sovrascriverà il valore esistente. | String
Elimina | Quando questa opzione è selezionata e la regola corrisponde e l'intestazione specificata nella regola è presente, verrà eliminata dalla richiesta. | String

## <a name="modify-response-header"></a>Modifica intestazione risposta

Questa azione consente di modificare le intestazioni presenti nelle risposte restituite ai client finali

**Campi obbligatori**

Azione | Nome intestazione HTTP | Valore
-------|------------------|------
Append | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in nome intestazione verrà aggiunta alla risposta con il valore specificato. Se l'intestazione è già presente, il valore verrà aggiunto al valore esistente. | String
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in nome intestazione verrà aggiunta alla risposta con il valore specificato. Se l'intestazione è già presente, il valore sovrascriverà il valore esistente. | String
Elimina | Quando questa opzione è selezionata e la regola corrisponde e l'intestazione specificata nella regola è presente, verrà eliminata dalla risposta. | String

## <a name="url-redirect"></a>Reindirizzamento URL

Questa azione consente di reindirizzare i client finali a un nuovo URL. 

**Campi obbligatori**

Campo | Descrizione 
------|------------
Tipo | Selezionare il tipo di risposta che verrà restituito al richiedente. Le opzioni disponibili sono-302, 301 spostati, 307 Reindirizzamento temporaneo e 308 Reindirizzamento permanente
Protocollo | Richiesta di corrispondenza, HTTP o HTTPS
Nome host | Selezionare il nome host a cui verrà reindirizzata la richiesta. Lasciare vuoto per conservare l'host in ingresso.
path | Definire il percorso da utilizzare nel reindirizzamento. Lasciare vuoto per conservare il percorso in arrivo.  
Stringa di query | Definire la stringa di query utilizzata nel reindirizzamento. Lasciare vuoto per mantenere la stringa di query in ingresso. 
Frammento | Definire il frammento da usare nel reindirizzamento. Lasciare vuoto per conservare il frammento in ingresso. 

È consigliabile usare un URL assoluto, poiché l'uso di un URL relativo può reindirizzare gli URL CDN su un percorso non valido. 

## <a name="url-rewrite"></a>Riscrittura URL

Questa azione consente di riscrivere il percorso di una richiesta in route all'origine.

**Campi obbligatori**

Campo | Descrizione 
------|------------
Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare "/" come valore del modello di origine.
Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il modello di origine verrà sovrascritto
Mantieni percorso senza corrispondenza | In caso affermativo, il percorso rimanente dopo il modello di origine verrà aggiunto al nuovo percorso di destinazione. 


[Torna all'inizio](#actions)

</br>

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole](cdn-standard-rules-engine-match-conditions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
