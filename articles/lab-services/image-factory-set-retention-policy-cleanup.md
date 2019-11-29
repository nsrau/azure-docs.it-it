---
title: Configurare i criteri di conservazione in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare un criterio di conservazione, pulire la factory e ritirare le immagini obsolete da DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1c18fc799014ad862c93076d695f2516c6363d
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560161"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creare una factory di immagini personalizzata in Azure DevTest Labs
In questo articolo viene illustrata l'impostazione di un criterio di conservazione, la pulizia della Factory e il ritiro di immagini obsolete da tutti gli altri laboratori DevTest nell'organizzazione. 

## <a name="prerequisites"></a>Prerequisiti
Prima di procedere, assicurarsi di aver seguito questi articoli:

- [Creare una factory di immagini](image-factory-create.md)
- [Eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md)
- [Salva immagini personalizzate e Distribuisci in più Lab](image-factory-save-distribute-custom-images.md)

Devono essere già disponibili gli elementi seguenti:

- Un Lab per la factory di immagini in Azure DevTest Labs
- Uno o più Azure DevTest Labs di destinazione in cui la Factory distribuirà le immagini dorate
- Un progetto DevOps di Azure usato per automatizzare la factory di immagini.
- Percorso del codice sorgente contenente gli script e la configurazione (in questo esempio, nello stesso progetto DevOps usato in precedenza)
- Definizione di compilazione per orchestrare le attività di Azure PowerShell
 
## <a name="setting-the-retention-policy"></a>Impostazione dei criteri di conservazione
Prima di configurare i passaggi di pulizia, definire il numero di immagini cronologiche che si desidera mantenere in DevTest Labs. Quando è stata seguita l'articolo [eseguire un'immagine Factory da Azure DevOps](image-factory-set-up-devops-lab.md) , sono state configurate varie variabili di compilazione. Uno di essi è **ImageRetention**. Questa variabile viene impostata su `1`, il che significa che DevTest Labs non manterrà una cronologia delle immagini personalizzate. Saranno disponibili solo le immagini distribuite più recenti. Se si modifica questa variabile in `2`, verranno mantenute le immagini distribuite più recenti e quelle precedenti. È possibile impostare questo valore per definire il numero di immagini cronologiche che si vuole gestire in DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Pulizia della Factory
Il primo passaggio per pulire la Factory consiste nel rimuovere le VM di immagini dorate dalla factory di immagini. È disponibile uno script per eseguire questa attività esattamente come gli script precedenti. Il primo passaggio consiste nell'aggiungere un'altra attività di **Azure PowerShell** alla definizione di compilazione, come illustrato nella figura seguente:

![Passaggio di PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Dopo aver creato la nuova attività nell'elenco, selezionare l'elemento e compilare tutti i dettagli, come illustrato nell'immagine seguente:

![Pulisci le immagini obsolete attività PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

I parametri dello script sono: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Ritira immagini obsolete 
Questa attività rimuove le immagini obsolete, mantenendo solo una cronologia corrispondente alla variabile di compilazione **ImageRetention** . Aggiungere un'altra attività di compilazione di **Azure PowerShell** alla definizione di compilazione. Una volta aggiunto, selezionare l'attività e specificare i dettagli come illustrato nell'immagine seguente: 

![Ritirare le immagini obsolete attività PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

I parametri dello script sono: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Accoda la compilazione
Ora che è stata completata la definizione di compilazione, accodare una nuova compilazione per assicurarsi che tutto funzioni correttamente. Al termine della compilazione, le nuove immagini personalizzate vengono visualizzate nel Lab di destinazione e, se si seleziona il Lab della factory di immagini, non viene visualizzata alcuna VM di cui è stato effettuato il provisioning. Inoltre, se si accodano altre compilazioni, le attività di pulizia ritireranno le immagini personalizzate obsolete da DevTest Labs in base al valore di conservazione impostato nelle variabili di compilazione.

> [!NOTE]
> Se la pipeline di compilazione è stata eseguita alla fine dell'ultimo articolo della serie, eliminare manualmente le macchine virtuali create nel Lab della factory di immagini prima di accodare una nuova compilazione.  Il passaggio di pulizia manuale è necessario solo quando si configurano tutti gli elementi e verificarne il funzionamento.



## <a name="summary"></a>Summary
A questo punto è disponibile una factory di immagini in esecuzione che può generare e distribuire immagini personalizzate nei Lab su richiesta. A questo punto, è sufficiente configurare correttamente le immagini e identificare i Lab di destinazione. Come indicato nell'articolo precedente, il file **Labs. JSON** che si trova nella cartella di **configurazione** specifica le immagini che devono essere rese disponibili in ogni Lab di destinazione. Quando si aggiungono altri DevTest Labs alla propria organizzazione, è sufficiente aggiungere una voce nel file Labs. JSON per il nuovo Lab.

Anche l'aggiunta di una nuova immagine alla Factory è semplice. Per includere una nuova immagine nella Factory, aprire il [portale di Azure](https://portal.azure.com), passare a Factory DevTest Labs, selezionare il pulsante per aggiungere una VM e scegliere l'immagine e gli artefatti del Marketplace desiderati. Anziché selezionare il pulsante **Crea per creare** la nuova macchina virtuale, selezionare **Visualizza Azure Resource Manager modello**"e salvare il modello come file con estensione JSON in un punto qualsiasi all'interno della cartella **GoldenImages** nel repository. Alla successiva esecuzione della factory di immagini verrà creata l'immagine personalizzata.


## <a name="next-steps"></a>Passaggi successivi
1. [Pianificare la compilazione o la versione](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) per eseguire periodicamente la factory di immagini. Aggiorna regolarmente le immagini generate in base alle impostazioni predefinite.
2. Crea più immagini d'oro per la tua Factory. È anche possibile [creare elementi per creare](devtest-lab-artifact-author.md) script per altre parti delle attività di configurazione della macchina virtuale e includere gli artefatti nelle immagini Factory.
4. Creare una [compilazione o una versione separata](/azure/devops/pipelines/overview?view=azure-devops-2019) per eseguire lo script **DistributeImages** separatamente. È possibile eseguire questo script quando si apportano modifiche a Labs. JSON e si ottengono immagini copiate nei Lab di destinazione senza dover ricreare tutte le immagini.

