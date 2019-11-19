---
title: Attributi dell'entità Paper - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Paper nell'API Academic Knowledge.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123072"
---
# <a name="paper-entity"></a>Entità Paper

> [!NOTE]
> Gli attributi seguenti sono specifici dell'entità paper. (Ty =' 0')

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
AA.AfId | ID di affiliazione autore | Int64 | Uguale a
AA.AfN | Nome di affiliazione autore | String | Uguale a, StartsWith
AA.AuId | ID autore | Int64 | Uguale a
AA.AuN | Nome dell'autore normalizzato | String | Uguale a, StartsWith
AA. DAuN | Nome autore originale | String | nessuno
AA. DAfN | Nome affiliazione originale | String | nessuno
AA.S | Posizione numerica nell'elenco degli autori | Int32 | Uguale a
BT | Tipo di documento BibTex (' a': articolo Journal,' b ': Book,' c': Book Chapter,' p ': Conference Paper) | String | nessuno
BV | Nome della sede BibTex | String | nessuno
C.CId | ID della serie di conferenze | Int64 | Uguale a
C.CN | Nome della serie di conferenze | String | Uguale a, StartsWith
CC | Numero delle citazioni | Int32 | nessuno  
CitCon | Contesti di citazione</br></br>Elenco di ID carta di riferimento e contesto corrispondente nel documento (ad esempio, [{123: ["le volpi marroni sono note per il salto come indicato nel documento 123" "," i cani Lazy sono un nome di riferimento cronologico come illustrato in carta 123 "]}) | Personalizzate | nessuno
D | Data di pubblicazione nel formato AAAA-MM-GG | Date | Equals, tra
DN | Titolo originale carta | String | nessuno
DOI | Digital Object Identifier - identificatore di oggetto digitale | String | Uguale a, StartsWith
E | Metadati estesi</br></br>**Importante**: questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA | [Estesa](#extended) | nessuno
ECC | Numero di citazioni stimato | Int32 | nessuno
F. DFN | Campo originale del nome dello studio | String | nessuno
F.FId | ID del campo di studi | Int64 | Uguale a
F.FN | Campo normalizzato del nome dello studio | String | Uguale a, StartsWith
FP | Prima pagina di documento nella pubblicazione | String | Uguale a
I | Problema di pubblicazione | String | Uguale a
IA | Abstract invertito | [InvertedAbstract](#invertedabstract) | nessuno
id | ID carta | Int64 | Uguale a
J.JId | ID giornale di registrazione | Int64 | Uguale a
J.JN | Nome del giornale di registrazione | String | Uguale a, StartsWith
LP | Ultima pagina di documento della pubblicazione | String | Uguale a
PB | Autore | String | nessuno
PT | Tipo di pubblicazione (0: sconosciuto, 1: articolo Journal, 2: brevetto, 3: giornale conferenza, 4: capitolo libro, 5: libro, 6: voce riferimento libro, 7: set di dati, 8: repository | String | Uguale a
RId | Elenco di ID carta di riferimento | Int64[] | Uguale a
S | Elenco degli URL di origine del documento, ordinati per pertinenza | [Origine](#source)[] | nessuno
Ti | Titolo normalizzato | String | Uguale a, StartsWith
V | Volume pubblicazione | String | Uguale a
VFN | Nome completo del journal o della sede della conferenza | String | nessuno
VSN | Nome breve del journal o della sede della conferenza | String | nessuno
W | Parole univoche nel titolo | String[] | Uguale a
S | Anno pubblicato | Int32 | Equals, tra

## <a name="extended"></a>Estesa
> [!IMPORTANT]
> Questo attributo è stato deprecato ed è supportato solo per le applicazioni legacy. La richiesta di questo attributo singolarmente (ad esempio, Attributes = ID, ti, E) comporterà la restituzione di tutti gli attributi di metadati estesi in una *stringa JSON serializzata*</br></br>Tutti gli attributi contenuti nei metadati estesi sono ora disponibili come attributo di primo livello e possono essere richiesti come tali, ad esempio attributi = ID, ti, DOI, IA

> [!IMPORTANT]
> Supporto per la richiesta di singoli attributi estesi utilizzando "E". l'ambito, ovvero "E. DN", verrà deprecato. Sebbene questo sia ancora tecnicamente supportato, richiedendo singoli attributi estesi utilizzando "E". l'ambito comporterà la restituzione del valore dell'attributo in due posizioni nella risposta JSON, come parte dell'oggetto "E" e come attributo di primo livello.

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
BT | Tipo di documento BibTex (' a': articolo Journal,' b ': Book,' c': Book Chapter,' p ': Conference Paper) | String | nessuno
BV | Nome della sede BibTex | String | nessuno
CC | Contesti di citazione</br></br>Elenco di ID carta di riferimento e contesto corrispondente nel documento (ad esempio, [{123: ["le volpi marroni sono note per il salto come indicato nel documento 123" "," i cani Lazy sono un nome di riferimento cronologico come illustrato in carta 123 "]}) | Personalizzate | nessuno
DN | Titolo originale carta | String | nessuno
DOI | Digital Object Identifier - identificatore di oggetto digitale | String | nessuno
FP | Prima pagina di documento nella pubblicazione | String | nessuno
I | Problema di pubblicazione | String | nessuno
IA | Inverted Abstract - astratto invertito | [InvertedAbstract](#invertedabstract) | nessuno
LP | Ultima pagina di documento della pubblicazione | String | nessuno
PB | Autore | String | nessuno
S | Elenco degli URL di origine del documento, ordinati per pertinenza | [Origine](#source)[] | nessuno
V | Volume pubblicazione | String | nessuno
VFN | Nome completo del journal o della sede della conferenza | String | nessuno
VSN | Nome breve del journal o della sede della conferenza | String | nessuno

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Gli attributi InvertedAbstract non possono essere richiesti direttamente come attributo return. Se è necessario un singolo attributo, è necessario richiedere l'attributo "IA" di livello superiore, ad esempio per ottenere IA. Attributi della richiesta IndexLength = IA

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
IndexLength | Numero di elementi nell'indice (conteggio delle parole dell'astratto) | Int32 | nessuno
InvertedIndex | Elenco di parole astratte e relativa posizione corrispondente nell'abstract originale, ad esempio [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}] | Personalizzate | nessuno

## <a name="source"></a>Source

> [!IMPORTANT]
> Gli attributi di origine non possono essere richiesti direttamente come attributo restituito. Se è necessario un singolo attributo, è necessario richiedere l'attributo "S" di livello superiore, ad esempio per ottenere gli attributi della richiesta S. U = S

Nome | DESCRIZIONE | digitare | Operazioni
--- | --- | --- | ---
Ty | Tipo di URL di origine (1: HTML, 2: testo, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | String | nessuno
U | URL della fonte | String | nessuno
