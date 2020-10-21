---
title: Panoramica dei modelli
description: Vengono descritti i vantaggi dell'utilizzo di modelli di Azure Resource Manager per la distribuzione delle risorse.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 1873c737d17fc8774ddd1276d1375799ca2da35e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280035"
---
# <a name="what-are-arm-templates"></a>Che cosa sono i modelli di Azure Resource Manager?

Con il passaggio al cloud, molti team hanno adottato metodi di sviluppo agile. Questi team eseguono un'iterazione rapida. È necessario distribuire ripetutamente le soluzioni nel cloud e verificare che l'infrastruttura sia in uno stato affidabile. Poiché l'infrastruttura è diventata parte del processo iterativo, la divisione tra le operazioni e lo sviluppo è scomparsa. I team devono gestire il codice dell'infrastruttura e dell'applicazione tramite un processo unificato.

Per soddisfare queste esigenze, è possibile automatizzare le distribuzioni e utilizzare la pratica dell'infrastruttura come codice. Nel codice è necessario definire l'infrastruttura da distribuire. Il codice dell'infrastruttura diventa parte del progetto. Proprio come il codice dell'applicazione, il codice dell'infrastruttura viene archiviato in un repository di origine e ne viene versione. Uno dei team può eseguire il codice e distribuire ambienti simili.

Per implementare l'infrastruttura come codice per le soluzioni di Azure, usare i modelli Azure Resource Manager (ARM). Il modello è un file JavaScript Object Notation (JSON) che definisce l'infrastruttura e la configurazione per il progetto. Il modello usa una sintassi dichiarativa che consente di indicare ciò che si intende distribuire senza dover scrivere la sequenza dei comandi di programmazione per crearlo. Nel modello si specificano le risorse da distribuire e le proprietà di tali risorse.

## <a name="why-choose-arm-templates"></a>Perché scegliere i modelli ARM?

Se si sta provando a decidere se usare i modelli ARM e una delle altre infrastrutture come servizi di codice, considerare i vantaggi seguenti dell'uso dei modelli:

* **Sintassi dichiarativa**: i modelli ARM consentono di creare e distribuire un'intera infrastruttura di Azure in modo dichiarativo. Ad esempio, è possibile distribuire non solo le macchine virtuali, ma anche l'infrastruttura di rete, i sistemi di archiviazione e tutte le altre risorse che potrebbero essere necessarie.

* **Risultati ripetibili**: distribuire ripetutamente l'infrastruttura durante tutto il ciclo di vita di sviluppo e avere la certezza che le risorse vengano distribuite in modo coerente. I modelli sono idempotente, il che significa che è possibile distribuire più volte lo stesso modello e ottenere gli stessi tipi di risorsa nello stesso stato. È possibile sviluppare un modello che rappresenta lo stato desiderato, anziché sviluppare molti modelli distinti per rappresentare gli aggiornamenti.

* **Orchestrazione**: non è necessario preoccuparsi delle complessità delle operazioni di ordinamento. Gestione risorse orchestra la distribuzione di risorse interdipendenti, in modo che vengano create nell'ordine corretto. Quando possibile, Gestione risorse distribuisce le risorse in parallelo, in modo che le distribuzioni vengano completate più velocemente rispetto alle distribuzioni seriali. Il modello viene distribuito tramite un solo comando, anziché tramite più comandi imperativi.

   ![Confronto Distribuzione modelli](./media/overview/template-processing.png)

* **File modulari**: è possibile suddividere i modelli in componenti più piccoli e riutilizzabili e collegarli insieme in fase di distribuzione. È anche possibile annidare un modello all'interno di un altro modello.

* **Creare una risorsa di Azure**: è possibile usare immediatamente i nuovi servizi e funzionalità di Azure nei modelli. Non appena un provider di risorse introduce nuove risorse, è possibile distribuire tali risorse tramite modelli. Non è necessario attendere l'aggiornamento degli strumenti o dei moduli prima di usare i nuovi servizi.

* **Estensibilità**: con gli [script di distribuzione](deployment-script-template.md)è possibile aggiungere script PowerShell o bash ai modelli. Gli script di distribuzione estendono la possibilità di configurare le risorse durante la distribuzione. Uno script può essere incluso nel modello o archiviato in un'origine esterna e a cui viene fatto riferimento nel modello. Gli script di distribuzione offrono la possibilità di completare la configurazione dell'ambiente end-to-end in un singolo modello ARM.

* **Test**: è possibile assicurarsi che il modello segua le linee guida consigliate eseguendone il test con ARM template tool kit (ARM-TTK). Questo kit di test è uno script di PowerShell che è possibile scaricare da [GitHub](https://github.com/Azure/arm-ttk). Il kit di strumenti semplifica lo sviluppo di competenze utilizzando il linguaggio del modello.

* **Anteprima modifiche**: è possibile usare l' [operazione](template-deploy-what-if.md) di simulazione per ottenere un'anteprima delle modifiche prima di distribuire il modello. Con simulazione, verranno visualizzate le risorse che verranno create, aggiornate o eliminate e tutte le proprietà delle risorse che verranno modificate. L'operazione di simulazione controlla lo stato corrente dell'ambiente ed elimina la necessità di gestire lo stato.

* **Convalida incorporata**: il modello viene distribuito solo dopo il passaggio della convalida. Gestione risorse controlla il modello prima di avviare la distribuzione per assicurarsi che la distribuzione abbia esito positivo. È meno probabile che la distribuzione venga interrotta in uno stato di metà fine.

* **Distribuzioni rilevate**: nella portale di Azure è possibile esaminare la cronologia di distribuzione e ottenere informazioni sulla distribuzione del modello. È possibile visualizzare il modello che è stato distribuito, i valori dei parametri passati ed eventuali valori di output. Altre infrastrutture come servizi del codice non vengono rilevate tramite il portale.

   ![Cronologia di distribuzione](./media/overview/deployment-history.png)

* **Criteri come codice**: [criteri di Azure](../../governance/policy/overview.md) è un criterio come Framework di codice per automatizzare la governance. Se si usano i criteri di Azure, la correzione dei criteri viene eseguita su risorse non conformi quando viene distribuita tramite modelli.

* **Progetti di distribuzione**: è possibile sfruttare i [progetti](../../governance/blueprints/overview.md) forniti da Microsoft per soddisfare gli standard normativi e di conformità. Questi progetti includono modelli predefiniti per diverse architetture.

* **Integrazione ci/CD**: è possibile integrare i modelli negli strumenti di integrazione continua e distribuzione continua (ci/CD), che consentono di automatizzare le pipeline di rilascio per aggiornamenti rapidi e affidabili dell'infrastruttura e dell'applicazione. Con l'attività DevOps di Azure e Gestione risorse modello è possibile usare Azure Pipelines per compilare e distribuire continuamente i progetti di modello ARM. Per altre informazioni, vedere [vs Project con pipeline](add-template-to-azure-pipelines.md) ed [esercitazione: integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines](./deployment-tutorial-pipeline.md).

* **Codice esportabile**: è possibile ottenere un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse o visualizzando il modello usato per una distribuzione specifica. La visualizzazione del [modello esportato](export-template-portal.md) è un modo utile per apprendere la sintassi del modello.

* **Strumenti di creazione**: è possibile creare modelli con [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) e l'estensione dello strumento del modello. Si ottengono IntelliSense, l'evidenziazione della sintassi, la guida inline e molte altre funzioni del linguaggio. Oltre a Visual Studio Code, è anche possibile usare [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>File modello

All'interno del modello è possibile scrivere [espressioni di modello](template-expressions.md) che estendono le funzionalità di JSON. Queste espressioni usano le [funzioni](template-functions.md) fornite da Gestione risorse.

Il modello include le sezioni seguenti:

* [Parameters](template-parameters.md) : specificare i valori durante la distribuzione che consentono di utilizzare lo stesso modello con ambienti diversi.

* [Variabili](template-variables.md) : definire i valori riutilizzati nei modelli. Possono essere costruiti da valori di parametro.

* [Funzioni definite dall'utente](template-user-defined-functions.md) : creare funzioni personalizzate che semplificano il modello.

* [Risorse](template-syntax.md#resources) : specificare le risorse da distribuire.

* [Output](template-outputs.md) : valori restituiti dalle risorse distribuite.

## <a name="template-deployment-process"></a>Distribuzione modelli processo

Quando si distribuisce un modello, Gestione risorse converte il modello in operazioni API REST. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
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
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Progettazione modello

La modalità di definizione dei modelli e dei gruppi di risorse è una scelta personale e dipende dalla modalità di gestione della soluzione preferita. Ad esempio, è possibile distribuire l'applicazione a tre livelli tramite un unico modello in un singolo gruppo di risorse.

![modello a tre livelli](./media/overview/3-tier-template.png)

Non è tuttavia necessario definire l'intera infrastruttura in un unico modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/overview/tier-templates.png)

Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](linked-templates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere [Esercitazione: Creare e distribuire il primo modello di Resource Manager](template-tutorial-create-first-template.md).
* Per informazioni sulle proprietà nei file di modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per informazioni sull'esportazione di modelli, vedere [Guida introduttiva: creare e distribuire modelli ARM usando il portale di Azure](quickstart-create-templates-use-the-portal.md).
* Per le risposte alle domande più comuni, vedere [domande frequenti sui modelli ARM](frequently-asked-questions.md).
