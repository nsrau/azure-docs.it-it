---
title: Modelli di token blockchain di Azure
description: I modelli di token blockchain di Azure sono modelli standardizzati e riutilizzabili che semplificano la creazione e la distribuzione di token basati su Ledger.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 934474e14d792de4663aeb630c9a28710299f36d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518632"
---
# <a name="azure-blockchain-tokens-templates"></a>Modelli di token blockchain di Azure

[!INCLUDE [Preview note](./includes/preview.md)]

Un modello di token blockchain di Azure è un modello standardizzato e riutilizzabile che semplifica la creazione e la distribuzione di token basati su Ledger. Un modello è costituito da una formula, basata sulla grammatica del [Framework di tassonomia dei token (ttf)](overview.md#token-taxonomy-framework) . La grammatica comprende il tipo di token di base e il set di comportamenti per il token.  

Ad esempio, il modello di token **τϜ {d, m, b, r}** descrive un token di base fungibili che è sub-divisibile, mintable, burnable e ha il supporto dei ruoli.
  
## <a name="base-token-types"></a>Tipi di token di base

Quando si definisce e si crea il token basato su Ledger per un asset specifico, è importante considerare il token di base da usare.

### <a name="fungible"></a>Fungibili

I token fungibili (τF) hanno un valore intercambiabili tra loro purché si trovino nella stessa classe o nella stessa serie. Un token ha lo stesso valore di un altro token o una determinata quantità di token ha lo stesso valore di un'altra quantità uguale. Un dollaro è ad esempio un token fungibili. Se due persone hanno ciascuno una fattura in dollari, possono scambiare tali fatture senza conseguenze. Le fatture del dollaro hanno un valore uguale. 

### <a name="non-fungible"></a>Non fungibili

I token non fungibili (τN) non sono intercambiabili con altri token dello stesso tipo, perché in genere hanno valori diversi. Ad esempio, un titolo di proprietà è un token non fungibili. I titoli delle proprietà in due appartamenti diversi in un complesso Apartment non sono necessariamente di uguale valore, a causa della posizione dell'unità o della superficie su cui si trova l'unità. Il valore percepito dei due token del titolo della proprietà non è uguale.

### <a name="hybrid"></a>Ibrido

I token ibridi sono token che includono componenti di token fungibili e token non fungibili. Un token ibrido è un tipo di token di base che possiede una classe dell'altro tipo di token.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base ibrida non fungibili con segmenti fungibili

Un token di base ibrido non fungibili con segmenti fungibili ha una base non fungibili con segmenti di token fungibili.
Ad esempio, un ticket di concerto è un token ibrido in cui la data e l'ora del concerto sono il token di base non fungibili. I ticket nelle varie sezioni di seduta per il concerto specificato sono i segmenti con token fungibili. I ticket sono scambiabili nelle singole sezioni di seduta, ma non tra le sezioni.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fungibili ibrida con segmenti non fungibili

Una base fungibili ibrida con un token di segmenti non fungibili ha una base fungibili con segmenti di token non fungibili. Ad esempio, una sicurezza con ipoteca supportata è un token ibrido in cui più proprietari sono la base fungibili divisa tra molti proprietari. La sicurezza è intercambiabile. I singoli mutui sono i segmenti non fungibili che rappresentano la sicurezza del mutuo supportata.

## <a name="token-behaviors"></a>Comportamenti dei token

Un comportamento del token definisce le funzionalità o le restrizioni del token. Il comportamento include proprietà di supporto che fanno parte della definizione del token. È possibile applicare comportamenti a tutti i tipi di token o solo a uno. I comportamenti possono essere interni o esterni a seconda degli effetti del comportamento. Un comportamento interno Abilita o limita le proprietà del token stesso. Un comportamento esterno Abilita o limita la chiamata del comportamento da un attore esterno.

Per ulteriori informazioni sui token di Azure blockchain supportati i comportamenti del token del Framework di tassonomia (TTF), vedere [composizione](composability.md)dei token.

## <a name="pre-built-token-templates"></a>Modelli di token predefiniti

I token blockchain di Azure forniscono quattro modelli di token predefiniti che possono essere usati senza modifiche. È possibile chiamare questi modelli predefiniti per la maggior parte dei casi d'uso per iniziare a creare, distribuire e gestire rapidamente i token.

### <a name="commodity-tokens"></a>Token di prodotti

I token di prodotti hanno un valore coerente e sono trasferibili. Ad esempio, un barilotto di petrolio o un'unità di energia.

**τF {~ d, t, m, b, r}** -fungibili, Whole, transferable, mintable, burnable e hanno supporto per i ruoli

Molti scenari blockchain richiedono trasparenza e visibilità nella supply chain o in più organizzazioni. I token dei prodotti sono basati su questi casi d'uso comuni. I token sono intercambiabili e coerenti. Il modello di token di prodotti è flessibile e personalizzabile con i metadati.

### <a name="qualified-tokens"></a>Token qualificati

I token qualificati rappresentano elementi ottenuti e sono in genere associati a un'entità e non possono essere trasferiti. Ad esempio, un diploma o una violazione del parcheggio.

**τN {s, ~ t}** -non fungibili, singleton e non trasferibile

Per diversi scenari di controllo e di attestazione è necessario che la proprietà del token non possa essere modificata. È disponibile un set di casi d'uso, che hanno la necessità di fornire un token completo se l'associazione è valida o non valida.

### <a name="asset-tokens"></a>Token di asset

I token asset hanno un valore univoco dipendente dall'elemento e non sono commoditized. Ad esempio, un elemento del Museo o un titolo della proprietà.

**τN {s, t}** -non fungibili, singleton e trasferibile

I token di asset possono essere confusi con i token di prodotti. La differenza principale tra i due token è che i token di asset sono intrinsecamente univoci e il valore è indipendente dal tipo di token. Ad esempio, un pezzo d'arte come un disegno a olio di un artista definito è un token di asset. Tuttavia, una stampa artistica della Gioconda Lisa viene considerata un token di prodotti. Analogamente, un titolo di proprietà è un token di asset poiché il valore esiste nelle qualità soggettive della proprietà.

### <a name="ticket-tokens"></a>Token ticket

I token ticket hanno un valore coerente ma in genere scadono. Ad esempio un ticket del piano.

**τN {m, b, r}** -non fungibili, mintable, burnable e dotati di supporto per i ruoli.

I token ticket hanno in genere una data di scadenza che li rende diversi da un normale token di base. Ad esempio, un ticket aereo, un ticket di concerto o un ticket sportivo tutti hanno opzioni di posti di seduta assegnati con date di utilizzo specifiche. Non è possibile interscambiare facilmente i ticket tra le date o le aree di seduta.

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria una maggiore flessibilità per lo scenario, vedere la pagina relativa alla creazione di modelli di token personalizzati mediante la [composizione dei token](composability.md).
