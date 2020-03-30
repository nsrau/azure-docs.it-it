---
title: Modelli di token Blockchain di AzureAzure Blockchain Tokens templates
description: I modelli di token Blockchain di Azure sono modelli standardizzati e riutilizzabili che semplificano la creazione e la distribuzione di token basati su contabilità generale.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252207"
---
# <a name="azure-blockchain-tokens-templates"></a>Modelli di token Blockchain di AzureAzure Blockchain Tokens templates

[!INCLUDE [Preview note](./includes/preview.md)]

Un modello di token Blockchain di Azure è un modello standardizzato e riutilizzabile che semplifica la creazione e la distribuzione di token basati su contabilità generale. Un modello è costituito da una formula basata sulla grammatica [TTF (Token Taxonomy Framework).](overview.md#token-taxonomy-framework) La grammatica include il tipo di token di base e il set di comportamenti per il token.  

Ad esempio, il modello di token di token **"d,m,b,r"** descrive un token di base fungibile che è sottodividibile, minable, bruciabile e ha il supporto del ruolo.
  
## <a name="base-token-types"></a>Tipi di token di base

Quando si definisce e si crea il token basato sulla contabilità generale per il cespite specifico, è importante considerare il token di base da utilizzare.

### <a name="fungible"></a>Fungibile

I token dei funghi (F) hanno un valore intercambiabile l'uno con l'altro, purché si trovino nella stessa classe o serie. Un token ha lo stesso valore di un altro token o una determinata quantità di token ha lo stesso valore di un'altra quantità uguale. Ad esempio, un dollaro è un gettone fungibile. Se due persone sono ciascuna in possesso di una banconota da un dollaro, possono scambiare queste banconote in dollari senza conseguenze. Le banconote in dollari hanno lo stesso valore. 

### <a name="non-fungible"></a>Non-Fungible

I token non fungibili (zN) non sono intercambiabili con altri token dello stesso tipo in quanto in genere hanno valori diversi. Ad esempio, il titolo di una proprietà è un token non fungibile. I titoli degli immobili di due appartamenti diversi in un complesso di appartamenti non sono necessariamente di uguale valore, a causa della posizione dell'unità o del piano su cui si trova l'unità. Il valore percepito dei due token del titolo della proprietà non è uguale.

### <a name="hybrid"></a>Ibrido

I token ibridi sono token che hanno componenti di token sia fungibili che token non fungibili. Un token ibrido è un tipo di token di base proprietario di una classe dell'altro tipo di token.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base ibrida non fungibile con segmenti fungibili

Una base ibrida non fungibile con token di segmenti fungibili ha una base non fungibile con segmenti di token fungibili.
Ad esempio, un ticket di concerto è un token ibrido in cui la data e l'ora del concerto sono il token di base non fungibile. I biglietti in varie sezioni di posti a sedere per il dato concerto sono i segmenti con gettoni fungibili. I biglietti sono scambiabili nelle loro singole sezioni di posti a sedere, ma non attraverso le sezioni.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fungibile ibrida con segmenti non fungibili

Una base fungibile ibrida con un token di segmenti non fungibili ha una base fungibile con segmenti token non fungibili. Ad esempio, una sicurezza garantita da ipoteca è un token ibrido in cui più proprietari sono la base fungibile che viene suddivisa tra molti proprietari. La sicurezza è intercambiabile. Le singole ipoteche sono i segmenti non fungibili che rappresentano il titolo di sicurezza specifico sostenuto dall'ipoteca.

## <a name="token-behaviors"></a>Comportamenti dei token

Un comportamento del token definisce le funzionalità o le restrizioni del token. Il comportamento include le proprietà di supporto che fanno parte della definizione del token. I comportamenti possono essere applicati a tutti i tipi di token o a uno solo. I comportamenti possono essere interni o esterni a seconda degli effetti del comportamento. Un comportamento interno abilita o limita le proprietà sul token stesso. Un comportamento esterno consente o limita la chiamata del comportamento da un attore esterno.

Per altre informazioni sui comportamenti dei token TTF (Token Taxonomy Framework) supportati dai token di Azure, vedere [Composizione di token.](composability.md)

## <a name="pre-built-token-templates"></a>Modelli di token predefiniti

I token Blockchain di Azure offrono quattro modelli di token predefiniti che possono essere usati senza modifiche. È possibile chiamare questi modelli predefiniti per la maggior parte dei casi d'uso per iniziare a creare, distribuire e gestire rapidamente i token.

### <a name="commodity-tokens"></a>Token di materie prime

I token delle materie prime hanno un valore costante e sono trasferibili. Ad esempio, un barile di petrolio o un'unità di energia.

**-** fungible, intero, trasferibile, minable, bruciabile e con supporto ruolo

Molti scenari blockchain richiedono trasparenza e visibilità in tutta la catena di fornitura o in più organizzazioni. I token delle materie prime si basano su questi casi d'uso comuni. I token sono intercambiabili e coerenti. Il modello di token commodity è flessibile e personalizzabile con i metadati.

### <a name="qualified-tokens"></a>Token qualificati

I token qualificati rappresentano un elemento guadagnato e di solito sono associati a un'entità e non possono essere trasferiti. Ad esempio, un diploma o una violazione del parcheggio.

**-** non-fungible, singleton e non trasferibile

Vari scenari di controllo e attestazione richiedono che la proprietà del token non possa essere modificata. Esiste un set di casi d'uso, che hanno la necessità di fornire un token qualificato, indipendentemente dal fatto che l'associazione sia buona o meno.

### <a name="asset-tokens"></a>Token di asset

I token degli asset hanno un valore univoco dipendente dall'elemento e non vengono mercificati. Ad esempio, un elemento del museo o un titolo di proprietà.

**-** non fungibile, singleton e trasferibile

I token di asset possono essere confusi con i token delle materie prime. La differenza principale tra i due token è che i token di asset sono intrinsecamente univoci e il valore è indipendente dal tipo di token che è. Ad esempio, un'opera d'arte come un dipinto ad olio di un artista affermato è un token di asset. Tuttavia, una stampa d'arte della Gioconda è considerata un token di merce. Analogamente, il titolo di una proprietà è un token di asset poiché il valore esiste nelle qualità soggettive della proprietà.

### <a name="ticket-tokens"></a>Token ticket

I token ticket hanno un valore costante, ma in genere scadono. Ad esempio, un biglietto aereo.

**-** non fungibile, mintable, bruciabile e avere il supporto del ruolo.

I token di ticket hanno in genere una data di scadenza che li rende diversi da un normale token di merce. Ad esempio, un biglietto aereo, un biglietto per concerti o un biglietto sportivo hanno tutte opzioni di posti a sedere assegnati con date di utilizzo specifiche. Non è possibile scambiare facilmente i biglietti tra date o posti a sedere.

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria maggiore flessibilità per lo scenario, vedere la creazione di modelli di token personalizzati utilizzando [la composizione di token](composability.md).
