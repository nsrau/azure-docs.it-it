---
title: Creare una factory di immagini in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare una factory di immagini personalizzate in Azure DevTest Labs.
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
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490740"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creare una factory di immagini personalizzate in Azure DevTest Labs
Questo articolo illustra l'impostazione di criteri di conservazione, pulizia della factory e ritiro immagini precedenti da tutte le altre DevTest Labs nell'organizzazione. 

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi che siano stati seguiti questi articoli prima di procedere:

- [Creare una factory di immagini](image-factory-create.md)
- [Eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md)
- [Salvare immagini personalizzate e distribuirle in più lab](image-factory-save-distribute-custom-images.md)

Gli elementi seguenti devono essere già disponibili:

- Un ambiente lab per la factory di immagini in Azure DevTest Labs
- Almeno uno di destinazione in cui la factory ha deciso di distribuire immagini golden Azure DevTest Labs
- Un progetto DevOps di Azure consentono di automatizzare la factory di immagini.
- Percorso del codice sorgente che contiene gli script e configurazione (in questo esempio, nello stesso progetto DevOps usato in precedenza)
- Una definizione di compilazione per orchestrare le attività di Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Impostazione dei criteri di conservazione
Prima di configurare i passaggi di pulizia, definire immagini cronologiche quanti si desidera mantenere in DevTest Labs. Quando è stata seguita la [eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md) articolo, è stato configurato diverse variabili di compilazione. Uno di essi è stata **ImageRetention**. Si imposta questa variabile su `1`, il che significa che DevTest Labs non manterrà una cronologia delle immagini personalizzate. Solo le immagini distribuite più recente saranno disponibili. Se si modifica questa variabile su `2`, l'ultima versione distribuita immagine oltre a quelli precedenti verranno mantenute. È possibile impostare questo valore per definire il numero di immagini cronologiche da conservare in DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Pulizia della factory
Il primo passaggio nella pulizia della factory consiste nel rimuovere le immagini di macchine virtuali di golden dalla factory di immagini. È richiesto uno script per eseguire questa operazione come negli articoli precedenti. Il primo passaggio consiste nell'aggiungere un'altra **Azure Powershell** attività alla definizione di compilazione come illustrato nell'immagine seguente:

![Passaggio di PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Dopo aver creato la nuova attività nell'elenco, selezionare l'elemento e specificare tutti i dettagli come mostrato nell'immagine seguente:

![Pulire i vecchi attività di PowerShell di immagini](./media/set-retention-policy-cleanup/configure-powershell-task.png)

I parametri script sono: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Ritirare le immagini precedente 
Questa attività consente di rimuovere tutte le immagini precedenti, mantenendo solo una cronologia corrispondenti di **ImageRetention** variabile di compilazione. Aggiungere un ulteriore **Azure Powershell** la definizione di compilazione dell'attività di compilazione. Dopo averlo aggiunto, selezionare l'attività e specificare i dettagli come mostrato nell'immagine seguente: 

![Ritirare precedente attività di PowerShell di immagini](./media/set-retention-policy-cleanup/retire-old-image-task.png)

I parametri script sono: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Accodare la compilazione
Ora che è stata completata la definizione di compilazione, accodare una nuova compilazione per assicurarsi che tutto funzioni. Dopo la compilazione viene completata correttamente le nuove immagini personalizzate appaiono nell'ambiente di laboratorio di destinazione e, se si seleziona il lab di factory di immagini, non le macchine virtuali sottoposte a provisioning. Inoltre, se accumulano ulteriori compilazioni, viene visualizzata l'attività di pulizia ritiro precedente immagini personalizzate di DevTest Labs in base al valore di conservazione impostato nelle variabili di compilazione.

> [!NOTE]
> Se è stato eseguito il pipeline di compilazione alla fine dell'ultimo articolo della serie, è necessario eliminare manualmente le macchine virtuali che sono state create nel lab factory immagine prima di Accodamento di una nuova compilazione.  Mentre si configurare tutto e verificarne che il funzionamento, è necessario solo il passaggio di pulizia manuale.



## <a name="summary"></a>Summary
Ora si dispone di una factory di immagini in esecuzione che potrebbe generare e distribuire immagini personalizzate per i laboratori su richiesta. A questo punto, è sufficiente ottenere le immagini impostato in modo corretto e che identifica i laboratori di destinazione. Come accennato nell'articolo precedente, il **Labs.json** file che si trova nelle **configurazione** cartella specifica le immagini che devono essere rese disponibili in ognuno dei laboratori di destinazione. Quando si aggiungono altri DevTest Labs per l'organizzazione, è sufficiente aggiungere una voce nella finestra di Labs.json per il nuovo lab.

È anche semplice aggiunta di una nuova immagine alla factory. Quando si desidera includere una nuova immagine nell'ambiente di produzione si apre la [portale di Azure](https://portal.azure.com), passare alla factory DevTest Labs, selezionare il pulsante per aggiungere una macchina virtuale e scegliere l'immagine del marketplace desiderato e gli elementi. Invece di selezionare il **Create** pulsante per rendere la nuova macchina virtuale, selezionare **modello View Azure Resource Manager**"e salvare il modello come file con estensione JSON in un punto qualsiasi all'interno di **GoldenImages** cartella nel repository. Alla successiva esecuzione di factory di immagini, verrà creato l'immagine personalizzata.


## <a name="next-steps"></a>Passaggi successivi
1. [Pianificare la compilazione/versione](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) consentono di eseguire periodicamente la factory di immagini. Aggiorna le immagini factory generati a intervalli regolari.
2. Rendere più immagini golden della factory. È anche possibile considerare [creazione di elementi](devtest-lab-artifact-author.md) parti aggiuntive delle attività di configurazione della macchina virtuale di script e includere gli elementi nelle immagini di factory.
4. Creare un [separare compilazione/versione](/azure/devops/pipelines/overview?view=azure-devops-2019) per eseguire il **DistributeImages** script separatamente. Quando si apportano modifiche a Labs.json e ottenere immagini copiate ai laboratori di destinazione senza dover ricreare tutte le immagini anche in questo caso, è possibile eseguire questo script.

