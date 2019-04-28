---
title: Attributi dell'entità Paper - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sugli attributi che è possibile usare con l'entità Paper nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: bd37665e962ada59149b54075d7f8acbea895c50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336825"
---
# <a name="paper-entity"></a>Entità Paper

<sub> *Gli attributi seguenti sono specifici dell'entità Paper. (Ty = '0') </sub>


NOME    |DESCRIZIONE                                        |Type       | Operazioni
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |ID entità                                          |Int64      |Uguale a
Ti      |Titolo Paper                                        |string     |Uguale a<br/>StartsWith
L       |Codice lingua Paper separato da "\@@@"            |string     |Uguale a
S       |Anno Paper                                         |Int32      |Uguale a<br/>IsBetween
D       |Data Paper                                         |Data       |Uguale a<br/>IsBetween
CC      |Numero delle citazioni                                     |Int32      |Nessuno  
ECC     |Numero stimato delle citazioni                           |Int32      |Nessuno
AA.AuN  |Nome autore                                        |string     |Uguale a<br/>StartsWith
AA.AuId |ID autore                                          |Int64      |Uguale a
AA.AfN  |Nome di affiliazione autore                            |string     |Uguale a<br/>StartsWith
AA.AfId |ID di affiliazione autore                              |Int64      |Uguale a
AA.S    |Ordine autore per il documento                         |Int32      |Uguale a
F.FN    |Nome del campo di studi                                |string     |Uguale a<br/>StartsWith
F.FId   |ID del campo di studi                                  |Int64      |Uguale a
J.JN    |Nome del giornale di registrazione                                       |string     |Uguale a<br/>StartsWith
J.JId   |ID giornale di registrazione                                         |Int64      |Uguale a
C.CN    |Nome della serie di conferenze                             |string     |Uguale a<br/>StartsWith
C.CId   |ID della serie di conferenze                               |Int64      |Uguale a
RId     |ID dei documenti referenziati                              |Int64[]    |Uguale a
W       |Parole dal titolo del documento e Astratto                |String[]   |Uguale a
E       |Metadati estesi (vedere tabella riportata di seguito)                |string     |Nessuno  
        


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

NOME    | DESCRIZIONE               
--------|---------------------------    
DN      | Nome visualizzato del documento 
S       | Fonti - elenco di fonti web del documento, ordinate per pertinenza statica
S.Ty    | Tipo di fonte (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | URL della fonte
VFN     | Venue Full Name - nome completo del giornale di registrazione o della conferenza
VSN     | Venue Short Name - nome abbreviato del giornale di registrazione o della conferenza
V       | Volume - volume del giornale di registrazione
BV      | Nome del giornale di registrazione
BT      | 
PB      | Abbreviazioni di registrazioni
I       | Issue - numero del giornale di registrazione
FP      | FirstPage - prima pagina del documento
LP      | LastPage - ultima pagina del documento
DOI     | Digital Object Identifier - identificatore di oggetto digitale
CC      | Citation Contexts – elenco degli ID dei documenti referenziati e il relativo contesto nel documento (ad esempio [{123:["le volpi marroni sono famose per il loro salto, come indicato nel documento 123", "cani pigri rappresenta un termine storico improprio, come mostrato nel documento 123”]})
IA      | Inverted Abstract - astratto invertito
IA.IndexLength| Numero di elementi nell'indice (conteggio delle parole dell'astratto)
IA.InvertedIndex| Elenco di parole dell'astratto e la relativa posizione nell'astratto originale (ad esempio, [{"la": [0, 15, 30]}, {"volpe": [1]}, {"marrone":[2]}])
