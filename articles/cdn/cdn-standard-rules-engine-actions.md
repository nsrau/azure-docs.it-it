---
title: Azioni nel motore delle regole Standard per la rete CDN di Azure Documenti Microsoft
description: Documentazione di riferimento per le azioni nel motore di regole Standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171640"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azioni nel motore regole Standard per la rete CDN di AzureActions in the Standard rules engine for Azure CDN

Nel [motore regole standard](cdn-standard-rules-engine.md) per la rete per la distribuzione di contenuti di Azure una regola è costituita da una o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle azioni che è possibile usare nel motore regole Standard per la rete CDN di Azure.This article provides detailed descriptions of the actions you can use in the Standard rules engine for Azure CDN.

La seconda parte di una regola è un'azione. Un'azione definisce il comportamento applicato al tipo di richiesta identificato da una condizione di corrispondenza o da un set di condizioni di corrispondenza.

## <a name="actions"></a>Azioni

The following actions are available to use in the Standard rules engine for Azure CDN. 

### <a name="cache-expiration"></a>Ora di scadenza della cache

Utilizzare questa azione per sovrascrivere il valore TTL (Time to Live) dell'endpoint per le richieste specificate dalle regole.

#### <a name="required-fields"></a>Required fields

Comportamento della cache |  Descrizione              
---------------|----------------
Cache di bypass | Quando questa opzione è selezionata e la regola corrisponde, il contenuto non viene memorizzato nella cache.
Sostituisci | Quando questa opzione è selezionata e la regola corrisponde, il valore TTL restituito dall'origine viene sovrascritto con il valore specificato nell'azione.
Imposta se mancante | Quando questa opzione è selezionata e la regola corrisponde, se dall'origine non è stato restituito alcun valore TTL, la regola imposta il valore TTL sul valore specificato nell'azione.

#### <a name="additional-fields"></a>Campi aggiuntivi

Days | Ore | Minuti | Secondi
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Stringa di query chiave cache

Utilizzare questa azione per modificare la chiave di cache in base alle stringhe di query.

#### <a name="required-fields"></a>Required fields

Comportamento | Descrizione
---------|------------
Includi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono incluse quando viene generata la chiave di cache. 
Memorizza nella cache tutti gli URL univoci | Quando questa opzione è selezionata e la regola corrisponde, ogni URL univoco ha la propria chiave di cache. 
Escludi | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query specificate nei parametri vengono escluse quando viene generata la chiave di cache.
Ignora stringhe di query | Quando questa opzione è selezionata e la regola corrisponde, le stringhe di query non vengono considerate quando viene generata la chiave di cache. 

### <a name="modify-request-header"></a>Modificare l'intestazione della richiesta

Utilizzare questa azione per modificare le intestazioni presenti nelle richieste inviate all'origine.

#### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto al valore esistente. | string
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive il valore esistente. | string
Delete | Quando questa opzione è selezionata, la regola corrisponde e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla richiesta. | string

### <a name="modify-response-header"></a>Modificare l'intestazione della risposta

Utilizzare questa azione per modificare le intestazioni presenti nelle risposte restituite ai client.

#### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta utilizzando il **valore**specificato. Se l'intestazione è già presente, **Value** viene aggiunto al valore esistente. | string
Overwrite | Quando questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta utilizzando il **valore**specificato. Se l'intestazione è già presente, **Value** sovrascrive il valore esistente. | string
Delete | Quando questa opzione è selezionata, la regola corrisponde e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla risposta. | string

### <a name="url-redirect"></a>Reindirizzamento URL

Utilizzare questa azione per reindirizzare i client a un nuovo URL. 

#### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Type | Selezionare il tipo di risposta da restituire al richiedente: Trovato (302), Spostato (301), Reindirizzamento temporaneo (307) e Reindirizzamento permanente (308).
Protocollo | Richiesta di corrispondenza, HTTP, HTTPS.
nomehost | Selezionare il nome host a cui si desidera reindirizzare la richiesta. Lasciare vuoto per mantenere l'host in ingresso.
Path | Definire il percorso da utilizzare nel reindirizzamento. Lasciare vuoto per mantenere il percorso in ingresso.  
Stringa di query | Definire la stringa di query utilizzata nel reindirizzamento. Lasciare vuoto per mantenere la stringa di query in ingresso. 
Frammento | Definire il frammento da utilizzare nel reindirizzamento. Lasciare vuoto per mantenere il frammento in ingresso. 

Si consiglia vivamente di utilizzare un URL assoluto. L'uso di un URL relativo potrebbe reindirizzare gli URL della rete CDN di Azure a un percorso non valido. 

### <a name="url-rewrite"></a>Riscrittura URL

Utilizzare questa azione per riscrivere il percorso di una richiesta in strada verso l'origine.

#### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con**/** tutti i percorsi URL, utilizzare una barra ( ) come valore del modello di origine.
Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine.
Mantieni percorso non corrispondente | Se impostato su **Sì**, il percorso rimanente dopo l'aggiunta del modello di origine al nuovo percorso di destinazione. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza nel motore regole StandardMatch conditions in the Standard rules engine](cdn-standard-rules-engine-match-conditions.md)
- [Applicare HTTPS usando il motore regole standard](cdn-standard-rules-engine.md)
