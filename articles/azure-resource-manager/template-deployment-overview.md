---
title: Modelli di Gestione risorse di Azure
description: Viene descritto come usare i modelli di Azure Resource Manager per la distribuzione delle risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801997"
---
# <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Con il passaggio al cloud, molti team hanno adottato metodi di sviluppo agile. Questi team eseguono un'iterazione rapida. È necessario distribuire ripetutamente le soluzioni nel cloud e verificare che l'infrastruttura sia in uno stato affidabile. Poiché l'infrastruttura è diventata parte del processo iterativo, la divisione tra le operazioni e lo sviluppo è scomparsa. I team devono gestire il codice dell'infrastruttura e dell'applicazione tramite un processo unificato.

Per soddisfare queste esigenze, è possibile automatizzare le distribuzioni e utilizzare la pratica dell'infrastruttura come codice. Nel codice è necessario definire l'infrastruttura da distribuire. Il codice dell'infrastruttura diventa parte del progetto. Proprio come il codice dell'applicazione, il codice dell'infrastruttura viene archiviato in un repository di origine e ne viene versione. Uno dei team può eseguire il codice e distribuire ambienti simili.

Per implementare l'infrastruttura come codice per le soluzioni di Azure, usare Azure Resource Manager modelli. Il modello è un file JavaScript Object Notation (JSON) che definisce l'infrastruttura e la configurazione per il progetto. Il modello usa la sintassi dichiarativa, che consente di indicare lo stato che si intende distribuire senza dover scrivere la sequenza di comandi di programmazione per crearla. Nel modello si specificano le risorse da distribuire e le proprietà di tali risorse.

## <a name="benefits-of-resource-manager-templates"></a>Vantaggi dei modelli di Gestione risorse

I modelli Gestione risorse offrono i vantaggi seguenti:

* Distribuire, gestire e monitorare tutte le risorse per la soluzione come gruppo, anziché gestire singolarmente queste risorse.

* Distribuire ripetutamente la soluzione nel corso del ciclo di vita di sviluppo e avere la certezza che le risorse vengano distribuite in uno stato coerente.

* Gestire l'infrastruttura tramite modelli dichiarativi anziché script.

Se si sta tentando di scegliere tra l'uso di modelli Gestione risorse o una delle altre infrastrutture come servizi di codice, tenere presente i seguenti modelli di vantaggi:

* I nuovi servizi e funzionalità di Azure sono immediatamente disponibili nei modelli. Non appena un provider di risorse introduce nuove risorse, è possibile distribuire tali risorse tramite modelli. Con altre infrastrutture come servizi di codice, è necessario attendere che le terze parti implementino le interfacce per le nuove risorse.

* Le distribuzioni di modelli vengono gestite tramite un singolo invio del modello, anziché tramite più comandi imperativi. Gestione risorse orchestra la distribuzione di risorse interdipendenti, in modo che vengano create nell'ordine corretto. Analizza il modello e determina l'ordine corretto per la distribuzione in base ai riferimenti tra le risorse.

   ![Confronto Distribuzione modelli](./media/template-deployment-overview/template-processing.png)

* Le distribuzioni di modelli vengono rilevate nel portale di Azure. È possibile esaminare la cronologia di distribuzione e ottenere informazioni sulla distribuzione del modello. È possibile visualizzare il modello che è stato distribuito, i valori dei parametri passati ed eventuali valori di output. Altre infrastrutture come servizi del codice non vengono rilevate tramite il portale.

   ![Cronologia distribuzioni](./media/template-deployment-overview/deployment-history.png)

* Le distribuzioni modello sottoposte a convalida pre-flight. Gestione risorse controlla il modello prima di avviare la distribuzione per assicurarsi che la distribuzione abbia esito positivo. È meno probabile che la distribuzione venga interrotta in uno stato di metà fine.

* Se si usano i [criteri di Azure](../governance/policy/overview.md), la correzione dei criteri viene eseguita su risorse non conformi quando viene distribuita tramite modelli.

* Microsoft fornisce i [progetti](../governance/blueprints/overview.md) di distribuzione per soddisfare gli standard normativi e di conformità. Questi progetti includono modelli predefiniti per diverse architetture.

## <a name="idempotent"></a>Idempotente

Idempotente significa semplicemente che è possibile eseguire molte volte le stesse operazioni e ottenere lo stesso risultato. La distribuzione di un modello di Gestione risorse è idempotente. È possibile distribuire lo stesso modello molte volte e ottenere gli stessi tipi di risorse nello stesso stato. Questo concetto è importante perché significa che si ottengono risultati coerenti se si ridistribuisce un modello in un gruppo di risorse esistente o si distribuisce un modello a un nuovo gruppo di risorse.

Si supponga di aver distribuito tre risorse in un gruppo di risorse, quindi decidere di aggiungere una quarta risorsa. Anziché creare un nuovo modello che contiene solo la nuova risorsa, è possibile aggiungere la quarta risorsa al modello esistente. Quando si distribuisce il nuovo modello nel gruppo di risorse che ha già tre risorse, Gestione risorse determina le azioni da intraprendere.

Se la risorsa esiste nel gruppo di risorse e la richiesta non contiene aggiornamenti per le proprietà, non viene eseguita alcuna azione. Se la risorsa esiste ma le proprietà sono state modificate, viene aggiornata la risorsa esistente. Se la risorsa non esiste, viene creata la nuova risorsa.

Si ha la certezza che, al termine della distribuzione, le risorse si trovino sempre nello stato previsto.

## <a name="template-file"></a>File modello

All'interno del modello è possibile scrivere [espressioni di modello](template-expressions.md) che estendono le funzionalità di JSON. Queste espressioni usano le [funzioni](resource-group-template-functions.md) fornite da Gestione risorse.

Il modello include le sezioni seguenti:

* [Parameters](template-parameters.md) : specificare i valori durante la distribuzione che consentono di utilizzare lo stesso modello con ambienti diversi.

* [Variabili](template-variables.md) : definire i valori riutilizzati nei modelli. Possono essere costruiti da valori di parametro.

* [Funzioni definite dall'utente](template-user-defined-functions.md) : creare funzioni personalizzate che semplificano il modello.

* [Risorse](resource-group-authoring-templates.md#resources) : specificare le risorse da distribuire.

* [Output](template-outputs.md) : valori restituiti dalle risorse distribuite.

## <a name="template-features"></a>Funzionalità del modello

Gestione risorse analizza le dipendenze per assicurarsi che le risorse vengano create nell'ordine corretto. La maggior parte delle dipendenze è determinata implicitamente. Tuttavia, è possibile impostare in modo esplicito una dipendenza per assicurarsi che una risorsa venga distribuita prima di un'altra risorsa. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

È possibile aggiungere una risorsa al modello e, facoltativamente, distribuirla. In genere, si passa un valore di parametro che indica se la risorsa deve essere distribuita. Per ulteriori informazioni, vedere [distribuzione condizionale nei modelli gestione risorse](conditional-resource-deployment.md).

Anziché ripetere i blocchi di JSON più volte nel modello, è possibile usare un elemento Copy per specificare più di un'istanza di una variabile, una proprietà o una risorsa. Per ulteriori informazioni, vedere la pagina relativa [all'iterazione di risorse, proprietà o variabili nei modelli Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Esporta modelli

È possibile ottenere un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse o visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il [modello esportato](export-template-portal.md).

Quando si crea una soluzione dal portale, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero, perché è possibile iniziare con il modello della soluzione e personalizzarlo per soddisfare esigenze specifiche. Per un esempio completo, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Distribuzione modelli processo

Quando si distribuisce un modello, Gestione risorse converte il modello in operazioni API REST. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Converte la definizione nell'operazione dell'API REST seguente, che viene inviata al provider di risorse Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Progettazione modello

La modalità di definizione dei modelli e dei gruppi di risorse è una scelta personale e dipende dalla modalità di gestione della soluzione preferita. Ad esempio, è possibile distribuire l'applicazione a tre livelli tramite un unico modello in un singolo gruppo di risorse.

![modello a tre livelli](./media/template-deployment-overview/3-tier-template.png)

Non è tuttavia necessario definire l'intera infrastruttura in un unico modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/template-deployment-overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/template-deployment-overview/tier-templates.png)

Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà nei file di modello, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per iniziare a sviluppare un modello, vedere [usare Visual Studio Code per creare modelli Azure Resource Manager](resource-manager-tools-vs-code.md).
* Per un'introduzione al servizio Gestione risorse, incluse le funzionalità di gestione, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

