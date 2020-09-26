---
title: Domande frequenti sul modello ARM
description: Domande frequenti sui modelli Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47ac8d0e3172645ec168d5cfe7a002d84765b864
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333121"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Domande frequenti sui modelli ARM

Questo articolo fornisce le risposte alle domande frequenti sui modelli di Azure Resource Manager (ARM).

## <a name="getting-started"></a>Guida introduttiva

* **Che cosa sono i modelli ARM e perché è consigliabile usarli?**

  I modelli ARM sono file JSON in cui si definiscono gli elementi da distribuire in Azure. I modelli consentono di implementare una soluzione di infrastruttura come codice per Azure. L'organizzazione può distribuire ripetutamente e in modo affidabile l'infrastruttura necessaria in ambienti diversi.
  
  Per altre informazioni sul modo in cui i modelli ARM consentono di gestire l'infrastruttura di Azure, vedere [che cosa sono i modelli ARM?](overview.md)

* **Ricerca per categorie iniziare a usare i modelli?**

  Per semplificare la creazione di modelli ARM, sono necessari gli strumenti appropriati. Si consiglia di installare [Visual Studio Code](https://code.visualstudio.com/) e l' [estensione degli strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Per una rapida introduzione a questi strumenti, vedere [Guida introduttiva: creare modelli di Azure Resource Manager con Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  Quando si è pronti per ottenere informazioni sulla creazione di modelli ARM, avviare la [serie di esercitazioni per principianti sui modelli ARM](template-tutorial-create-first-template.md). Queste esercitazioni illustrano in modo dettagliato il processo di creazione di un modello ARM. Vengono fornite informazioni sulle diverse sezioni del modello e su come interagiscono. Questo contenuto è disponibile anche come [modulo Microsoft Learn](/learn/modules/authoring-arm-templates/).

* **È consigliabile usare I modelli ARM o la bonifica per eseguire la distribuzione in Azure?**

  Usare l'opzione migliore. Entrambi i servizi facilitano l'automazione delle distribuzioni in Azure.
  
  Si ritiene che esistano vantaggi nell'uso di modelli ARM rispetto ad altri servizi di infrastruttura come codice. Per informazioni su questi vantaggi, vedere [perché scegliere i modelli ARM?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Compilazione 2020

* **Ho perso la presentazione in Microsoft Build 2020. La presentazione è disponibile per la visualizzazione?**

  Sì, Guarda il video in [qualsiasi momento](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Dove è possibile reperire altre informazioni sulle nuove funzionalità annunciate in Build?**

  Per informazioni generali sulle funzionalità che stiamo lavorando, partecipa al [gruppo Yammer di distribuzioni di Azure Advisor](https://aka.ms/ARMMeet).

  Per informazioni sul nuovo linguaggio del modello, [iscriversi per le notifiche](https://aka.ms/armLangUpdates).

  Per informazioni sulle specifiche dei modelli, vedere [Azure Resource Manager specifiche del modello (anteprima)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Creazione e test di modelli

* **Dove è possibile ottenere informazioni sulle procedure consigliate per i modelli ARM?**

  Per indicazioni su come implementare i modelli, vedere procedure consigliate per il [modello ARM](template-best-practices.md). Dopo aver creato un modello, eseguire [ARM test Toolkit](https://github.com/azure/arm-ttk). Verifica se il modello corrisponde alle procedure consigliate.

* **Ho configurato l'ambiente tramite il portale. È possibile ottenere il modello da un gruppo di risorse esistente?**

  Sì, è possibile [esportare il modello](export-template-portal.md) da un gruppo di risorse. Il modello esportato è un valido punto di partenza per acquisire familiarità con i modelli, ma è probabile che si desideri modificarlo prima di utilizzarlo in un ambiente di produzione.
  
  Quando si esporta il modello, è possibile selezionare le risorse che si desidera includere nel modello.

* **È possibile creare un gruppo di risorse in un modello ARM e distribuirvi risorse?**

  Sì, è possibile creare un gruppo di risorse in un modello quando si distribuisce il modello al livello della sottoscrizione di Azure. Per un esempio di creazione di un gruppo di risorse e di distribuzione delle risorse, vedere [risorse e gruppi](deploy-to-subscription.md#resource-groups)di risorse.

* **È possibile creare una sottoscrizione in un modello ARM?**

  Non ancora, ma stiamo lavorando.

* **Come è possibile testare il modello prima di distribuirlo?**

  Prima di distribuirli, è consigliabile eseguire [ARM test Toolkit](https://github.com/azure/arm-ttk) e l' [operazione](template-deploy-what-if.md) di simulazione sui modelli. Il Toolkit di test controlla se il modello usa le procedure consigliate. Fornisce avvisi quando identifica le modifiche che potrebbero migliorare la modalità di implementazione del modello.

  L'operazione di simulazione mostra le modifiche che il modello apporterà all'ambiente. È possibile visualizzare le modifiche impreviste prima di distribuirle. Cosa-se restituisce anche eventuali errori che possono essere rilevati durante la convalida preliminare. Ad esempio, se il modello contiene un errore sintattico, viene restituito l'errore. Restituisce anche eventuali errori che possono determinare sullo stato finale delle risorse distribuite. Se, ad esempio, il modello distribuisce un account di archiviazione con un nome già in uso, cosa-se restituisce l'errore.

* **Dove è possibile trovare informazioni sulle proprietà disponibili per ogni tipo di risorsa?**

  VS Code fornisce IntelliSense per l'utilizzo delle proprietà della risorsa. È anche possibile visualizzare il [riferimento al modello](/azure/templates/) per le proprietà e le descrizioni.

* **È necessario creare più istanze di un tipo di risorsa. Ricerca per categorie creare un iteratore nel modello?**

  Usare l'elemento Copy per specificare più di un'istanza. È possibile usare copia su [risorse](copy-resources.md), [Proprietà](copy-properties.md), [variabili](copy-variables.md)e [output](copy-outputs.md).

## <a name="template-language"></a>Lingua del modello

* **Ho sentito dire che stai lavorando a un nuovo linguaggio del modello. Dove è possibile reperire ulteriori informazioni?**

  Per visualizzare l'anteprima del nuovo linguaggio, vedere [Project bicipit repository](https://github.com/Azure/bicep). Per rimanere sempre aggiornati sulla nuova lingua, [iscriversi per le notifiche](https://aka.ms/armLangUpdates).

* **È previsto un piano per supportare la creazione di modelli in YAML?**

  Attualmente non è previsto il supporto di YAML. Il nuovo linguaggio del modello offrirà una soluzione più facile da usare rispetto a YAML o JSON.

* **È ancora possibile scrivere modelli in JSON dopo che il nuovo linguaggio del modello è stato rilasciato?**

  Sì, è possibile continuare a usare i modelli JSON.

* **Si offrirà uno strumento per convertire i modelli JSON nel nuovo linguaggio del modello?**

  Sì.

## <a name="template-specs"></a>Specifiche di modello

* **Come si può partecipare alla versione di anteprima delle specifiche dei modelli?**

  [Aggiungere l'elenco di attesa per le specifiche del](https://aka.ms/templateSpecsWaitlist) modello.

* **Come sono correlate le specifiche di modello e i progetti di Azure?**

  I progettisti di Azure utilizzeranno le specifiche dei modelli nell'implementazione sostituendo la `blueprint definition` risorsa con una `template spec` risorsa. Verrà fornito un percorso di migrazione per convertire la definizione del progetto in una specifica del modello, ma le API di definizione del progetto saranno ancora supportate. Non sono state apportate modifiche alla `blueprint assignment` risorsa. I progetti rimarranno un'esperienza utente per comporre un ambiente governato in Azure.

* **Le specifiche di modello sostituiscono i modelli collegati?**

  No, ma le specifiche dei modelli sono progettate per funzionare correttamente con i modelli collegati. Prima di distribuire il modello padre, non è necessario spostare il modello collegato in un endpoint accessibile pubblicamente. Al contrario, si crea il pacchetto del modello padre e dei relativi artefatti insieme quando si crea la specifica del modello.

* **Le specifiche di modello possono essere condivise tra le sottoscrizioni?**

  Sì, possono essere usate tra le sottoscrizioni, purché l'utente disponga dell'accesso in lettura alla specifica del modello. Le specifiche del modello non possono essere usate tra i tenant.

## <a name="scripts-in-templates"></a>Script nei modelli

* **È possibile includere uno script nel modello per eseguire attività che non sono possibili in un modello?**

  Sì, usare gli [script di distribuzione](deployment-script-template.md). Nei modelli è possibile includere Azure PowerShell o script dell'interfaccia della riga di comando di Azure. La funzionalità è in anteprima.

* **È comunque possibile usare estensioni di script personalizzate e DSC (Desired state Configuration)?**

  Queste opzioni sono ancora disponibili e non sono state modificate. Gli script di distribuzione sono progettati per eseguire azioni che non sono correlate al Guest della macchina virtuale. Se è necessario eseguire uno script in un sistema operativo host in una macchina virtuale, l'estensione dello script personalizzata e/o DSC è la scelta migliore. Tuttavia, gli script di distribuzione presentano dei vantaggi, ad esempio l'impostazione della durata del timeout.

* **Gli script di distribuzione sono supportati in Azure per enti pubblici?**

  Sì, è possibile usare gli script di distribuzione in US Gov Arizona e US Gov Virginia.

## <a name="preview-changes-before-deployment"></a>Anteprima modifiche prima della distribuzione

* **È possibile visualizzare in anteprima le modifiche che si verificheranno prima di distribuire un modello?**

  Sì, usare la [funzionalità](template-deploy-what-if.md)simulazione. Viene valutato lo stato corrente dell'ambiente e viene confrontato con lo stato esistente dopo la distribuzione. È possibile esaminare le modifiche riepilogate per assicurarsi che il modello non contenga risultati imprevisti.

* **È possibile usare la funzionalità di simulazione con le modalità incrementale e completa?**

  Sì, sono supportate entrambe le [modalità di distribuzione](deployment-modes.md) . Per un esempio dell'uso della modalità incrementale, vedere [eseguire un'operazione](template-deploy-what-if.md#run-what-if-operation)di simulazione. Per un esempio dell'uso della modalità completa, vedere [confirm deletion](template-deploy-what-if.md#confirm-deletion).

* **Che cosa si può fare con i modelli collegati?**

  Sì, cosa-se valuta lo stato del modello padre e dei relativi modelli collegati.

* **È possibile usare la funzionalità di simulazione in una pipeline di Azure?**

  Sì, è possibile usare l'utilità di simulazione per verificare che la pipeline continui.

* **Quando si usa simulazione, vengono visualizzate le modifiche apportate alle proprietà che non sono presenti nel modello. Si tratta di un "rumore" previsto?**

  Cosa-se è in anteprima. Stiamo lavorando per ridurre il rumore. Per contribuire a migliorare inviando i problemi nel repository GitHub, vedere: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Visualizzatore modelli

* **Esiste un modo per visualizzare il modello ARM e le relative risorse?**

  Abbiamo un' [estensione di vs code fornita dalla community](https://aka.ms/ARMVisualizer) che offre un ottimo lavoro per la visualizzazione del modello ARM. Mostra le risorse che vengono distribuite e le relazioni tra di esse.

* **È possibile usare il Visualizzatore modelli al di fuori della VS Code?**

  Il Visualizzatore modelli viene visualizzato in anteprima nel portale. Per altre informazioni, guardare questa [breve sessione dalla compilazione](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Limiti di distribuzione

* **Quanti gruppi di risorse è possibile distribuire in una singola operazione di distribuzione?**

  In passato, questo limite era di cinque gruppi di risorse. È stata recentemente aumentata a 800 gruppi di risorse. Per altre informazioni, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

* **Si è ricevuto un errore relativo al limite di 800 distribuzioni nella cronologia di distribuzione. Cosa dovrei fare?**

  Si sta cambiando il modo in cui viene mantenuta la cronologia di distribuzione per un gruppo di risorse. In passato era necessario eliminare manualmente le distribuzioni da questa cronologia per evitare questo errore. A partire da giugno 2020, verranno eliminate automaticamente le distribuzioni dalla cronologia Man seconda del limite. Per alter informazioni, vedere [Eliminazioni automatiche dalla cronologia delle distribuzioni](deployment-history-deletions.md).

  L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.

## <a name="templates-and-devops"></a>Modelli e DevOps

* **È possibile integrare I modelli ARM in Azure Pipelines?**

  Sì. Per una spiegazione su come usare il modello e le pipeline, vedere [esercitazione: integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines](deployment-tutorial-pipeline.md) e [integrare modelli ARM con Azure Pipelines](add-template-to-azure-pipelines.md).

* **È possibile usare le azioni di GitHub per distribuire un modello?**

  Sì, vedere [distribuire modelli di Azure Resource Manager usando le azioni di GitHub](deploy-github-actions.md).

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ai modelli ARM, vedere [che cosa sono i modelli ARM?](overview.md).
