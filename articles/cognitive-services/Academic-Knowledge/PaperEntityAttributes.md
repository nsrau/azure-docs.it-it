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
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704948"
---
# <a name="paper-entity"></a>Entità Paper

<sub> *Gli attributi seguenti sono specifici dell'entità Paper. (Ty = '0') </sub>


Name    |DESCRIZIONE                                        |Type       | Operazioni
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |ID entità                                          |Int64      |Equals
Ti      |Titolo Paper                                        |String     |Uguale a<br/>StartsWith
L       |Codice lingua carta separato da "\@"\@\@          |String     |Equals
Y       |Anno Paper                                         |Int32      |Uguale a<br/>IsBetween
D       |Data Paper                                         |Date       |Uguale a<br/>IsBetween
Cc      |Numero delle citazioni                                     |Int32      |none  
ECC     |Numero stimato delle citazioni                           |Int32      |none
AA.AuN  |Nome autore                                        |String     |Uguale a<br/>StartsWith
AA.AuId |ID autore                                          |Int64      |Equals
AA.AfN  |Nome di affiliazione autore                            |String     |Uguale a<br/>StartsWith
AA.AfId |ID di affiliazione autore                              |Int64      |Equals
AA.S    |Ordine autore per il documento                         |Int32      |Equals
F.FN    |Nome del campo di studi                                |String     |Uguale a<br/>StartsWith
F.FId   |ID del campo di studi                                  |Int64      |Equals
J.JN    |Nome del giornale di registrazione                                       |String     |Uguale a<br/>StartsWith
J.JId   |ID giornale di registrazione                                         |Int64      |Equals
C.CN    |Nome della serie di conferenze                             |String     |Uguale a<br/>StartsWith
C.CId   |ID della serie di conferenze                               |Int64      |Equals
RId     |ID dei documenti referenziati                              |Int64[]    |Equals
W       |Parole dal titolo del documento e Astratto                |String[]   |Equals
E       |Metadati estesi (vedere tabella riportata di seguito)                |String     |none  
        


## <a name="extended-metadata-attributes"></a>Attributi dei metadati estesi ##

Name    | Descrizione               
--------|---------------------------    
Nome di dominio      | Nome visualizzato del documento 
D       | Fonti - elenco di fonti web del documento, ordinate per pertinenza statica
S.Ty    | Tipo di fonte (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | URL della fonte
VFN     | Venue Full Name - nome completo del giornale di registrazione o della conferenza
VSN     | Venue Short Name - nome abbreviato del giornale di registrazione o della conferenza
V       | Volume - volume del giornale di registrazione
BV      | Nome del giornale di registrazione
BT      | 
PB      | Abbreviazioni del Journal
I       | Issue - numero del giornale di registrazione
FP      | FirstPage - prima pagina del documento
LP      | LastPage - ultima pagina del documento
DOI     | Digital Object Identifier - identificatore di oggetto digitale
Cc      | Citation Contexts – elenco degli ID dei documenti referenziati e il relativo contesto nel documento (ad esempio [{123:["le volpi marroni sono famose per il loro salto, come indicato nel documento 123", "cani pigri rappresenta un termine storico improprio, come mostrato nel documento 123”]})
IA      | Inverted Abstract - astratto invertito
IA.IndexLength| Numero di elementi nell'indice (conteggio delle parole dell'astratto)
IA.InvertedIndex| Elenco di parole dell'astratto e la relativa posizione nell'astratto originale (ad esempio, [{"la": [0, 15, 30]}, {"volpe": [1]}, {"marrone":[2]}])
