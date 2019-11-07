---
title: Composizione del token blockchain di Azure
description: La componibilità dei token blockchain di Azure offre la flessibilità necessaria per creare token per scenari avanzati.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: a82d7ba606eac5dcafc26b1a8527810a5a21840d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577123"
---
# <a name="azure-blockchain-tokens-composability"></a>Composizione del token blockchain di Azure

[!INCLUDE [Preview note](./includes/preview.md)]

La composizione dei token offre la flessibilità necessaria per creare i token per scenari avanzati. È possibile che si disponga di uno scenario complesso che non può essere implementato usando i [quattro modelli di token predefiniti](templates.md#base-token-types). La composizione dei token consente di progettare modelli di token personalizzati mediante l'aggiunta o la rimozione di comportamenti definiti per la creazione di un modello di token personalizzato. Quando si crea un nuovo modello di token, i token blockchain di Azure verificano tutte le regole di grammatica del token. I modelli composti vengono salvati nel servizio token blockchain di Azure per l'emissione in reti blockchain connesse.

È possibile usare i [comportamenti dei token](templates.md#token-behaviors) nelle sezioni seguenti per progettare il modello di token.

## <a name="burnable-b"></a>Masterizzabile (b)

Possibilità di rimuovere i token dall'offerta.

Ad esempio, quando si riscattano i punti della carta di credito online per una carta regalo, i punti della carta di credito vengono bruciati.

## <a name="delegable-g"></a>Delegabili (g)

Possibilità di delegare le azioni intraprese sul token di cui si è proprietari.

Il delegato può eseguire azioni come proprietario del token. Ad esempio, è possibile usare un token delegabili per implementare un voto. Un token delegabili consente al proprietario del token di voto di votare un altro utente per loro conto.

## <a name="logable-l"></a>Logable (l)

Possibilità di registrare.

Ad esempio, è possibile emettere un token logable per una distribuzione di film in ogni teatro che mostra un film specifico. Affinché il film venga riprodotto, la visualizzazione deve registrare una transazione per ogni visualizzazione, perché i pagamenti di royalty sono visualizzati durante l'esecuzione del rilascio del film. La build Actors può usare i token dei film per convalidare i pagamenti per ogni film che mostra per ogni teatro nella distribuzione.

## <a name="mint-able-m"></a>Mint-able (m)

Possibilità di coniare token aggiuntivi per la classe token. Il ruolo Minter include il comportamento mintable.

Ad esempio, un'azienda di vendita al dettaglio, che desidera implementare un programma di fedeltà, può utilizzare token mintable per il programma fedeltà. Possono coniare punti di fedeltà aggiuntivi per i clienti man mano che aumentano la propria clientela.  

## <a name="non-subdividable-or-whole-d"></a>Non subdividable o whole (~ d)

Restrizione per impedire che un token venga diviso in parti più piccole.

Ad esempio, un singolo disegno artistico non può essere suddiviso in più parti più piccole. 

## <a name="non-transferable-t"></a>Non trasferibile (~ t)

Restrizione per impedire una modifica della proprietà dal proprietario del token iniziale.

Ad esempio, un diploma universitario è un token non trasferibile. Quando un diploma viene assegnato a un laureato, non può essere trasferito dal laureato a un altro utente.

## <a name="roles-r"></a>Ruoli (r)

Possibilità di definire i ruoli all'interno della classe del modello di token per comportamenti specifici.

È possibile specificare un elenco di nomi di ruolo supportati da un token al momento della creazione del token. Quando si specificano i ruoli, l'utente può assegnare ruoli a questi comportamenti. Attualmente, è supportato solo il ruolo Minter.

## <a name="singleton-s"></a>Singleton/i

Restrizione per consentire un rifornimento di un token.

Un elemento del Museo, ad esempio, è un token singleton. Gli elementi del Museo sono univoci. Un token che rappresenta un artefatto dispone solo di un singolo elemento nell'oggetto fornito.

## <a name="subdividable-d"></a>Subdividable (d)

Possibilità di dividere un token in parti più piccole.

Un dollaro, ad esempio, può essere suddiviso in centesimi.

## <a name="transferable-t"></a>Trasferibile (t)

Possibilità di trasferire la proprietà del token.

Ad esempio, un titolo di proprietà è un token trasferibile, che può essere trasferito da una persona a un'altra quando la proprietà viene venduta.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [gestione degli account per i token blockchain di Azure](account-management.md).
