---
title: Glossario - QnA Maker
titleSuffix: Azure Cognitive Services
description: Il servizio QnA Maker presenta molti termini nuovi da machine learning ed elaborazione del linguaggio naturale, nonché termini specifici del servizio. Questo elenco sarà d'aiuto per comprendere tali termini.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: e86ea22208ccd08c0268fda0cc47d5127b4568a1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226469"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Glossario per la knowledge base e il servizio QnA Maker

## <a name="qna-maker-service"></a>Servizio QnA Maker
Un servizio QnA Maker è un prerequisito per iniziare a usare QnA Maker. L'acquisto di un piano QnA Maker consente di configurare le risorse nella sottoscrizione di Azure per la creazione e la gestione della Knowledge Base. Ogni account utente QnA Maker può creare più servizi QnA Maker nella propria sottoscrizione di Azure.

## <a name="knowledge-base"></a>Knowledge Base
Una Knowledge Base è il repository di domande e risposte create, gestite e usate tramite QnA Maker. Ogni livello di QnA Maker può essere usato per più Knowledge Base.

## <a name="endpoint"></a>Endpoint
Endpoint HTTP basato su REST che serve i contenuti della Knowledge Base che possono essere integrati nell'applicazione, comunemente un chat bot. 

## <a name="test-knowledge-base"></a>Knowledge Base di test
Una Knowledge Base ha due stati, test e pubblicata. La Knowledge Base di test è la versione che viene modificata, salvata e testata per verificare l'accuratezza e la completezza delle risposte. Le modifiche apportate alla Knowledge Base di test non interessano l'utente finale dell'applicazione o del chat bot.

## <a name="published-knowledge-base"></a>Knowledge Base pubblicata
Una Knowledge Base ha due stati, test e pubblicata.  La Knowledge Base pubblicata è la versione che viene usata nel chat bot o nell'applicazione. L'azione di pubblicazione di una Knowledge Base inserisce il contenuto della Knowledge Base di test nella versione pubblicata. Poiché la Knowledge Base pubblicata è la versione usata dall'applicazione tramite l'endpoint, è necessario assicurarsi che il contenuto sia corretto e ben testato.

## <a name="query"></a>Query
Una query utente è la domanda che l'utente finale o il tester pone alla Knowledge Base. La query è spesso in un formato di linguaggio naturale o con alcune parole chiave che rappresentano la domanda.

## <a name="response"></a>Risposta
Risposta recuperata dalla Knowledge Base, basata sulla migliore corrispondenza per una determinata query dell'utente.

## <a name="confidence-score"></a>Punteggio di attendibilità
Il punteggio di attendibilità della risposta è un valore numerico compreso tra 0 e 100, dove 100 indica la corrispondenza esatta tra la query dell'utente e una domanda nella Knowledge Base e che la risposta fornita è la risposta corretta e appropriata per una determinata query dell'utente. Le risposte sono generalmente classificate in base al punteggio di attendibilità e quella con il punteggio più alto viene fornita come risposta predefinita.
