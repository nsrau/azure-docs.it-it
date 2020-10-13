---
title: Salvare e distribuire immagini in Azure DevTest Labs | Microsoft Docs
description: Questo articolo illustra la procedura per salvare immagini personalizzate dalle macchine virtuali (VM) già create in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476241"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Salvare immagini personalizzate e distribuirle in più lab
Questo articolo illustra la procedura per salvare immagini personalizzate dalle macchine virtuali (VM) già create. Viene inoltre illustrato come distribuire queste immagini personalizzate in altri laboratori di DevTest nell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti
Devono essere già disponibili gli elementi seguenti:

- Un Lab per la factory di immagini in Azure DevTest Labs.
- Un progetto DevOps di Azure usato per automatizzare la factory di immagini.
- Percorso del codice sorgente contenente gli script e la configurazione (in questo esempio, nello stesso progetto DevOps indicato nel passaggio precedente).
- Definizione di compilazione per orchestrare le attività di Azure PowerShell.

Se necessario, seguire la procedura descritta in [eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md) per creare o configurare questi elementi. 

## <a name="save-vms-as-generalized-vhds"></a>Salvare le macchine virtuali come dischi rigidi virtuali generalizzati
Salvare le macchine virtuali esistenti come dischi rigidi virtuali generalizzati.  È disponibile uno script di PowerShell di esempio per salvare le macchine virtuali esistenti come dischi rigidi virtuali generalizzati. Per usarlo, aggiungere prima di tutto un'altra attività di **Azure PowerShell** alla definizione di compilazione, come illustrato nella figura seguente:

![Aggiungi Azure PowerShell passaggio](./media/save-distribute-custom-images/powershell-step.png)

Dopo aver creato la nuova attività nell'elenco, selezionare l'elemento in modo da poter compilare tutti i dettagli, come illustrato nell'immagine seguente: 

![Impostazioni di PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Confronto tra immagini personalizzate generalizzate e specializzate
Nel [portale di Azure](https://portal.azure.com), quando si crea un'immagine personalizzata da una macchina virtuale, è possibile scegliere di disporre di un'immagine personalizzata generalizzata o specializzata.

- **Immagine personalizzata specializzata:** Sysprep/deprovision non è stato eseguito nel computer. Significa che l'immagine è una copia esatta del disco del sistema operativo nella macchina virtuale esistente (snapshot).  Gli stessi file, applicazioni, account utente, nome computer e così via sono tutti presenti quando si crea un nuovo computer da questa immagine personalizzata.
- **Immagine personalizzata generalizzata:** Sysprep/deprovision è stato eseguito nel computer.  Quando si esegue questo processo, vengono rimossi gli account utente, viene rimosso il nome del computer, vengono eliminati gli hive del registro di sistema dell'utente e così via, con l'obiettivo di generalizzare l'immagine in modo che possa essere personalizzata quando si crea un'altra macchina virtuale.  Quando si generalizza una macchina virtuale (eseguendo Sysprep), il processo Elimina la macchina virtuale corrente. non sarà più funzionante.

Lo script per l'allineamento delle immagini personalizzate nella Factory di immagini salverà i dischi rigidi virtuali per le macchine virtuali create nel passaggio precedente (identificato in base a un tag nella risorsa in Azure).

## <a name="update-configuration-for-distributing-images"></a>Aggiornare la configurazione per la distribuzione di immagini
Il passaggio successivo del processo consiste nel eseguire il push delle immagini personalizzate dal Lab della factory di immagini a tutti gli altri Lab che li richiedono. La parte principale di questo processo è la **labs.jsnel** file di configurazione. È possibile trovare questo file nella cartella di **configurazione** inclusa nella Factory di immagini.

Nel file di configurazione labs.jsnel file di configurazione sono presenti due elementi chiave:

- Identificazione univoca di un Lab di destinazione specifico usando l'ID sottoscrizione e il nome del Lab.
- Set specifico di immagini da inserire nel Lab come percorsi relativi alla radice di configurazione. È possibile specificare anche l'intera cartella (per ottenere tutte le immagini in tale cartella).

Di seguito è riportato un esempio labs.jsin un file con due Lab elencati. In questo caso, si distribuiscono immagini in due diversi Lab.

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
Seguendo la stessa procedura illustrata in precedenza in questo articolo, aggiungere un'altra attività di compilazione di **Azure PowerShell** alla definizione di compilazione. Inserire i dettagli come illustrato nell'immagine seguente: 

![Attività di compilazione per la distribuzione di immagini](./media/save-distribute-custom-images/second-build-task-powershell.png)

I parametri sono: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Questa attività acquisisce tutte le immagini personalizzate presenti nella Factory di immagini e le inserisce in tutti i Lab definiti nel Labs.jssu file.

## <a name="queue-the-build"></a>Accoda la compilazione
Una volta completata l'attività di compilazione della distribuzione, accodare una nuova compilazione per assicurarsi che tutto funzioni correttamente. Una volta completata la compilazione, le nuove immagini personalizzate vengono visualizzate nel Lab di destinazione che è stato immesso nel file di configurazione Labs.js.

## <a name="next-steps"></a>Passaggi successivi
Nel prossimo articolo della serie si aggiorna la factory di immagini con i criteri di conservazione e i passaggi di pulizia: [impostare i criteri di conservazione ed eseguire gli script di pulizia](image-factory-set-retention-policy-cleanup.md).
