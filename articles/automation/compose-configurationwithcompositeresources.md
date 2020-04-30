---
title: Composizione di configurazioni DSC nella configurazione dello stato di automazione di Azure con risorse composite
description: Informazioni su come comporre le configurazioni usando risorse composite in configurazione dello stato di automazione di Azure.
keywords: powershell dsc, Desired State Configuration, powershell dsc azure
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682920"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Composizione di configurazioni DSC nella configurazione dello stato di automazione di Azure con risorse composite

Quando è necessario gestire una risorsa con più di una singola DSC (Desired state Configuration), il percorso migliore consiste nell'usare [le risorse composite](/powershell/scripting/dsc/resources/authoringresourcecomposite). Una risorsa composita è una configurazione annidata e con parametri usata come risorsa DSC all'interno di un'altra configurazione. L'uso di risorse Composite consente di creare configurazioni complesse, consentendo al contempo la gestione e la compilazione personalizzate delle risorse composite sottostanti.

Automazione di Azure consente l'[importazione e la compilazione di risorse composite](automation-dsc-compile.md). Dopo aver importato le risorse composite nell'account di automazione, è possibile usare la configurazione dello stato di automazione di Azure tramite la funzionalità di **configurazione dello stato (DSC** nel portale di Azure.

## <a name="composing-a-configuration-from-composite-resources"></a>Creazione di una configurazione da risorse composite

Prima di poter assegnare una configurazione effettuata da risorse composite nella portale di Azure, è necessario comporre la configurazione. La composizione usa la **configurazione compose** nella pagina Configurazione dello stato (DSC), mentre nella scheda **configurazioni** o **configurazioni compilate** .

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** o **configurazioni compilate** , quindi fare clic su **Componi configurazione** nel menu nella parte superiore della pagina.
1. Nell passaggio **Informazioni di base** specificare il nome della configurazione (obbligatorio) e fare clic su un punto qualsiasi nella riga di ogni risorsa composita che si vuole includere nella nuova configurazione, quindi fare clic su **Avanti** oppure fare clic sul passaggio **Codice sorgente**. Per i passaggi seguenti sono state selezionate `PSExecutionPolicy` e `RenameAndDomainJoin` le risorse composite.
   ![Screenshot delle informazioni di base nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Nel passaggio **Codice sorgente** viene visualizzato l'aspetto della configurazione creata con le risorse composite selezionate. È possibile notare che tutti parametri sono stati uniti e passati alla risorsa composita. Dopo aver esaminato il nuovo codice sorgente, fare clic su **Avanti** oppure fare clic sul passaggio **Parametri**.
   ![Screenshot del codice sorgente nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Nel passaggio **parametri** , il parametro per ogni risorsa composita viene esposto in modo che sia possibile fornire i valori. Se per il parametro esiste una descrizione, questa viene visualizzata accanto al campo del parametro. Se un parametro è di `PSCredential` tipo, l'elenco a discesa fornisce un elenco di oggetti **Credential** nell'account di automazione corrente. È disponibile anche un'opzione **+ Add an Credential** . Dopo aver specificato tutti i parametri necessari, fare clic su **Salva e compila**.
   ![Screenshot dei parametri nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Dopo averla salvata, la configurazione viene inoltrata per essere compilata. Lo stato del processo di visualizzazione viene visualizzato come per una qualsiasi configurazione importata. Per altre informazioni, vedere [Visualizzazione di un processo di compilazione](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando la compilazione viene completata correttamente, la nuova configurazione viene visualizzata nella scheda **configurazioni compilate** . È quindi possibile assegnare la configurazione a un nodo gestito, usando la procedura descritta in [riassegnazione di un nodo a una configurazione del nodo diversa](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come caricare i nodi, vedere Caricamento [di computer per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [cmdlet di configurazione dello stato di automazione di Azure](/powershell/module/azurerm.automation/#automation).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [distribuzione continua con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).
