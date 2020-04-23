---
title: Panoramica dei modelli
description: Descrive i vantaggi dell'uso dei modelli di Azure Resource Manager per la distribuzione delle risorse.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086335"
---
# <a name="what-are-arm-templates"></a>Che cosa sono i modelli di Azure Resource Manager?

Con il passaggio al cloud, molti team hanno adottato metodi di sviluppo agili. Questi team iterano rapidamente. Devono distribuire ripetutamente le soluzioni nel cloud e sapere che la propria infrastruttura è in uno stato affidabile. Poiché l'infrastruttura è diventata parte del processo iterativo, la divisione tra operazioni e sviluppo è scomparsa. I team devono gestire l'infrastruttura e il codice dell'applicazione tramite un processo unificato.

Per affrontare questi problemi, è possibile automatizzare le distribuzioni e utilizzare la pratica dell'infrastruttura come codice. Nel codice si definisce l'infrastruttura da distribuire. Il codice dell'infrastruttura diventa parte del progetto. Proprio come il codice dell'applicazione, si archivia il codice dell'infrastruttura in un repository di origine e lo si versione. Qualsiasi membro del team può eseguire il codice e distribuire ambienti simili.

Per implementare l'infrastruttura come codice per le soluzioni Azure, usare i modelli di Azure Resource Manager (ARM). Il modello è un file JSON (JavaScript Object Notation) che definisce l'infrastruttura e la configurazione per il progetto. Il modello usa una sintassi dichiarativa che consente di indicare ciò che si intende distribuire senza dover scrivere la sequenza dei comandi di programmazione per crearlo. Nel modello specificare le risorse da distribuire e le proprietà per tali risorse.

## <a name="why-choose-arm-templates"></a>Perché scegliere i modelli ARM?

Se si sta tentando di decidere tra l'utilizzo di modelli ARM e una delle altre infrastrutture come servizi di codice, considerare i seguenti vantaggi dell'utilizzo dei modelli:

* **Sintassi dichiarativa:** i modelli ARM consentono di creare e distribuire un'intera infrastruttura di Azure in modo dichiarativo. Ad esempio, è possibile distribuire non solo le macchine virtuali, ma anche l'infrastruttura di rete, i sistemi di archiviazione e qualsiasi altra risorsa necessaria.

* **Risultati ripetibili**: Distribuire ripetutamente l'infrastruttura durante il ciclo di vita dello sviluppo e avere fiducia nelle risorse distribuite in modo coerente. I modelli sono idempotenti, il che significa che è possibile distribuire lo stesso modello più volte e ottenere gli stessi tipi di risorse nello stesso stato. È possibile sviluppare un modello che rappresenta lo stato desiderato, anziché sviluppare molti modelli separati per rappresentare gli aggiornamenti.

* **Orchestrazione**: Non devi preoccuparti della complessità delle operazioni di ordinazione. Resource Manager orchestra la distribuzione delle risorse interdipendenti in modo che vengano create nell'ordine corretto. Quando possibile, Resource Manager distribuisce le risorse in parallelo in modo che le distribuzioni vengano completate più rapidamente rispetto alle distribuzioni seriali. Distribuire il modello tramite un comando, anziché tramite più comandi imperativi.

   ![Confronto tra la distribuzione dei modelli](./media/overview/template-processing.png)

* **File modulari**: È possibile suddividere i modelli in componenti più piccoli e riutilizzabili e collegarli tra loro al momento della distribuzione. È inoltre possibile nidificare un modello all'interno di un altro modello.

* **Creare qualsiasi risorsa**di Azure: è possibile usare immediatamente i nuovi servizi e le nuove funzionalità di Azure nei modelli. Non appena un provider di risorse introduce nuove risorse, è possibile distribuire tali risorse tramite modelli. Non è necessario attendere l'aggiornamento di strumenti o moduli prima di utilizzare i nuovi servizi.

* **Estensibilità**: Con gli script di [distribuzione,](deployment-script-template.md)è possibile aggiungere script PowerShell o Bash ai modelli. Gli script di distribuzione estendono la possibilità di configurare le risorse durante la distribuzione. Uno script può essere incluso nel modello o archiviato in un'origine esterna e a cui viene fatto riferimento nel modello. Gli script di distribuzione consentono di completare la configurazione dell'ambiente end-to-end in un unico modello ARM.

* **Test**: È possibile assicurarsi che il modello segua le linee guida consigliate testandolo con il kit di strumenti del modello ARM (arm-ttk). Questo kit di test è uno script di PowerShell che è possibile scaricare da [GitHub](https://github.com/Azure/arm-ttk). Il kit di strumenti semplifica lo sviluppo di competenze utilizzando il linguaggio dei modelli.

* **Anteprima delle modifiche**: È possibile utilizzare l'operazione [di what-if](template-deploy-what-if.md) per ottenere un'anteprima delle modifiche prima di distribuire il modello. Con what-if, vengono visualizzate le risorse che verranno create, aggiornate o eliminate e le proprietà delle risorse che verranno modificate. L'operazione di creazione-se controlla lo stato corrente dell'ambiente ed elimina la necessità di gestire lo stato.

* **Convalida predefinita:** il modello viene distribuito solo dopo il superamento della convalida. Resource Manager controlla il modello prima di avviare la distribuzione per assicurarsi che la distribuzione abbia esito positivo. È meno probabile che la distribuzione si interrompa in uno stato semifinito.

* **Distribuzioni rilevate:** nel portale di Azure è possibile esaminare la cronologia della distribuzione e ottenere informazioni sulla distribuzione del modello. È possibile visualizzare il modello distribuito, i valori dei parametri passati ed eventuali valori di output. Altre infrastrutture come servizi di codice non vengono monitorate tramite il portale.

   ![Cronologia di distribuzione](./media/overview/deployment-history.png)

* **Criteri come codice:** [Criteri di Azure](../../governance/policy/overview.md) è un criterio come framework di codice per automatizzare la governance. Se si usano criteri di Azure, la correzione dei criteri viene eseguita nelle risorse non conformi quando viene distribuita tramite modelli.

* **Blueprint di distribuzione**: È possibile sfruttare [i blueprint](../../governance/blueprints/overview.md) forniti da Microsoft per soddisfare gli standard normativi e di conformità. Questi blueprint includono modelli predefiniti per varie architetture.

* **Integrazione CI/CD**: è possibile integrare i modelli negli strumenti di integrazione continua e distribuzione continua (CI/CD), in grado di automatizzare le pipeline di rilascio per aggiornamenti rapidi e affidabili dell'infrastruttura e delle applicazioni. Usando Azure DevOps e l'attività modello di Resource Manager, è possibile usare le pipeline di Azure per compilare e distribuire continuamente progetti di modello ARM. Per altre informazioni, vedere Progetto VS con pipeline ed [Esercitazione: Integrazione continua dei modelli di Azure Resource Manager con le pipeline di Azure.To](./deployment-tutorial-pipeline.md)learn more, see VS project [with pipelines](add-template-to-azure-pipelines.md) and Tutorial: Continuous integration of Azure Resource Manager templates with Azure Pipelines .

* **Codice esportabile:** è possibile ottenere un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse o visualizzando il modello utilizzato per una distribuzione specifica. La visualizzazione del [modello esportato](export-template-portal.md) è un modo utile per conoscere la sintassi del modello.

* **Strumenti**di creazione : È possibile creare modelli con [Visual Studio Code](use-vs-code-to-create-template.md) e l'estensione dello strumento modello. Si ottiene intellisense, evidenziazione della sintassi, aiuto in linea, e molte altre funzioni del linguaggio. Oltre al codice di Visual Studio, è anche possibile utilizzare [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>File modello

All'interno del modello, è possibile scrivere [espressioni di modello](template-expressions.md) che estendono le funzionalità di JSON. Queste espressioni utilizzano le [funzioni](template-functions.md) fornite da Gestione risorse.

Il modello ha le seguenti sezioni:

* [Parametri:](template-parameters.md) fornire valori durante la distribuzione che consentono l'utilizzo dello stesso modello in ambienti diversi.

* [Variabili:](template-variables.md) consente di definire i valori riutilizzati nei modelli. Possono essere costruiti dai valori dei parametri.

* [Funzioni definite dall'utente:](template-user-defined-functions.md) consente di creare funzioni personalizzate che semplificano il modello.

* [Risorse:](template-syntax.md#resources) specificare le risorse da distribuire.

* [Output:](template-outputs.md) restituiscono valori dalle risorse distribuite.

## <a name="template-deployment-process"></a>Processo di distribuzione dei modelli

Quando si distribuisce un modello, Resource Manager converte il modello in operazioni dell'API REST. Ad esempio, quando Resource Manager riceve un modello con la definizione di risorsa seguente:

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

## <a name="template-design"></a>Progettazione di modelli

La modalità di definizione dei modelli e dei gruppi di risorse è una scelta personale e dipende dalla modalità di gestione della soluzione preferita. Ad esempio, è possibile distribuire l'applicazione a tre livelli tramite un unico modello in un singolo gruppo di risorse.

![modello a tre livelli](./media/overview/3-tier-template.png)

Non è tuttavia necessario definire l'intera infrastruttura in un unico modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riusare i modelli per altre soluzioni. Per distribuire una soluzione specifica è necessario creare un modello master che colleghi tutti i modelli necessari. L'immagine seguente illustra come distribuire una soluzione a tre livelli tramite un modello padre che include tre modelli annidati.

![modello a tre livelli annidati](./media/overview/nested-tiers-template.png)

Se si immaginano livelli con cicli di vita separati, è possibile distribuire i tre livelli a gruppi di risorse separati. Si noti che le risorse possono comunque essere collegate alle risorse in altri gruppi.

![modello a livelli](./media/overview/tier-templates.png)

Per informazioni sui modelli annidati, vedere [Uso di modelli collegati con Azure Resource Manager](linked-templates.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere [Esercitazione: Creare e distribuire il primo modello ARM.](template-tutorial-create-first-template.md)
* Per informazioni sulle proprietà dei file modello, vedere Informazioni sulla struttura e la [sintassi dei modelli ARM](template-syntax.md).
* Per altre informazioni sull'esportazione dei modelli, vedere [Guida introduttiva: Creare e distribuire modelli ARM tramite il portale](quickstart-create-templates-use-the-portal.md)di Azure.
