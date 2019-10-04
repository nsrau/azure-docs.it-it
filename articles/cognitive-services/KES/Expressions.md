---
title: Espressioni di query strutturate - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare le espressioni di query strutturate nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815108"
---
# <a name="structured-query-expression"></a>Espressioni di query strutturate

Un'espressione di query strutturata specifica un set di operazioni da valutare rispetto all'indice dei dati.  È costituita da espressioni di query di attributo e funzioni di alto livello.  Usare il metodo [*evaluate*](evaluateMethod.md) per calcolare gli oggetti corrispondenti all'espressione.  L'esempio seguente è tratto dal dominio delle pubblicazioni accademiche che restituisce le pubblicazioni dell'autore Jaime Teevan dall'anno 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Le espressioni di query strutturate possono essere ottenute da richieste [*interpret*](interpretMethod.md), in cui l'output semantico di ogni interpretazione è un'espressione di query strutturata che restituisce gli oggetti indice corrispondenti alla query in linguaggio naturale di input.  In alternativa, è possibile crearle manualmente usando la sintassi descritta in questa sezione.

## <a name="attribute-query-expression"></a>Espressione di query per attributo

Un'espressione di query per attributo identifica un set di oggetti in base alla corrispondenza rispetto a un attributo specifico.  Sono supportate operazioni per l'individuazione delle corrispondenze diverse a seconda del tipo di attributo e dell'operazione indicizzata specificata nello [schema](SchemaFormat.md):

| Type | Operazione | Esempi |
|------|-------------|------------|
| String | equals | Title = 'latent semantic analysis' (valore canonico + sinonimi) |
| String | equals | Author.Name=='susan t dumais' (solo valore canonico)|
| String | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'... (qualsiasi valore decimale a partire da "20") |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=2010,2012 *(include solo il valore limite sinistro: 2010, 2011)* <br/> Year=2000,2012 *(include entrambi i valori limite: 2010, 2011, 2012)* |
| Date | equals | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Ad esempio, l'espressione "Title='latent s'..." corrisponde a tutte le pubblicazioni accademiche con titolo che inizia con la stringa "latent s".  Per valutare questa espressione, l'attributo Title deve specificare l'operazione "starts_with" nello schema usato per compilare l'indice.

Per gli attributi con sinonimi associati, un'espressione di query può specificare gli oggetti il cui valore canonico corrisponde a una stringa specificata usando l'operatore "==" oppure gli oggetti per cui uno qualsiasi dei relativi valori canonico/sinonimo corrisponde usando l'operatore "=".  Per entrambi deve essere specificato l'operatore "equals" nella definizione dell'attributo.


## <a name="functions"></a>Funzioni

È disponibile un set predefinito di funzioni che consente la costruzione di espressioni di query più elaborate da query per attributo di base.

### <a name="and-function"></a>Funzione And

`And(expr1, expr2)`

Restituisce l'intersezione di due espressioni di query di input.

L'esempio seguente restituisce le pubblicazioni accademiche pubblicate nell'anno 2000 sul recupero delle informazioni:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Funzione Or

`Or(expr1, expr2)`

Restituisce l'unione delle due espressioni di query di input.

L'esempio seguente restituisce le pubblicazioni accademiche pubblicate nell'anno 2000 sul recupero delle informazioni o sulla modellazione degli utenti:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Funzione Composite

`Composite(expr)`

Restituisce un'espressione che incapsula un'espressione interna composta da query sugli attributi secondari di un attributo composito comune.  Per l'incapsulamento è necessario che l'attributo composito di qualsiasi oggetto dati corrispondenti abbia almeno un valore che soddisfa singolarmente l'espressione interna.  Si noti che un'espressione di query su attributi secondari di un attributo composito deve essere incapsulata usando la funzione Composite() prima di poter essere combinata con altre espressioni di query.

Ad esempio, l'espressione seguente restituisce le pubblicazioni accademiche di "harry shum" mentre lavorava a "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

L'espressione seguente restituisce invece le pubblicazioni accademiche per cui uno degli autori è "harry shum" e una delle affiliazioni è "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
