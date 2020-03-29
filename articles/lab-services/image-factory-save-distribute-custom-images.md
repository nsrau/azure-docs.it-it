---
title: Salvare e distribuire immagini in Azure DevTest Labs Documenti Microsoft
description: Questo articolo illustra i passaggi per salvare le immagini personalizzate dalle macchine virtuali (VM) già create in Azure DevTest Labs.This article gives you the steps to save custom images from the already created virtual machines (VMs) in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759432"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Salvare immagini personalizzate e distribuirle in più lab
Questo articolo illustra i passaggi per salvare le immagini personalizzate dalle macchine virtuali (VM) già create. Viene inoltre illustrato come distribuire queste immagini personalizzate ad altri laboratori DevTest dell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti
I seguenti articoli dovrebbero già essere in atto:

- Laboratorio per la factory di immagini in Azure DevTest Labs.A lab for the Image Factory in Azure DevTest Labs.
- Progetto DevOps di Azure usato per automatizzare la factory di immagini.
- Percorso del codice sorgente contenente gli script e la configurazione (nell'esempio, nello stesso progetto DevOps menzionato nel passaggio precedente).
- Definizione di compilazione per orchestrare le attività di Azure Powershell.Build definition to orchestrate the Azure Powershell tasks.

Se necessario, seguire i passaggi descritti in Eseguire una factory di [immagini da DevOps](image-factory-set-up-devops-lab.md) di Azure per creare o configurare questi elementi. 

## <a name="save-vms-as-generalized-vhds"></a>Salvare le macchine virtuali come dischi rigidi virtuali generalizzatiSave VMs as generalized VHDs
Salvare le macchine virtuali esistenti come dischi rigidi virtuali generalizzati.  È disponibile uno script di PowerShell di esempio per salvare le macchine virtuali esistenti come dischi rigidi virtuali generalizzati. Per usarlo, aggiungere innanzitutto un'altra attività di Azure Powershell alla definizione di compilazione, come illustrato nell'immagine seguente:To use it, first, add another **Azure Powershell** task to the build definition as shown in the following image:

![Passaggio Aggiungi Azure PowerShellAdd Azure PowerShell step](./media/save-distribute-custom-images/powershell-step.png)

Una volta che hai la nuova attività nell'elenco, seleziona l'elemento in modo che possiamo inserire tutti i dettagli come mostrato nell'immagine seguente: 

![Impostazioni di PowerShellPowerShell settings](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Immagini personalizzate generalizzate e specializzate
Nel [portale di Azure](https://portal.azure.com), quando si crea un'immagine personalizzata da una macchina virtuale, è possibile scegliere di avere un'immagine personalizzata generalizzata o specializzata.

- **Immagine personalizzata specializzata:** Sysprep/Deprovision NON è stato eseguito nel computer. Significa che l'immagine è una copia esatta del disco del sistema operativo nella macchina virtuale esistente (uno snapshot).  Gli stessi file, applicazioni, account utente, nome computer e così via, sono tutti presenti quando creiamo una nuova macchina da questa immagine personalizzata.
- **Immagine personalizzata generalizzata:** Sysprep/Deprovision è stato eseguito nel computer.  Quando questo processo viene eseguito, rimuove gli account utente, rimuove il nome del computer, rimuove gli hive del Registro di sistema dell'utente e così via, con l'obiettivo di generalizzare l'immagine in modo che possa essere personalizzata durante la creazione di un'altra macchina virtuale.  Quando si generalizza una macchina virtuale (eseguendo sysprep), il processo elimina la macchina virtuale corrente, non sarà più funzionale.

Lo script per l'ancoraggio di immagini personalizzate in Factory immagini salverà i dischi rigidi virtuali per tutte le macchine virtuali create nel passaggio precedente (identificate in base a un tag sulla risorsa in Azure).

## <a name="update-configuration-for-distributing-images"></a>Aggiornare la configurazione per la distribuzione delle immagini
Il passaggio successivo del processo consiste nell'eseguire il push delle immagini personalizzate dal lab della factory di immagini a tutti gli altri lab che ne hanno bisogno. La parte principale di questo processo è il file di configurazione **labs.json.** È possibile trovare questo file nella cartella **Configuration** inclusa nella fabbrica di immagini.

Nel file di configurazione labs.json sono elencati due elementi chiave:There are two key things listed in the labs.json configuration file:

- Identificazione un lab di destinazione specifico tramite l'ID sottoscrizione e il nome del lab.
- Il set specifico di immagini che devono essere inviate al lab come percorsi relativi alla radice di configurazione. È possibile specificare l'intera cartella (per ottenere tutte le immagini in quella cartella) troppo.

Di seguito è riportato un file labs.json di esempio con due lab elencati. In questo caso, si distribuiscono immagini in due laboratori diversi.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Creare un'attività di compilazione
Usando gli stessi passaggi che hai visto in precedenza in questo articolo, aggiungere un'altra attività di compilazione di **Azure Powershell** alla definizione di compilazione. Inserire i dettagli come mostrato nell'immagine seguente: 

![Attività di compilazione per la distribuzione di immagini](./media/save-distribute-custom-images/second-build-task-powershell.png)

I parametri sono:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Questa attività accetta tutte le immagini personalizzate presenti nella factory di immagini e le inserisce in qualsiasi lab definito nel file Labs.json.

## <a name="queue-the-build"></a>Accodare la compilazione
Una volta completata l'attività di compilazione di distribuzione, accodare una nuova compilazione per assicurarsi che tutto funzioni. Al termine della compilazione, le nuove immagini personalizzate verranno visualizzate nel lab di destinazione immesso nel file di configurazione Labs.json.

## <a name="next-steps"></a>Passaggi successivi
Nel prossimo articolo della serie, aggiornare la factory di immagini con un criterio di conservazione e le istruzioni di pulizia: Impostare i criteri di conservazione ed eseguire gli script di [pulitura](image-factory-set-retention-policy-cleanup.md).
