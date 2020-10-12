---
title: Progettazione per QnA Maker lingua
description: La risorsa QnA Maker e tutte le Knowledge base all'interno di tale risorsa supportano un'unica lingua. Il linguaggio singolo è necessario per fornire i risultati ottimali per la risposta a una query.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777004"
---
# <a name="design-knowledge-base-for-content-language"></a>Progettazione della Knowledge base per la lingua del contenuto

La risorsa QnA Maker e tutte le Knowledge base all'interno di tale risorsa supportano una sola lingua. Il linguaggio singolo è necessario per fornire i risultati ottimali per la risposta a una query.

## <a name="single-language-per-resource"></a>Singola lingua per risorsa

QnA Maker considerazioni sul supporto delle lingue:

* Un servizio QnA Maker e tutte le relative Knowledge base supportano solo una lingua.
* La lingua viene impostata in modo esplicito al momento della creazione della prima Knowledge base del servizio
* Il linguaggio è determinato dai file e dagli URL aggiunti quando viene creata la Knowledge base
* Non è possibile modificare la lingua per altre Knowledge base nel servizio
* Il linguaggio viene usato dal servizio ricerca cognitiva (Ranker #1) e dal servizio QnA Maker (Ranker #2) per generare la risposta migliore a una query

## <a name="supporting-multiple-languages"></a>Supporto di più lingue

Se è necessario supportare un sistema Knowledge base, che include diverse lingue, è possibile scegliere uno dei metodi seguenti:

* Utilizzare il [servizio traduzione testuale](../../translator/translator-info-overview.md) per tradurre una domanda in una sola lingua prima di inviare la domanda alla Knowledge base. In questo modo è possibile concentrarsi sulla qualità di una singola lingua e sulla qualità delle domande e risposte alternative.
* Creare una risorsa di QnA Maker e una Knowledge base all'interno di tale risorsa per ogni lingua. In questo modo è possibile gestire le domande alternative separate e il testo di risposta più sfumato per ogni lingua. In questo modo si ottiene una maggiore flessibilità, ma è necessario un costo di manutenzione molto più elevato quando le domande o le risposte cambiano in tutte le lingue.

Esaminare le [lingue supportate](../overview/language-support.md) per QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Supportare ogni lingua con una risorsa QnA Maker

* Creare una risorsa QnA Maker per ogni lingua
* Aggiungi solo file e URL per tale lingua
* Usare una convenzione di denominazione per la risorsa per identificare la lingua. Un esempio è `qna-maker-fr` relativo a tutte le Knowledge base per i documenti in francese

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [concetti](query-knowledge-base.md) relativi all'esecuzione di una query sulla Knowledge base per una risposta.