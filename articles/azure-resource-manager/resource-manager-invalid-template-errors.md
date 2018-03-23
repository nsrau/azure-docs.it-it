---
title: Errori di Azure dovuti a modelli non validi | Microsoft Docs
description: Descrive come risolvere gli errori dovuti a modelli non validi.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 9626b3caaa7188a4e9a37f83d1fbf091951714f4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
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

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Soluzione 1: errore di sintassi

Se viene visualizzato un messaggio di errore che indica che la convalida del modello non è riuscita, potrebbe trattarsi di un problema di sintassi nel modello.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Non è insolito commettere questo errore, perché le espressioni del modello possono essere complesse. Ad esempio, l'assegnazione del nome seguente per un account di archiviazione contiene un set di parentesi quadre, tre funzioni, tre set di parentesi, un set di virgolette singole e una proprietà:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se non si specifica la sintassi corrispondente, il modello genera un valore diverso dal previsto.

Quando viene visualizzato questo tipo di errore, esaminare attentamente la sintassi dell'espressione. È consigliabile usare un editor JSON come [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) che può segnalare gli errori di sintassi.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Soluzione 2: lunghezza non valida di uno o più segmenti

Un altro errore di modello non valido si verifica quando il formato del nome della risorsa non è corretto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Il nome di una risorsa a livello di radice deve contenere un segmento in meno rispetto al tipo di risorsa. Ogni segmento si differenzia mediante una barra. Nell'esempio seguente il tipo ha due segmenti e il nome un segmento, quindi è un **nome valido**.

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

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Soluzione 3: parametro non valido

Se si specifica un valore di parametro che non corrisponde a uno dei valori consentiti, verrà visualizzato un messaggio simile all'errore seguente:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Ricontrollare i valori consentiti nel modello e specificarne uno durante la distribuzione. Per altre informazioni sui valori di parametro consentiti, vedere [Sezione parameters dei modelli di Azure Resource Manager](resource-manager-templates-parameters.md).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Soluzione 4: troppi gruppi di risorse di destinazione

Se si specificano più di cinque gruppi di risorse di destinazione in un'unica distribuzione, viene visualizzato questo errore. Prendere in considerazione il consolidamento del numero di gruppi di risorse nella distribuzione o la distribuzione di alcuni dei modelli come distribuzioni separate. Per altre informazioni, vedere [Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Soluzione 5: rilevamento di una dipendenza circolare

Questo errore viene visualizzato quando le dipendenze tra le risorse impediscono l'avvio della distribuzione. A causa di una combinazione di interdipendenze, due o più risorse attendono altre risorse che sono a propria volta in attesa. Ad esempio, la risorsa 1 dipende dalla risorsa 3, la risorsa 2 dipende dalla risorsa 1 e la risorsa 3 dipende dalla risorsa 2. È in genere possibile risolvere questo problema rimuovendo le dipendenze non necessarie.

Per risolvere una dipendenza circolare:

1. Nel modello trovare la risorsa identificata nella dipendenza circolare. 
2. Esaminare la proprietà **dependsOn** e le occorrenze della funzione **reference** per tale risorsa per verificare le risorse da cui dipende. 
3. Esaminare tali risorse per verificare da quali risorse dipendono. Seguire le dipendenze finché non si rileva una risorsa che dipende dalla risorsa originale.
5. Per le risorse coinvolte nella dipendenza circolare, esaminare attentamente tutte le occorrenze della proprietà **dependsOn** per identificare eventuali dipendenze non necessarie. Rimuovere tali dipendenze. Se non si è certi che una dipendenza sia necessaria, provare a rimuoverla. 
6. Ridistribuire il modello.

La rimozione di valori dalla proprietà **dependsOn** può causare errori durante la distribuzione del modello. Se si riceve un errore, riaggiungere la dipendenza nel modello. 

Se con questo approccio non si risolve la dipendenza circolare, provare a spostare parte della logica di distribuzione in risorse figlio, ad esempio estensioni o impostazioni di configurazione. Configurare tali risorse figlio in modo che vengano distribuite dopo le risorse coinvolte nella dipendenza circolare. Si supponga, ad esempio, di distribuire due macchine virtuali e che sia necessario impostare in ognuna proprietà che fanno riferimento all'altra. È possibile eseguire la distribuzione nell'ordine seguente:

1. VM 1
2. VM 2
3. L'estensione in VM 1 dipende da VM 1 e VM 2. L'estensione imposta in VM 1 valori ottenuti da VM 2.
4. L'estensione in VM 2 dipende da VM 1 e VM 2. L'estensione imposta in VM 2 valori ottenuti da VM 1.

Lo stesso approccio è applicabile alle app del servizio app. Provare a spostare i valori di configurazione in una risorsa figlio della risorsa app. È possibile distribuire due app Web nell'ordine seguente:

1. App Web 1
2. App Web 2
3. La configurazione per App Web 1 dipende da App Web 1 e App Web 2. Contiene impostazioni dell'app con valori di App Web 2.
4. La configurazione per App Web 2 dipende da App Web 1 e App Web 2. Contiene impostazioni dell'app con valori di App Web 1.
