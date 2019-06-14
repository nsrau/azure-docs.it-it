---
title: Salvare e distribuire le immagini in Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622639"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Salvare immagini personalizzate e distribuirle in più lab
Questo articolo illustra ti offre la procedura per salvare le immagini personalizzate dalle macchine virtuali (VM) già create. Viene anche illustrato come distribuire queste immagini personalizzate per altri DevTest Labs nell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti
Gli elementi seguenti devono essere già disponibili:

- Un ambiente lab per la Factory di immagini in Azure DevTest Labs.
- Un progetto DevOps di Azure che consente di automatizzare la factory di immagini.
- Percorso del codice sorgente che contiene gli script e configurazione (in questo esempio, nello stesso progetto DevOps citato nel passaggio precedente).
- Definizione per orchestrare le attività di Azure Powershell di compilazione.

Se necessario, seguire i passaggi nel [eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md) per creare o configurare questi elementi. 

## <a name="save-vms-as-generalized-vhds"></a>Salvare le macchine virtuali in dischi rigidi virtuali generalizzati
Salvare le macchine virtuali esistenti in dischi rigidi virtuali generalizzati.  È uno script di PowerShell di esempio per salvare le macchine virtuali esistenti in dischi rigidi virtuali generalizzati. Per utilizzarlo, in primo luogo, aggiungere un'altra **Azure Powershell** attività alla definizione di compilazione come illustrato nell'immagine seguente:

![Aggiungi istruzione di Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Dopo aver creato la nuova attività nell'elenco, selezionare l'elemento consentire la compilazione in tutti i dettagli come mostrato nell'immagine seguente: 

![Impostazioni di PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizzate e specializzate immagini personalizzate
Nel [portale di Azure](https://portal.azure.com), quando si crea un'immagine personalizzata da una macchina virtuale, è possibile scegliere di avere un generalizzata o un'immagine specializzata personalizzata.

- **Immagine specializzata personalizzata:** Sysprep o sottoposta a deprovisioning non è stato eseguito nel computer. Significa che l'immagine è una copia esatta del disco del sistema operativo nella macchina virtuale esistente (uno snapshot).  File stesso, applicazioni, gli account utente, nome del computer e così via, sono tutti presenti quando si crea una nuova macchina da questa immagine personalizzata.
- **Immagine generalizzata personalizzata:** Nel computer è stato eseguito Sysprep o sottoposta a deprovisioning.  Quando si esegue questo processo, rimuove gli account utente, rimuove il nome del computer, estrae l'hive del Registro di sistema dell'utente e così via, con l'obiettivo di generalizzare l'immagine in modo che può essere personalizzato durante la creazione di un'altra macchina virtuale.  Quando si generalizzare una macchina virtuale (eseguendo sysprep), il processo elimina la macchina virtuale corrente, non potrà più essere funzionali.

Lo script per bloccare le immagini personalizzate nell'ambiente di produzione immagine salverà i dischi rigidi virtuali per le macchine virtuali create nel passaggio precedente (identificato basato su un tag sulla risorsa in Azure).

## <a name="update-configuration-for-distributing-images"></a>Aggiornare la configurazione per la distribuzione delle immagini
Il passaggio successivo del processo è eseguire il push di immagini personalizzate dal lab factory immagine orizzontale a eventuali altri ambienti di testing che ne hanno bisogno. La parte principale di questo processo è il **labs.json** file di configurazione. È possibile trovare questo file nei **configurazione** cartella inclusa nella factory di immagini.

Esistono due aspetti chiave elencati nel file di configurazione labs.json:

- Che identifica in modo univoco un ambiente di destinazione specifico utilizzando l'ID sottoscrizione e il nome del lab.
- Il set di immagini che deve essere inserita nel lab come percorsi relativi per la radice di configurazione specifico. È possibile specificare l'intera cartella (per ottenere tutte le immagini in tale cartella) troppo.

Qui è un file labs.json di esempio con due laboratori elencati. In questo caso, si prevede di distribuire immagini ai due lab diverso.

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
Usando gli stessi passaggi si è visto in precedenza in questo articolo, aggiungere un ulteriore **Azure Powershell** attività di compilazione è della definizione di compilazione. Specificare i dettagli come mostrato nell'immagine seguente: 

![Attività per distribuire le immagini di compilazione](./media/save-distribute-custom-images/second-build-task-powershell.png)

I parametri sono: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Questa attività accetta le immagini personalizzate presenti nell'ambiente di produzione di immagine e li inserisce in qualsiasi lab definito nel file Labs.json.

## <a name="queue-the-build"></a>Accodare la compilazione
Una volta completata l'attività di compilazione di distribuzione, accodare una nuova compilazione per assicurarsi che tutto funzioni. Dopo la compilazione viene completata correttamente, le nuove immagini personalizzate verranno visualizzato nell'ambiente di laboratorio di destinazione che è stato immesso nel file di configurazione Labs.json.

## <a name="next-steps"></a>Passaggi successivi
Nel prossimo articolo della serie, si aggiorna la factory di immagini con una procedura di criteri e la pulizia di conservazione: [Impostare criteri di conservazione ed eseguire gli script di pulizia](image-factory-set-retention-policy-cleanup.md).
