---
title: Progettare per la lingua - QnA Maker
description: La risorsa QnA Maker e tutte le knowledge base all'interno di tale risorsa supportano un'unica lingua. La lingua singola è necessaria per fornire i risultati migliori per una query.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843422"
---
# <a name="design-knowledge-base-for-content-language"></a>Progettare la Knowledge Base per il linguaggio dei contenuti

La risorsa QnA Maker e tutte le knowledge base all'interno di tale risorsa supportano un'unica lingua. La lingua singola è necessaria per fornire i risultati migliori per una query.

## <a name="single-language-per-resource"></a>Lingua singola per risorsa

Considerazioni su QnA Maker per il supporto linguistico:

* Un servizio QnA Maker e tutte le sue basi di conoscenza supportano una sola lingua.
* La lingua viene impostata in modo esplicito quando viene creata la prima Knowledge Base del servizio
* La lingua viene determinata dai file e dagli URL aggiunti quando viene creata la Knowledge Base
* La lingua non può essere modificata per altre knowledge base nel servizio
* Il linguaggio viene utilizzato dal servizio di ricerca cognitiva (#1 più affiancata) e dal servizio QnA Maker (#2 più affiancato) per generare la risposta migliore a una query

## <a name="supporting-multiple-languages"></a>Supporto di più lingue

Se è necessario supportare un sistema di Knowledge Base, che include diverse lingue, è possibile scegliere uno dei seguenti metodi:

* Utilizzare il [servizio Testo di traduzione](../../translator/translator-info-overview.md) per tradurre una domanda in un'unica lingua prima di inviare la domanda alla Knowledge Base. Ciò consente di concentrarsi sulla qualità di una singola lingua e sulla qualità delle domande e risposte alternative.
* Creare una risorsa QnA Maker e una Knowledge Base all'interno di tale risorsa per ogni lingua. In questo modo è possibile gestire domande alternative separate e rispondere a testo più sfumato per ogni lingua. Ciò offre una maggiore flessibilità, ma richiede un costo di manutenzione molto più elevato quando le domande o le risposte cambiano in tutte le lingue.

Esaminare [le lingue supportate](../overview/language-support.md) per QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Supporta ogni lingua con una risorsa QnA MakerSupport each language with a QnA Maker resource

* Creare una risorsa QnA Maker per ogni linguaCreate a QnA Maker resource for every language
* Aggiungi solo file e URL per quella lingua
* Utilizzare una convenzione di denominazione per la risorsa per identificare la lingua. Un esempio `qna-maker-fr` è per tutte le basi di conoscenza per i documenti francesi

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](query-knowledge-base.md) come eseguire una query sulla Knowledge Base per una risposta.