---
title: Usare Azure DevTest Labs per il training | Documentazione Microsoft
description: Informazioni su come usare Azure DevTest Labs per gli scenari di training.
services: devtest-lab,virtual-machines
documentationcenter: na
author: steved0x
manager: douge
editor: 
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a85999b7963e9a07d3f91ec47f298f91439c0808
ms.lasthandoff: 04/15/2017


---
# <a name="use-azure-devtest-labs-for-training"></a>Usare Azure DevTest Labs per il training
Azure DevTest Labs può essere usato per implementare diversi scenari chiave oltre che per le attività di sviluppo/test. Uno di tali scenari prevede la configurazione di un lab per il training. Azure DevTest Labs consente di creare un lab in cui è possibile fornire modelli personalizzati che ogni partecipante può usare per creare ambienti identici e isolati per il training. È possibile applicare criteri per verificare che per ogni partecipante siano disponibili ambienti di training solo quando sono necessari e contengano un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. Infine, è facilmente possibile condividere il lab con i partecipanti, al quale possono accedere con un clic.

![Usare DevTest Labs per il training](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs soddisfa i requisiti seguenti, che sono necessari per eseguire il training in un ambiente virtuale: 

* I partecipanti non possono visualizzare le VM create da altri partecipanti
* Ogni computer di training deve essere identico
* I partecipanti possono effettuare rapidamente il provisioning degli ambienti di training
* Controllare il costo assicurandosi che i partecipanti non possano ottenere più VM di quelle necessarie per il training né arrestare le VM quando non le usano
* Condividere facilmente il lab di training con ogni partecipante
* Usare più volte il lab di training

Questo articolo illustra le diverse funzionalità di Azure DevTest Labs che possono essere usate per soddisfare i requisiti di training descritti prima e i passaggi dettagliati che è possibile seguire per configurare un lab per il training.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementazione del training con Azure DevTest Labs
1. **Creare il lab** 
   
    I lab sono il punto di partenza in Azure DevTest Labs. Dopo avere creato un lab, è possibile eseguire attività come aggiungere utenti (partecipanti) al lab, impostare criteri per controllare i costi, definire immagini di VM che possano per la creazione rapida e altro.   
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare un lab di sviluppo/test di Azure](devtest-lab-create-lab.md) |Informazioni su come creare un lab in Azure DevTest Labs nel portale di Azure. |
2. **Creare VM di training in pochi minuti usando immagini del marketplace predefinite e immagini personalizzate** 
   
    È possibile selezionare immagini predefinite tra le moltissime presenti in Azure Marketplace e renderle disponibili per i partecipanti nel lab. Se le immagini predefinite non soddisfano i requisiti, è possibile creare un'immagine personalizzata creando una VM per il lab da un'immagine predefinita di Azure Marketplace, installando tutto il software necessario per il training e salvando la VM come immagine personalizzata nel lab. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Configurare le impostazioni dell'immagine di Azure Marketplace in un lab](devtest-lab-configure-marketplace-images.md) |Informazioni su come aggiungere all'elenco elementi consentiti le immagini di Azure Marketplace, rendendo disponibili per la selezione solo quelle che si vuole usare per il training. |
   | [Creare un'immagine personalizzata](devtest-lab-create-template.md) |Creare un'immagine personalizzata preinstallando il software necessario per il training in modo che i partecipanti possano creare rapidamente una VM usando l'immagine personalizzata. |
3. **Creare modelli riutilizzabili per i computer di training** 
   
    Una formula in Azure DevTest Labs è un elenco di valori predefiniti di proprietà usati per creare una VM. È possibile creare una formula nel lab selezionando un'immagine, una dimensione per la VM (una combinazione di CPU e RAM) e una rete virtuale. Ogni partecipante può visualizzare la formula nel lab e usarla per creare una VM. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Gestire le formule dei lab di sviluppo/test per creare macchine virtuali](devtest-lab-manage-formulas.md) |Informazioni su come creare una formula selezionando un'immagine, una dimensione per la VM (combinazione di CPU e RAM) e una rete virtuale. |
4. **Controllare i costi**
   
    Azure DevTest Labs consente di impostare un criterio nel lab per specificare il numero massimo di VM che possono essere create da un partecipante nel lab. 
   
    Se si deve effettuare un training di più giorni e si vuole arrestare tutte le VM a una determinata ora del giorno e quindi riavviarle automaticamente il giorno seguente, è possibile impostare criteri di arresto automatico e di riavvio automatico nel lab. 
   
    Infine, al termine del training, è possibile eliminare tutte le VM in una volta eseguendo un unico script di PowerShell. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Definire i criteri del lab](devtest-lab-set-lab-policy.md) |Controllare i costi impostando criteri nel lab. |
   | [Eliminare tutte le VM del lab usando uno script di PowerShell](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Eliminare tutti i lab con una sola operazione al termine del training. |
5. **Condividere il lab con ogni partecipante**
   
    I lab sono direttamente accessibili con un collegamento condiviso con i partecipanti. I partecipanti non devono nemmeno avere un account Azure, purché abbiano un [account Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). I partecipanti non possono visualizzare le VM create da altri partecipanti.  
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Aggiungere un partecipante a un lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Usare il portale di Azure per aggiungere partecipanti al lab di training. |
   | [Aggiungere partecipanti al lab usando uno script di PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Usare PowerShell per automatizzare l'aggiunta di partecipanti al lab di training. |
   | [Ottenere un collegamento al lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Informazioni su come un lab sia direttamente accessibile tramite un collegamento ipertestuale. |
6. **Usare più volte il lab** 
   
    È possibile automatizzare la creazione del lab, incluse le impostazioni personalizzate, creando un modello di Resource Manager e usandolo per creare altri lab identici. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare un lab usando un modello di Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) |Creare lab in Azure DevTest Labs usando modelli di Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]


