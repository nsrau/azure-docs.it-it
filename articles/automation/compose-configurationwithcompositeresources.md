---
title: Composizione delle configurazioni DSC nella configurazione dello stato di automazione di Azure usando le risorse composteComposing DSC configurations in Azure Automation State Configuration using Azure Automation State Configuration using Azure Automation State Configuration
description: Informazioni su come comporre configurazioni usando risorse composite in Configurazione stato di automazione di Azure.Learn how to compose configurations using composite resources in Azure Automation State Configuration.
keywords: powershell dsc, Desired State Configuration, powershell dsc azure
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682920"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Composizione delle configurazioni DSC nella configurazione dello stato di automazione di Azure usando le risorse composteComposing DSC configurations in Azure Automation State Configuration using Azure Automation State Configuration using Azure Automation State Configuration

Quando è necessario gestire le risorse con più di una singola configurazione dello stato desiderato (DSC), il percorso migliore consiste nell'utilizzare [risorse composte.](/powershell/scripting/dsc/resources/authoringresourcecomposite) Una risorsa composita è una configurazione annidata e con parametri usata come risorsa DSC all'interno di un'altra configurazione. L'uso di risorse composite consente di creare configurazioni complesse consentendo la gestione e la compilazione delle risorse composite sottostanti singolarmente.

Automazione di Azure consente l'[importazione e la compilazione di risorse composite](automation-dsc-compile.md). Dopo aver importato risorse composte nell'account di automazione, è possibile usare la configurazione dello stato di automazione di Azure tramite la funzionalità **Configurazione stato (DSC)** nel portale di Azure.Once you've imported composite resources into your Automation account, you can use Azure Automation State Configuration through the State Configuration (DSC feature in the Azure portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Creazione di una configurazione da risorse composite

Prima di poter assegnare una configurazione costituita da risorse composte nel portale di Azure, è necessario comporre la configurazione. Composizione utilizza la **configurazione di Componi** nella pagina Configurazione stato (DSC) mentre nella scheda **Configurazioni** o **Configurazioni compilate** .

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni** o **Configurazioni compilate,** quindi fare clic su **Scrivi configurazione** nel menu nella parte superiore della pagina.
1. Nell passaggio **Informazioni di base** specificare il nome della configurazione (obbligatorio) e fare clic su un punto qualsiasi nella riga di ogni risorsa composita che si vuole includere nella nuova configurazione, quindi fare clic su **Avanti** oppure fare clic sul passaggio **Codice sorgente**. Per i passaggi seguenti `PSExecutionPolicy` `RenameAndDomainJoin` sono state selezionate e composite resources.
   ![Screenshot delle informazioni di base nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Nel passaggio **Codice sorgente** viene visualizzato l'aspetto della configurazione creata con le risorse composite selezionate. È possibile notare che tutti parametri sono stati uniti e passati alla risorsa composita. Dopo aver esaminato il nuovo codice sorgente, fare clic su **Avanti** oppure fare clic sul passaggio **Parametri**.
   ![Screenshot del codice sorgente nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Nel passaggio **Parametri,** il parametro per ogni risorsa composita viene esposto in modo che i valori possono essere forniti. Se per il parametro esiste una descrizione, questa viene visualizzata accanto al campo del parametro. Se un parametro è di `PSCredential` tipo, l'elenco a discesa fornisce un elenco di oggetti **Credential** nell'account di automazione corrente. È disponibile anche **un'opzione di aggiunta di credenziali.** Dopo aver specificato tutti i parametri necessari, fare clic su **Salva e compila**.
   ![Screenshot dei parametri nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Dopo averla salvata, la configurazione viene inoltrata per essere compilata. Lo stato del processo di visualizzazione viene visualizzato come per una qualsiasi configurazione importata. Per altre informazioni, vedere [Visualizzazione di un processo di compilazione](automation-dsc-getting-started.md#viewing-a-compilation-job).

Al termine della compilazione, la nuova configurazione viene visualizzata nella scheda **Configurazioni compilate.** È quindi possibile assegnare la configurazione a un nodo gestito, attenendosi alla procedura descritta in [Riassegnazione di un nodo a una configurazione di nodo diversa.](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni su come eseguire l'onboarding dei nodi, vedere [Onboarding di macchine per la gestione da parte](automation-dsc-onboarding.md)della configurazione dello stato di automazione di Azure .
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere Cmdlet per la configurazione dello stato di [automazione di Azure.](/powershell/module/azurerm.automation/#automation)
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite Configurazione dello stato](automation-dsc-cd-chocolatey.md)di automazione di Azure e Chocolatey .
