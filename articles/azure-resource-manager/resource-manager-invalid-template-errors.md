---
title: Errori di Azure dovuti a modelli non validi | Microsoft Docs
description: Descrive come risolvere gli errori dovuti a modelli non validi.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 87bc6e4def624785c5052a9a25f579b022c940ec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="resolve-errors-for-invalid-template"></a>Risolvere errori dovuti a modelli non validi

Questo articolo descrive come risolvere gli errori dovuti a modelli non validi.

## <a name="symptom"></a>Sintomo

Quando si distribuisce un modello, viene visualizzato l'errore seguente:

```
Code=InvalidTemplate
Message=<varies>
```

Il messaggio di errore dipende dal tipo di errore.

## <a name="cause"></a>Causa

Questo errore può essere causato da diversi tipi di errori. In genere si riferisce a errori di sintassi o strutturali nel modello.

## <a name="solution"></a>Soluzione

### <a name="solution-1---syntax-error"></a>Soluzione 1: errore di sintassi

Se viene visualizzato un messaggio di errore che indica che la convalida del modello non è riuscita, potrebbe trattarsi di un problema di sintassi nel modello.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Non è insolito commettere questo errore, perché le espressioni del modello possono essere complesse. Ad esempio, l'assegnazione di nome seguente per un account di archiviazione contiene un set di parentesi, tre funzioni, tre set di parentesi, un set di virgolette singole e una proprietà:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se non si specifica la sintassi corrispondente, il modello produce un valore diverso dal previsto.

Quando viene visualizzato questo tipo di errore, esaminare attentamente la sintassi dell'espressione. È consigliabile usare un editor JSON come [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) che può segnalare gli errori di sintassi.

### <a name="solution-2---incorrect-segment-lengths"></a>Soluzione 2: lunghezza non valida di uno o più segmenti

Un altro errore di modello non valido si verifica quando il nome della risorsa non è nel formato corretto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Il nome di una risorsa a livello di radice deve contenere un segmento in meno rispetto al tipo di risorsa. Ogni segmento si differenzia mediante una barra. Nell'esempio seguente, il tipo ha due segmenti e il nome un segmento, quindi è un **nome valido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

L'esempio successivo invece **non è un nome valido** , perché ha lo stesso numero di segmenti del tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Per le risorse figlio, il tipo e il nome devono avere lo stesso numero di segmenti. Questo è dovuto al fatto che il nome completo e il tipo dell'elemento figlio includono il nome e il tipo dell'elemento padre. Di conseguenza, il nome completo ha comunque un segmento in meno rispetto al tipo completo.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Ottenere il numero di segmenti corretto può essere difficile con i tipi di Resource Manager applicati ai provider di risorse. Per applicare un blocco di risorsa a un sito Web, ad esempio, è necessario un tipo con quattro segmenti. Il nome è quindi costituito da tre segmenti:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Soluzione 3: parametro non valido

Se il modello specifica i valori consentiti per un parametro e si fornisce un valore diverso da tali valori, verrà visualizzato un messaggio simile all'errore seguente:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Ricontrollare i valori consentiti nel modello e specificarne uno durante la distribuzione.

### <a name="solution-4---too-many-target-resource-groups"></a>Soluzione 4: troppi gruppi di risorse di destinazione

Se si specificano più di cinque gruppi di risorse di destinazione in un'unica distribuzione, viene visualizzato questo errore. Prendere in considerazione il consolidamento del numero di gruppi di risorse nella distribuzione o la distribuzione di alcuni dei modelli come distribuzioni separate. Per altre informazioni, vedere [Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse](resource-manager-cross-resource-group-deployment.md).

### <a name="solution-5---circular-dependency-detected"></a>Soluzione 5: rilevamento di una dipendenza circolare

Questo errore viene visualizzato quando le dipendenze tra le risorse impediscono l'avvio della distribuzione. A causa di una combinazione di interdipendenze, due o più risorse attendono altre risorse che sono a propria volta in attesa. Ad esempio, la risorsa 1 dipende dalla risorsa 3, la risorsa 2 dipende dalla risorsa 1 e la risorsa 3 dipende dalla risorsa 2. È in genere possibile risolvere questo problema rimuovendo le dipendenze non necessarie.
