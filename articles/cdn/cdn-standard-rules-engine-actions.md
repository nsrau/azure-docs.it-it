---
title: Azioni nel motore delle regole standard per la rete CDN di Azure | Microsoft Docs
description: Documentazione di riferimento per le azioni nel motore delle regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74171640"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azioni nel motore delle regole standard per la rete CDN di Azure

Nel [motore delle regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure), una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle azioni che è possibile usare nel motore delle regole standard per la rete CDN di Azure.

La seconda parte di una regola è un'azione. Un'azione definisce il comportamento applicato al tipo di richiesta identificata da una condizione di corrispondenza o da un set di condizioni di corrispondenza.

## <a name="actions"></a>Actions

Le azioni seguenti sono disponibili per l'uso nel motore delle regole standard per la rete CDN di Azure. 

### <a name="cache-expiration"></a>Ora di scadenza della cache

Usare questa azione per sovrascrivere il valore TTL (time to Live) dell'endpoint per le richieste specificate dalle condizioni di corrispondenza delle regole.

#### <a name="required-fields"></a>Campi obbligatori

Comportamento della cache |  DESCRIZIONE              
---------------|----------------
Ignora cache | Quando questa opzione è selezionata e la regola corrisponde, il contenuto non viene memorizzato nella cache.
Override | Quando questa opzione è selezionata e la regola corrisponde, il valore TTL restituito dall'origine viene sovrascritto con il valore specificato nell'azione.
Imposta se mancante | Quando questa opzione è selezionata e la regola corrisponde, se non è stato restituito alcun valore TTL dall'origine, la regola imposta la durata (TTL) sul valore specificato nell'azione.

#### <a name="additional-fields"></a>Campi aggiuntivi

Days | Ore | Minuti | Secondi
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Stringa di query della chiave della cache

Usare questa azione per modificare la chiave della cache in base alle stringhe di query.

#### <a name="required-fields"></a>Campi obbligatori

Comportamento | DESCRIZIONE
---------|------------
Includi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono incluse quando viene generata la chiave di cache. 
Memorizza nella cache tutti gli URL univoci | Quando questa opzione è selezionata e la regola corrisponde, ogni URL univoco ha una propria chiave di cache. 
Escludi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono escluse quando viene generata la chiave di cache.
Ignora stringhe di query | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query non vengono considerate quando viene generata la chiave di cache. 

### <a name="modify-request-header"></a>Modificare l'intestazione della richiesta

Usare questa azione per modificare le intestazioni presenti nelle richieste inviate all'origine.

#### <a name="required-fields"></a>Campi obbligatori

Azione | Nome intestazione HTTP | Valore
-------|------------------|------
Append | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto al valore esistente. | String
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive il valore esistente. | String
Elimina | Quando questa opzione è selezionata, la regola corrisponde a e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla richiesta. | String

### <a name="modify-response-header"></a>Modifica intestazione risposta

Usare questa azione per modificare le intestazioni presenti nelle risposte restituite ai client.

#### <a name="required-fields"></a>Campi obbligatori

Azione | Nome intestazione HTTP | Valore
-------|------------------|------
Append | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **nome intestazione** viene aggiunta alla risposta usando il **valore**specificato. Se l'intestazione è già presente, il **valore** viene aggiunto al valore esistente. | String
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **nome intestazione** viene aggiunta alla risposta usando il **valore**specificato. Se l'intestazione è già presente, il **valore** sovrascrive il valore esistente. | String
Elimina | Quando questa opzione è selezionata, la regola corrisponde a e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla risposta. | String

### <a name="url-redirect"></a>Reindirizzamento URL

Usare questa azione per reindirizzare i client a un nuovo URL. 

#### <a name="required-fields"></a>Campi obbligatori

Campo | DESCRIZIONE 
------|------------
digitare | Selezionare il tipo di risposta da restituire al richiedente: trovato (302), spostato (301), Reindirizzamento temporaneo (307) e Reindirizzamento permanente (308).
Protocol | Request match, HTTP, HTTPS.
Nome host | Selezionare il nome host a cui si desidera reindirizzare la richiesta. Lasciare vuoto per mantenere l'host in ingresso.
Path | Definire il percorso da usare nel reindirizzamento. Lasciare vuoto per conservare il percorso in arrivo.  
Stringa di query | Definire la stringa di query utilizzata nel reindirizzamento. Lasciare vuoto per mantenere la stringa di query in ingresso. 
Frammento | Definire il frammento da usare nel reindirizzamento. Lasciare vuoto per conservare il frammento in ingresso. 

Si consiglia vivamente di utilizzare un URL assoluto. L'uso di un URL relativo può reindirizzare gli URL della rete CDN di Azure a un percorso non valido 

### <a name="url-rewrite"></a>Riscrittura URL

Usare questa azione per riscrivere il percorso di una richiesta che viene indirizzata all'origine.

#### <a name="required-fields"></a>Campi obbligatori

Campo | DESCRIZIONE 
------|------------
Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare una barra ( **/** ) come valore del modello di origine.
Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine.
Mantieni percorso senza corrispondenza | Se impostato su **Sì**, il percorso rimanente dopo il modello di origine viene aggiunto al nuovo percorso di destinazione. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Riferimento al motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza nel motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
