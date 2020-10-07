---
title: Grammatica delle regole di attestazione di Attestazione di Azure
description: Informazioni dettagliate sulle attestazioni di criteri e le regole di attestazione di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285419"
---
# <a name="claim-and-claim-rules"></a>Attestazioni e regole di attestazione

Per comprendere la grammatica delle regole di attestazione, è necessario prima comprendere le attestazioni dei criteri di attestazione.

## <a name="claim"></a>Attestazione

Un'attestazione è un set di proprietà raggruppate per fornire informazioni pertinenti. Per Attestazione di Azure, un'attestazione contiene le proprietà seguenti:

- **type**: valore stringa che rappresenta il tipo di attestazione.
- **value**: valore booleano, intero o stringa che rappresenta il valore dell'attestazione.
- **valueType**: il tipo di dati delle informazioni archiviate nella proprietà value. I tipi supportati sono stringa, intero e booleano. Se non è definito, il valore predefinito sarà stringa.
- **issuer**: informazioni riguardanti l'autorità emittente dell'attestazione. La proprietà issuer può essere di uno dei tipi seguenti:
  - **AttestationService**: Attestazione di Azure rende disponibili alcune attestazioni all'autore del criterio, che può usarle per creare il criterio di attestazione appropriato.
  - **AttestationPolicy**: il criterio stesso (definito dall'amministratore) può aggiungere attestazioni all'evidenza in ingresso durante l'elaborazione. La proprietà issuer in questo caso è impostata su "AttestationPolicy".
  - **CustomClaim**: l'attestatore (client) può anche aggiungere altre attestazioni all'evidenza di attestazione. La proprietà issuer in questo caso è impostata su "CustomClaim".

Se non è definito, il valore predefinito sarà "CustomClaim".

## <a name="claim-rule"></a>Regola di attestazione

Il set di attestazioni in ingresso viene usato dal motore di criteri per calcolare il risultato dell'attestazione. Una regola di attestazione è un set di condizioni usate per convalidare le attestazioni in ingresso e intraprendere l'azione definita.

```
Conditions list => Action (Claim)
```

La valutazione di un'attestazione da parte di Attestazione di Azure implica i passaggi seguenti:

- Se l'elenco di condizioni non è presente, viene eseguita l'azione con l'attestazione specificata 
- In caso contrario, vengono valutate le condizioni dell'elenco.
- Se l'elenco di condizioni restituisce false, l'operazione viene interrotta. In caso contrario, procede.

Le condizioni di una regola di attestazione vengono usate per determinare se l'azione deve essere eseguita. L'elenco di condizioni è una sequenza di condizioni separate dall'operatore "&&".

L'elenco di condizioni è strutturato come segue:

```
Condition && Condition && ...
```

La condizione è strutturata come segue:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

L'elenco di condizioni è costituito da singole condizioni basate su varie proprietà di un'attestazione. Una condizione può avere un identificatore facoltativo, che può essere usato per fare riferimento alle attestazioni che la soddisfano. Questo riferimento può essere usato nelle altre condizioni o nell'azione della stessa regola.

Ad esempio

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Di seguito sono riportati gli operatori che è possibile usare per verificare le condizioni:

| Tipo di valore | Operazioni supportate |
|--|--|
| Integer | == (uguale a), \!= (diverso da), <= (minore o uguale a), < (minore di), >= (maggiore o uguale a), > (maggiore di) |
| string | == (uguale a), \!= (diverso da) |
| Boolean | == (uguale a), \!= (diverso da) |

Valutazione dell'elenco di condizioni:

- La presenza dell'operatore "&&" implica che un elenco di condizioni restituisce true solo se tutte le condizioni dell'elenco restituiscono true.
- Una condizione rappresenta criteri di filtro applicati al set di attestazioni. La condizione stessa restituisce true se è presente almeno un'attestazione che la soddisfa.
- Un'attestazione soddisfa il criterio di filtro rappresentato dalla condizione se ognuna delle relative proprietà soddisfa le corrispondenti condizioni delle proprietà delle attestazioni presenti nella condizione.  

Le azioni consentite in un criterio sono descritte di seguito.

| Verbo dell'azione | Descrizione | Sezioni dei criteri a cui si applicano |
|--|--|--|
| permit() | Il set di attestazioni in ingresso può essere usato per calcolare **issuancerules**. Non accetta attestazioni come parametro | **authorizationrules** |
| deny() | Il set di attestazioni in ingresso non deve essere usato per calcolare **issuancerules** Non accetta attestazioni come parametro | **authorizationrules** |
| add(claim) | Aggiunge l'attestazione al set di attestazioni in ingresso. Qualsiasi attestazione aggiunta al set di attestazioni in ingresso sarà disponibile per le regole di attestazione successive. |**authorizationrules**, **issuancerules** |
| issue(claim) | Aggiunge l'attestazione al set di attestazioni in ingresso e in uscita | **issuancerules** |
| issueproperty(claim) | Aggiunge l'attestazione al set di attestazioni in ingresso e di proprietà | **issuancerules**

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)

