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
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161721"
---
# <a name="paper-entity"></a>Entità Paper

<sub>* Gli attributi seguenti sono specifici dell'entità paper. (Ty =' 0')</sub>

name | Description | Type | Operazioni
--- | --- | --- | ---
AA.AfId | ID di affiliazione autore | Int64 | Uguale a
AA.AfN | Nome di affiliazione autore | Stringa | Uguale a, StartsWith
AA.AuId | ID autore | Int64 | Uguale a
AA.AuN | Nome dell'autore normalizzato | Stringa | Uguale a, StartsWith
AA. DAuN | Nome autore originale | Stringa | Nessuno
AA. DAfN | Nome affiliazione originale | Stringa | Nessuno
AA.S | Posizione numerica nell'elenco degli autori | Int32 | Uguale a
CC | Numero delle citazioni | Int32 | Nessuno  
C.CId | ID della serie di conferenze | Int64 | Uguale a
C.CN | Nome della serie di conferenze | Stringa | Uguale a, StartsWith
D | Data di pubblicazione nel formato AAAA-MM-GG | Data | Equals, tra
E | Metadati estesi (vedere tabella riportata di seguito) | Stringa | N/D  
ECC | Numero di citazioni stimato | Int32 | Nessuno
F. DFN | Campo originale del nome dello studio | Stringa | Nessuno
F.FId | ID del campo di studi | Int64 | Uguale a
F.FN | Campo normalizzato del nome dello studio | Stringa | Uguale a, StartsWith
ID | ID carta | Int64 | Uguale a
J.JId | ID giornale di registrazione | Int64 | Uguale a
J.JN | Nome del giornale di registrazione | Stringa | Uguale a, StartsWith
PT | Tipo di pubblicazione (0: sconosciuto, 1: articolo Journal, 2: brevetto, 3: giornale conferenza, 4: capitolo libro, 5: libro, 6: voce riferimento libro, 7: set di dati, 8: repository | Stringa | Uguale a
RId | Elenco di ID carta di riferimento | Int64[] | Uguale a
Ti | Titolo normalizzato | Stringa | Uguale a, StartsWith
W | Parole univoche nel titolo | String[] | Uguale a
S | Anno pubblicato | Int32 | Equals, tra

## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

name | Description               
--- | ---
BT | Tipo di documento BibTex (' a': articolo Journal,' b ': Book,' c': Book Chapter,' p ': Conference Paper)
BV | Nome della sede BibTex
CC | Contesti di citazione: elenco di ID carta di riferimento e contesto corrispondente nel documento (ad esempio, [{123: ["le volpi brune sono note per il salto come indicato nel documento 123" "," i cani Lazy sono un nome di stato cronologico, come illustrato nella carta 123 "]})
DN | Titolo originale carta
DOI | Digital Object Identifier - identificatore di oggetto digitale
FP | Prima pagina di documento nella pubblicazione
I | Problema di pubblicazione
IA | Inverted Abstract - astratto invertito
IA.IndexLength | Numero di elementi nell'indice (conteggio delle parole dell'astratto)
IA.InvertedIndex | Elenco di parole astratte e relativa posizione corrispondente nell'abstract originale, ad esempio [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}]
LP | Ultima pagina di documento della pubblicazione
PB | Editore
S | Fonti - elenco di fonti web del documento, ordinate per pertinenza statica
S.Ty | Tipo di fonte (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U | URL della fonte
V | Volume pubblicazione
VFN | Nome completo del journal o della sede della conferenza
VSN | Nome breve del journal o della sede della conferenza
