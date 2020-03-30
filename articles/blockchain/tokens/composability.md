---
title: Componibilità dei token Blockchain di AzureAzure Blockchain Tokens composability
description: La composizione dei token Blockchain di Azure offre flessibilità per creare token per scenari avanzati.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325110"
---
# <a name="azure-blockchain-tokens-composability"></a>Componibilità dei token Blockchain di AzureAzure Blockchain Tokens composability

[!INCLUDE [Preview note](./includes/preview.md)]

La composizione dei token offre la flessibilità necessaria per creare token per scenari avanzati. È possibile che si disponga di uno scenario complesso che non può essere implementato utilizzando i quattro modelli di [token predefiniti.](templates.md#base-token-types) La composizione di token consente di progettare modelli di token personalizzati aggiungendo o rimuovendo comportamenti definiti per creare un modello di token personalizzato. Quando si crea un nuovo modello di token, I token Blockchain di Azure verificano tutte le regole grammaticali dei token. I modelli composti vengono salvati nel servizio Token Blockchain di Azure per l'emissione su reti blockchain connesse.

È possibile usare i [comportamenti dei token](templates.md#token-behaviors) nelle sezioni seguenti per progettare il modello di token.

## <a name="burnable-b"></a>Bruciabile (b)

Possibilità di rimuovere i token dalla fornitura.

Ad esempio, quando riscatti i punti della carta di credito online per una carta regalo, i punti della carta di credito vengono bruciati.

## <a name="delegable-g"></a>Delegabile (g)

Possibilità di delegare le azioni eseguite sul token di cui si è proprietari.

Il delegato può eseguire azioni come proprietario del token. Ad esempio, è possibile utilizzare un token delegabile per implementare un voto. Un token delegabile consente al proprietario del token di voto di far votare qualcun altro per suo conto.

## <a name="logable-l"></a>Logabile (l)

Possibilità di log.

Ad esempio, è possibile emettere un token registrabile per la distribuzione di un film a ogni teatro che mostra un film specifico. Affinché il filmato venga riprodotto, la presentazione deve registrare una transazione per ogni presentazione perché i pagamenti delle royalty vengono visualizzati durante l'esecuzione del film. La build degli attori può usare i token del film per convalidare i pagamenti per film che mostrano per teatro nella distribuzione.

## <a name="mint-able-m"></a>Mente capace (m)

Possibilità di coniare token aggiuntivi per la classe token. Il ruolo minter include il comportamento modificabile.

Ad esempio, una società di vendita al dettaglio che desidera implementare un programma fedeltà può utilizzare token mintable per il programma fedeltà. Possono coniare punti fedeltà aggiuntivi per i loro clienti man mano che la loro base di clienti cresce.  

## <a name="non-subdividable-or-whole-d"></a>Non sottodividibile o intero (d)

Restrizione per impedire che un token venga diviso in parti più piccole.

Ad esempio, un singolo dipinto artistico non può essere suddiviso in più parti più piccole. 

## <a name="non-transferable-t"></a>Non trasferibile (zt)

Restrizione per impedire una modifica della proprietà da parte del proprietario del token iniziale.

Ad esempio, un diploma universitario è un token non trasferibile. Una volta che un diploma è dato a un laureato, non può essere trasferito dal laureato ad un'altra persona.

## <a name="roles-r"></a>Ruoli (r)

Possibilità di definire ruoli all'interno della classe modello di token per comportamenti specifici.

È possibile fornire un elenco di nomi di ruolo supportati da un token al momento della creazione del token. Quando vengono specificati i ruoli, l'utente può assegnare ruoli a questi comportamenti. Attualmente è supportato solo il ruolo minter.

## <a name="singleton-s"></a>Singleton (s)

Restrizione per consentire la fornitura di un token.

Ad esempio, un elemento del museo è un token singleton. I manufatti del museo sono unici. Un token che rappresenta un elemento ha un solo elemento nella fornitura.

## <a name="subdividable-d"></a>Subdividibile (d)

Possibilità di dividere un token in parti più piccole.

Ad esempio, un dollaro può essere suddiviso in centesimi.

## <a name="transferable-t"></a>Trasferibile (t)

Possibilità di trasferire la proprietà del token.

Ad esempio, il titolo di una proprietà è un token trasferibile, che può essere trasferito da una persona a un'altra quando la proprietà viene venduta.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [gestione dell'account di Azure Blockchain Tokens](account-management.md).
