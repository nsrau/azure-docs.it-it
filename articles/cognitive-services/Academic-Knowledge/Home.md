---
title: Informazioni sull'API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Usare l'API Academic Knowledge per interpretare le query utente e recuperare informazioni dettagliate da Academic Graph.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: 975c36451f0f00ce374a0bbf0a6dd87ec39eb912
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871558"
---
# <a name="academic-knowledge-api"></a>API Academic Knowledge

Il servizio API Academic Knowledge consente di interpretare le query utente con finalità accademiche e recuperare informazioni complete da Microsoft Academic Graph (MAG). La Knowledge Base MAG è un grafo di entità eterogenee di livello Web costituito da entità per la modellazione di attività didattiche: campo di studio, autore, istituto, pubblicazione, sede di eventi ed evento. 

I dati MAG vengono estratti tramite data mining dall'indice Web di Bing, nonché da una Knowledge Base interna da Bing. Grazie alla continua indicizzazione gestita da Bing, questa API conterrà informazioni aggiornate dal Web in base alle operazioni di individuazione e indicizzazione eseguite da Bing. Sulla base di questo set di dati, l'API Academic Knowledge consente un dialogo interattivo basato su Knowledge Base che combina in modo trasparente esperienze di ricerca reattiva e di suggerimento proattivo, risultati di ricerca avanzati nel grafo delle pubblicazioni di ricerca e distribuzioni su istogramma dei valori di attributo per un set di pubblicazioni e le entità correlate.

Per altre informazioni su Microsoft Academic Graph, vedere [http://aka.ms/academicgraph](https://aka.ms/academicgraph).

L'API Academic Knowledge è stata spostata dalla versione di anteprima di Servizi cognitivi a Lab di Servizi cognitivi. La nuova home page per il progetto è: [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). La chiave API esistente continuerà a funzionare fino al 24 maggio 2018. Dopo questa data, generare una nuova chiave API. Si noti che la versione di anteprima a pagamento non sarà più disponibile dopo la scadenza della chiave esistente. Se il livello gratuito dell'API non è sufficiente per gli scopi desiderati, contattare il team Microsoft. 

## <a name="features"></a>Funzionalità
L'API Academic Knowledge è costituita da quattro endpoint REST correlati:  
  1. **interpret**: interpreta una stringa di query utente in linguaggio naturale. Restituisce interpretazioni con note per consentire esperienze avanzate di completamento automatico della casella di ricerca, in grado di prevedere ciò che l'utente sta digitando.  
  2. **evaluate**: valuta un'espressione di query e restituisce risultati di entità Academic Knowledge.  
  3. **calchistogram**: calcola un istogramma della distribuzione dei valori degli attributi per le entità accademiche restituiti da un'espressione di query, ad esempio la distribuzione delle citazioni di un determinato autore in base all'anno.  
  
Usati insieme, questi metodi API consentono la creazione di un'esperienza di ricerca semantica avanzata. Data una stringa di query utente, il metodo **interpret** fornisce una versione con note della query e un'espressione di query strutturata, completando nel frattempo in modo facoltativo la query dell'utente in base alla semantica dei dati accademici sottostanti. Ad esempio, se un utente digita la stringa *latent s*, il metodo **interpret** può fornire un set di interpretazioni classificate, il che suggerisce che l'utente stia cercando il campo di studio *latent semantic analysis*, la pubblicazione *latent structure analysis* o altre espressioni di entità che iniziano con *latent s*. Queste informazioni possono essere usate per consentire all'utente di arrivare rapidamente ai risultati della ricerca desiderati.

Il metodo **evaluate** può essere usato per recuperare dalla Knowledge Base accademica un set di entità pubblicazione corrispondenti, mentre il metodo**calchistogram** può essere usato per calcolare la distribuzione dei valori degli attributi per un set di entità pubblicazione, che è possibile usare per filtrare ulteriormente i risultati della ricerca.        

## <a name="getting-started"></a>Introduzione 
Vedere gli argomenti secondari a sinistra per la documentazione dettagliata.  Si noti che, per migliorare la leggibilità degli esempi, le chiamate alle API REST contengono caratteri (ad esempio, spazi) senza codifica URL.  Il codice dovrà applicare le codifiche URL appropriate.
