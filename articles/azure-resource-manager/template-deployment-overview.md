---
title: Modelli di Gestione risorse di Azure
description: Viene descritto come usare i modelli di Azure Resource Manager per la distribuzione delle risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: tomfitz
ms.openlocfilehash: 96f140cfa5e6151ad53ca242d1fc87ba3397316e
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300161"
---
# <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Con il passaggio al cloud, molti team hanno adottato metodi di sviluppo agile. Questi team eseguono un'iterazione rapida. È necessario distribuire ripetutamente le soluzioni nel cloud e verificare che l'infrastruttura sia in uno stato affidabile. Poiché l'infrastruttura è diventata parte del processo iterativo, la divisione tra le operazioni e lo sviluppo è scomparsa. I team devono gestire il codice dell'infrastruttura e dell'applicazione tramite un processo unificato.

Per soddisfare queste esigenze, è possibile automatizzare le distribuzioni e utilizzare la pratica dell'infrastruttura come codice. Nel codice è necessario definire l'infrastruttura da distribuire. Il codice dell'infrastruttura diventa parte del progetto. Proprio come il codice dell'applicazione, il codice dell'infrastruttura viene archiviato in un repository di origine e ne viene versione. Uno dei team può eseguire il codice e distribuire ambienti simili.

Per implementare l'infrastruttura come codice per le soluzioni di Azure, usare Azure Resource Manager modelli. Il modello è un file JavaScript Object Notation (JSON) che definisce l'infrastruttura e la configurazione per il progetto. Il modello usa una sintassi dichiarativa che consente di indicare ciò che si intende distribuire senza dover scrivere la sequenza dei comandi di programmazione per crearlo. Nel modello si specificano le risorse da distribuire e le proprietà di tali risorse.

## <a name="why-choose-resource-manager-templates"></a>Perché scegliere Gestione risorse modelli?

Se si sta tentando di scegliere tra l'uso di modelli Gestione risorse e una delle altre infrastrutture come servizi di codice, considerare i vantaggi seguenti dell'uso dei modelli:

* **Sintassi dichiarativa**: Gestione risorse modelli consentono di creare e distribuire un'intera infrastruttura di Azure in modo dichiarativo. Ad esempio, è possibile distribuire non solo le macchine virtuali, ma anche l'infrastruttura di rete, i sistemi di archiviazione e tutte le altre risorse che potrebbero essere necessarie.

* **Risultati ripetibili**: Distribuire ripetutamente l'infrastruttura durante tutto il ciclo di vita di sviluppo e avere la certezza che le risorse vengano distribuite in modo coerente. I modelli sono idempotente, il che significa che è possibile distribuire più volte lo stesso modello e ottenere gli stessi tipi di risorsa nello stesso stato. È possibile sviluppare un modello che rappresenta lo stato desiderato, anziché sviluppare molti modelli distinti per rappresentare gli aggiornamenti.

* **Orchestrazione**. Non è necessario preoccuparsi delle complessità delle operazioni di ordinamento. Gestione risorse orchestra la distribuzione di risorse interdipendenti, in modo che vengano create nell'ordine corretto. Quando possibile, Gestione risorse distribuisce le risorse in parallelo, in modo che le distribuzioni vengano completate più velocemente rispetto alle distribuzioni seriali. Il modello viene distribuito tramite un solo comando, anziché tramite più comandi imperativi.

   ![Confronto Distribuzione modelli](./media/template-deployment-overview/template-processing.png)

* **Convalida incorporata**: Il modello viene distribuito solo dopo il passaggio della convalida. Gestione risorse controlla il modello prima di avviare la distribuzione per assicurarsi che la distribuzione abbia esito positivo. È meno probabile che la distribuzione venga interrotta in uno stato di metà fine.

* **File modulari**: È possibile suddividere i modelli in componenti più piccoli e riutilizzabili e collegarli insieme in fase di distribuzione. È anche possibile annidare un modello all'interno di un altro modello.

* **Creare una risorsa di Azure**: Nei modelli è possibile usare immediatamente nuovi servizi e funzionalità di Azure. Non appena un provider di risorse introduce nuove risorse, è possibile distribuire tali risorse tramite modelli. Non è necessario attendere l'aggiornamento degli strumenti o dei moduli prima di usare i nuovi servizi.

* **Distribuzioni rilevate**: Nella portale di Azure è possibile esaminare la cronologia della distribuzione e ottenere informazioni sulla distribuzione del modello. È possibile visualizzare il modello che è stato distribuito, i valori dei parametri passati ed eventuali valori di output. Altre infrastrutture come servizi del codice non vengono rilevate tramite il portale.

   ![Cronologia distribuzioni](./media/template-deployment-overview/deployment-history.png)

* **Criteri come codice**: [Criteri di Azure](../governance/policy/overview.md) è un criterio come Framework di codice per automatizzare la governance. Se si usano i criteri di Azure, la correzione dei criteri viene eseguita su risorse non conformi quando viene distribuita tramite modelli.

* **Progetti di distribuzione**: È possibile sfruttare i vantaggi dei [progetti](../governance/blueprints/overview.md) forniti da Microsoft per soddisfare gli standard normativi e di conformità. Questi progetti includono modelli predefiniti per diverse architetture.

* **Integrazione ci/CD**: È possibile integrare i modelli negli strumenti per l'integrazione continua e la distribuzione continua (CI/CD), che consente di automatizzare le pipeline di rilascio per gli aggiornamenti di applicazioni e infrastrutture veloci e affidabili. Con l'attività Gestione risorse modello di Azure DevOps è possibile usare Azure Pipelines per compilare e distribuire continuamente progetti di Azure Resource Manager modello. Per altre informazioni, vedere [Visual Studio Project con pipeline](./vs-resource-groups-project-devops-pipelines.md) e [integrazione continua con Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md).

* **Codice esportabile**: È possibile ottenere un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse o visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il [modello esportato](export-template-portal.md).

* **Strumenti di creazione**: È possibile creare modelli con [Visual Studio Code](resource-manager-tools-vs-code.md) e l'estensione dello strumento del modello. Si ottengono IntelliSense, l'evidenziazione della sintassi, la guida inline e molte altre funzioni del linguaggio. Oltre a Visual Studio Code, è anche possibile usare [Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="template-file"></a>File modello

All'interno del modello è possibile scrivere [espressioni di modello](template-expressions.md) che estendono le funzionalità di JSON. Queste espressioni usano le [funzioni](resource-group-template-functions.md) fornite da Gestione risorse.

Il modello include le sezioni seguenti:

* [Parameters](template-parameters.md) : specificare i valori durante la distribuzione che consentono di utilizzare lo stesso modello con ambienti diversi.

* [Variabili](template-variables.md) : definire i valori riutilizzati nei modelli. Possono essere costruiti da valori di parametro.

* [Funzioni definite dall'utente](template-user-defined-functions.md) : creare funzioni personalizzate che semplificano il modello.

* [Risorse](resource-group-authoring-templates.md#resources) : specificare le risorse da distribuire.

* [Output](template-outputs.md) : valori restituiti dalle risorse distribuite.

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
* Per informazioni sull'esportazione di modelli, [vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).
