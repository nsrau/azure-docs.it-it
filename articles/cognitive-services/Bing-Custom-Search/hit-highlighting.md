---
title: 'Ricerca personalizzata Bing: Usare indicatori di effetto per evidenziare il testo | Microsoft Docs'
description: Illustra come abilitare gli effetti testo nelle risposte della ricerca.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: d2d0070865aa29257ac827bbb4fc313d87ea7282
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373036"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Uso degli indicatori di effetto per evidenziare il testo

Bing supporta l'evidenziazione dei risultati, con cui i termini di query o altri termini ritenuti rilevanti da Bing vengono contrassegnati nelle stringhe visualizzate di alcune risposte. I termini di query possono ad esempio essere contrassegnati con i campi `name`, `displayUrl` e `snippet` di una pagina Web.

Per impostazione predefinita, Bing non include i marcatori di evidenziazione nelle stringhe visualizzate. Per includerli, includere il parametro di query `textDecorations` nella richiesta e impostarlo su **true**. Bing contrassegna i termini di query usando i caratteri Unicode E000 e E001 Unicode per indicare l'inizio e la fine del termine. Se il termine di query è Sailing Dinghy e uno dei termini è presente nel campo, ad esempio, il termine è racchiuso tra i caratteri di evidenziazione dei risultati come illustrato di seguito:  
  
![Evidenziazione dei risultati](./media/bing-hit-highlighting.PNG) 

Prima di visualizzare la stringa nell'interfaccia utente, i caratteri Unicode verranno sostituiti con caratteri appropriati per il formato di visualizzazione. Se si visualizza il testo in formato HTML, ad esempio, è possibile evidenziare il termine di query sostituendo E000 con <b\> e E001 con </b\>. Se non si vuole applicare formattazione, rimuovere i marcatori dalla stringa. 

Come marcatori, Bing offre la possibilità di usare caratteri Unicode o tag HTML. Per specificare i marcatori da usare, includere il parametro di query `textFormat`. Per contrassegnare il contenuto con caratteri Unicode, impostare `textFormat` su Raw (valore predefinito). Per contrassegnare il contenuto con tag HTML, impostare `textFormat` su HTML. 
  
Se il parametro `textDecorations` è impostato su **true**, nelle stringhe visualizzate delle risposte possono essere inclusi da Bing i marcatori riportati di seguito. Se non esiste un equivalente HTML, la cella della colonna HTML della tabella è vuota.

|Unicode|HTML|DESCRIZIONE
|-|-|-
|U+E000|\<b>|Contrassegna l'inizio del termine di query (evidenziazione dei risultati)
|U+E001|\</b>|Contrassegna la fine del termine di query
|U+E002|\<i&gt;|Contrassegna l'inizio di contenuto in corsivo 
|U+E003|\</i&gt;|Contrassegna la fine di contenuto in corsivo
|U+E004|\<br/>|Contrassegna un'interruzione di riga
|U+E005||Contrassegna l'inizio di un numero di telefono
|U+E006||Contrassegna la fine di un numero di telefono
|U+E007||Contrassegna l'inizio di un indirizzo
|U+E008||Contrassegna la fine di un indirizzo
|U+E009|\&nbsp;|Contrassegna uno spazio unificatore
|U+E00C|\<strong&gt;|Contrassegna l'inizio di contenuto in grassetto
|U+E00D|\</strong>|Contrassegna la fine di contenuto in grassetto
|U+E00E||Contrassegna l'inizio di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E00F||Contrassegna la fine di contenuto con sfondo più chiaro rispetto allo sfondo circostante
|U+E010||Contrassegna l'inizio di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E011||Contrassegna la fine di contenuto con sfondo più scuro rispetto allo sfondo circostante
|U+E012|\<del>|Contrassegna l'inizio di contenuto barrato
|U+E013|\</del&gt;|Contrassegna la fine di contenuto barrato
|U+E016|\<sub>|Contrassegna l'inizio di contenuto formattato come pedice
|U+E017|\</sub&gt;|Contrassegna la fine di contenuto formattato come pedice
|U+E018|\<sup&gt;|Contrassegna l'inizio di contenuto formattato come apice
|U+E019|\</sup>|Contrassegna la fine di contenuto formattato come apice

L'esempio seguente illustra una risposta `Computation` contenente i marcatori di pedice per il termine di query log(2). Il campo `expression` contiene i marcatori solo se textDecoration è **true**.

![Marcatori in computation](./media/bing-markers-computation.PNG) 

In assenza di effetti nella richiesta, l'espressione sarebbe log10(2). 
  
