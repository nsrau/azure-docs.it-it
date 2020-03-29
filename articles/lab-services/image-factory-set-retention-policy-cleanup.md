---
title: Configurare i criteri di conservazione in Azure DevTest Labs - Documenti Microsoft
description: Informazioni su come configurare criteri di conservazione, pulire la factory e ritirare le vecchie immagini da DevTest Labs.
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759415"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Configurare i criteri di conservazione in Azure DevTest LabsSet up retention policy in Azure DevTest Labs
In questo articolo viene illustrata l'impostazione di criteri di conservazione, la pulizia della factory e il ritiro delle vecchie immagini da tutti gli altri laboratori DevTest dell'organizzazione. 

## <a name="prerequisites"></a>Prerequisiti
Assicurati di aver seguito questi articoli prima di procedere ulteriormente:

- [Creare una factory di immagini](image-factory-create.md)
- [Eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md)
- [Salvare immagini personalizzate e distribuirle in più laboratori](image-factory-save-distribute-custom-images.md)

I seguenti articoli dovrebbero già essere in atto:

- Un lab per la factory di immagini in Azure DevTest Labs
- Uno o più laboratori di Azure DevTest di destinazione in cui la factory distribuirà immagini dorate
- Progetto DevOps di Azure usato per automatizzare la factory di immagini.
- Percorso del codice sorgente contenente gli script e la configurazione (nel nostro esempio, nello stesso progetto DevOps usato in precedenza)Source code location containing the scripts and configuration (in our example, in the same DevOps Project used above)
- Definizione di compilazione per orchestrare le attività di Azure PowershellA build definition to orchestrate the Azure Powershell tasks
 
## <a name="setting-the-retention-policy"></a>Impostazione dei criteri di conservazione
Prima di configurare i passaggi di pulizia, definire il numero di immagini cronologiche che si desidera conservare nei laboratori DevTest. Quando è stato seguito l'articolo Eseguire una factory di [immagini da Azure DevOps,](image-factory-set-up-devops-lab.md) sono state configurate diverse variabili di compilazione. Uno di loro era **ImageRetention**. Impostare questa `1`variabile su , il che significa che i laboratori DevTest non manterrà una cronologia delle immagini personalizzate. Saranno disponibili solo le ultime immagini distribuite. Se si modifica `2`questa variabile in , verrà mantenuta l'ultima immagine distribuita più quelle precedenti. You can set this value to define the number of historic images you wish to maintain in your DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Pulizia della fabbrica
Il primo passaggio nella pulizia della fabbrica consiste nel rimuovere le macchine virtuali immagine dorate dalla factory di immagini. C'è uno script per fare questa attività proprio come i nostri script precedenti. Il primo passaggio consiste nell'aggiungere un'altra attività di Azure Powershell alla definizione di compilazione, come illustrato nell'immagine seguente:The first step is to add another **Azure Powershell** task to the build definition as shown in the following image:

![Passaggio di PowerShellPowerShell step](./media/set-retention-policy-cleanup/powershell-step.png)

Una volta che hai la nuova attività nell'elenco, seleziona l'elemento e inserisci tutti i dettagli come mostrato nell'immagine seguente:

![Attività di PowerShell Pulisci immagini precedenti](./media/set-retention-policy-cleanup/configure-powershell-task.png)

I parametri dello `-DevTestLabName $(devTestLabName)`script sono: .

## <a name="retire-old-images"></a>Ritirare le vecchie immagini 
Questa attività rimuove tutte le immagini precedenti, mantenendo solo una cronologia corrispondente alla variabile di compilazione **ImageRetention.This** task removes any old images, keeping only a history matching the ImageRetention build variable. Aggiungere un'attività di compilazione di **Azure Powershell aggiuntiva** alla definizione di compilazione. Una volta aggiunta, seleziona l'attività e inserisci i dettagli come mostrato nell'immagine seguente: 

![Attività di PowerShell Ritira immagini precedenti](./media/set-retention-policy-cleanup/retire-old-image-task.png)

I parametri dello script sono:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Accodare la compilazione
Dopo aver completato la definizione di compilazione, accodare una nuova compilazione per assicurarsi che tutto funzioni. Al termine della compilazione, le nuove immagini personalizzate vengono visualizzate nel lab di destinazione e, se si controlla il lab della factory di immagini, non viene visualizzata alcuna macchine virtuali di cui è stato eseguito il provisioning. Inoltre, se si accodano ulteriori compilazioni, le attività di pulizia ritirano le vecchie immagini personalizzate dai laboratori DevTest in base al valore di conservazione impostato nelle variabili di compilazione.

> [!NOTE]
> Se la pipeline di compilazione è stata eseguita alla fine dell'ultimo articolo della serie, eliminare manualmente le macchine virtuali create nel lab della factory di immagini prima di accodare una nuova compilazione.  La procedura di pulizia manuale è necessaria solo quando abbiamo impostato tutto e verificare che funzioni.



## <a name="summary"></a>Riepilogo
A questo punto è possibile utilizzare una factory di immagini in esecuzione in grado di generare e distribuire immagini personalizzate nei laboratori su richiesta. A questo punto, si tratta solo di impostare correttamente le immagini e identificare i lab di destinazione. Come accennato nell'articolo precedente, il file **Labs.json** che si trova nella cartella **Configuration** specifica quali immagini devono essere rese disponibili in ognuno dei lab di destinazione. Quando si aggiungono altri laboratori DevTest all'organizzazione, è sufficiente aggiungere una voce nel file Labs.json per il nuovo lab.

Anche l'aggiunta di una nuova immagine alla tua fabbrica è semplice. Quando si vuole includere una nuova immagine nella factory, aprire il [portale di Azure,](https://portal.azure.com)passare alla factory DevTest Labs, selezionare il pulsante per aggiungere una macchina virtuale e scegliere l'immagine del marketplace e gli elementi desiderati. Invece di selezionare il pulsante **Crea** per creare la nuova macchina virtuale, selezionare Visualizza modello di **Azure Resource Manager**" e salvare il modello come file con estensione json in un punto qualsiasi all'interno della cartella **GoldenImages** nel repository. Alla successiva esecuzione della factory di immagini, verrà creata l'immagine personalizzata.


## <a name="next-steps"></a>Passaggi successivi
1. [Pianificare la compilazione/rilascio](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) per eseguire periodicamente la factory di immagini. Rinfresca regolarmente le immagini generate in fabbrica.
2. Crea altre immagini d'oro per la tua fabbrica. È anche possibile creare [elementi](devtest-lab-artifact-author.md) per creare script di parti aggiuntive delle attività di configurazione della macchina virtuale e includere gli elementi nelle immagini di factory.
4. Creare una build/release separata per eseguire separatamente lo script **DistributeImages.Create** a separate [build/release](/azure/devops/pipelines/overview?view=azure-devops-2019) to run the DistributeImages script separately. È possibile eseguire questo script quando si apportano modifiche a Labs.json e si ottengono le immagini copiate nei lab di destinazione senza dover ricreare nuovamente tutte le immagini.

