---
title: Creazione di configurazioni DSC in Desired State Configuration (DSC) di Automazione di Azure usando risorse composite
description: Informazioni su come creare configurazioni usando risorse composite in Desired State Configuration (DSC) di Automazione di Azure
keywords: powershell dsc, Desired State Configuration, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9d16d9d9967af5bf8f9e13406e13d5637325f52
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243359"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Creazione di configurazioni DSC in Desired State Configuration (DSC) di Automazione di Azure usando risorse composite

Se si deve gestire una risorsa con più di una configurazione DSC, è consigliabile usare le [risorse composite](/powershell/scripting/dsc/resources/authoringresourcecomposite). Una risorsa composita è una configurazione annidata e con parametri usata come risorsa DSC all'interno di un'altra configurazione. Consente di creare configurazioni complesse e al tempo stesso gestire e creare singolarmente le risorse composite sottostanti, vale a dire le configurazioni con parametri.

Automazione di Azure consente l'[importazione e la compilazione di risorse composite](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Dopo aver importato le risorse composite nell'account di Automazione, è possibile usare l'esperienza **Crea configurazione** nella pagina **Configurazione stato (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Creazione di una configurazione da risorse composite

Prima di poter assegnare una configurazione costituita da risorse composite nel portale di Azure, è necessario crearla. Questa operazione può essere eseguita usando **Crea configurazione** nella pagina **Configurazione stato (DSC)** oppure usando le schede **Configurazioni** o **Configurazioni compilate**.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina **Account di automazione** selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina **Configurazione stato (DSC)** fare clic sulle schede **Configurazioni** o **Configurazioni compilate**, quindi selezionare **Crea configurazione** nel menu nella parte superiore della pagina.
1. Nell passaggio **Informazioni di base** specificare il nome della configurazione (obbligatorio) e fare clic su un punto qualsiasi nella riga di ogni risorsa composita che si vuole includere nella nuova configurazione, quindi fare clic su **Avanti** oppure fare clic sul passaggio **Codice sorgente**. Per i passaggi seguenti, sono state selezionate le risorse **PSExecutionPolicy** e **RenameAndDomainJoin**.
   ![Screenshot delle informazioni di base nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Nel passaggio **Codice sorgente** viene visualizzato l'aspetto della configurazione creata con le risorse composite selezionate. È possibile notare che tutti parametri sono stati uniti e passati alla risorsa composita. Dopo aver esaminato il nuovo codice sorgente, fare clic su **Avanti** oppure fare clic sul passaggio **Parametri**.
   ![Screenshot del codice sorgente nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Nel passaggio **Parametri** viene esposto il parametro di ogni risorsa composita in modo che sia possibile specificarli. Se per il parametro esiste una descrizione, questa viene visualizzata accanto al campo del parametro. Se un campo è un parametro di tipo **PSCredential**, l'elenco a discesa per la configurazione offre un elenco di oggetti **Credential** nell'account di Automazione corrente. È disponibile anche un'opzione **+ Add an Credential** . Dopo aver specificato tutti i parametri necessari, fare clic su **Salva e compila**.
   ![Screenshot dei parametri nella pagina di creazione della configurazione](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Dopo averla salvata, la configurazione viene inoltrata per essere compilata. Lo stato del processo di visualizzazione viene visualizzato come per una qualsiasi configurazione importata. Per altre informazioni, vedere [Visualizzazione di un processo di compilazione](automation-dsc-getting-started.md#viewing-a-compilation-job).

Se la compilazione è stata completata, la nuova configurazione viene visualizzata nella scheda **Configurazioni compilate**. Se è visibile in questa scheda, può essere assegnata a un nodo gestito seguendo la procedura descritta in [Riassegnazione di un nodo a una diversa configurazione di nodo](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sull'onboarding dei nodi, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
