---
title: Usare Azure DevTest Labs per macchine virtuali e ambienti di test PaaS | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per macchine virtuali scenari di ambienti di test PaaS.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a556cee9d7b665cd7df23c97e7e2c8c2afabbe58
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Usare Azure DevTest Labs per macchine virtuali e ambienti di test PaaS

Azure DevTest Labs può essere usato per implementare molti scenari chiave. Uno dei più importanti prevede l'uso di DevTest Labs per ospitare computer per i tester. 

In questo scenario DevTest Labs offre i vantaggi seguenti:

- I tester possono provare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux tramite modelli ed elementi riutilizzabili.
- Possono anche aumentare i test di carico eseguendo il provisioning di più agenti di test.
- Gli amministratori possono controllare i costi assicurandosi che:
  - I tester non possano ottenere più macchine virtuali del necessario per lo sviluppo.
  - Le VM vengano arrestate quando non sono in uso.

![Usare DevTest Labs per il training](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Questo articolo illustra le diverse funzionalità di Azure DevTest Labs usate per soddisfare i requisiti dei tester e i passaggi dettagliati da seguire per configurare un lab.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementazione di ambienti di test con Azure DevTest Labs
1. **Creare il lab** 
   
    I lab sono il punto di partenza in Azure DevTest Labs. Dopo avere creato un lab, è possibile eseguire attività quali aggiungere utenti, ovvero tester, al lab, impostare criteri per controllare i costi, definire le immagini della macchina virtuale per la creazione rapida e altro.  
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare un lab di sviluppo/test di Azure](devtest-lab-create-lab.md) |Informazioni su come creare un lab in Azure DevTest Labs nel portale di Azure. |
2. **Creare VM in pochi minuti usando immagini del marketplace predefinite e immagini personalizzate** 
   
    È possibile selezionare immagini predefinite tra le moltissime presenti in Azure Marketplace e renderle disponibili nel lab. Se le immagini predefinite non soddisfano i requisiti, è possibile creare un'immagine personalizzata creando una VM per il lab da un'immagine predefinita di Azure Marketplace, installando tutto il software necessario e salvando la VM come immagine personalizzata nel lab.

    Se si useranno immagini personalizzate, considerare la possibilità di usare una factory di immagini per creare e distribuire le immagini. Una factory di immagini è una soluzione di configurazione come codice che compila e distribuisce automaticamente le immagini configurate a intervalli regolari. In questo modo si risparmia il tempo necessario per configurare manualmente il sistema dopo la creazione di una VM con il sistema operativo di base.
  
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Configurare le impostazioni dell'immagine di Azure Marketplace in un lab](devtest-lab-configure-marketplace-images.md) |Informazioni su come aggiungere all'elenco elementi consentiti le immagini di Azure Marketplace, rendendo disponibili per la selezione solo quelle che si vuole usare per i tester.|
   | [Creare un'immagine personalizzata](devtest-lab-create-template.md) |Creare un'immagine personalizzata preinstallando il software necessario in modo che i tester possano creare rapidamente una macchina virtuale usando l'immagine personalizzata.|
   | [Learn about image factory](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) (Informazioni sulla factory di immagini) |Guardare un video che descrive come configurare e usare una factory di immagini.|

3. **Creare modelli riutilizzabili per i computer di test** 
   
    Una formula in Azure DevTest Labs è un elenco di valori predefiniti di proprietà usati per creare una VM. È possibile creare una formula nel lab selezionando un'immagine, una dimensione per la VM (una combinazione di CPU e RAM) e una rete virtuale. Ogni tester può visualizzare la formula nel lab e usarla per creare una macchina virtuale. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Gestire le formule dei lab di sviluppo/test per creare macchine virtuali](devtest-lab-manage-formulas.md) |Informazioni su come creare una formula selezionando un'immagine, una dimensione per la VM (combinazione di CPU e RAM) e una rete virtuale.|

3. **Creare ambienti di test per più macchine virtuali** 
   
    I modelli di Azure Resource Manager consentono di definire l'infrastruttura e la configurazione della soluzione di Azure e di distribuire ripetutamente più macchine virtuali di test in modo coerente.

    È possibile eseguire il provisioning delle risorse di Azure PaaS in un ambiente usando un modello di Azure Resource Manager che verrà visualizzato nella verifica dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse PaaS.

    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Informazioni su come distribuire più macchine virtuali in modo coerente nell'ambiente di test.|

4. **Creare elementi per consentire la personalizzazione delle VM flessibile**

   Gli elementi vengono usati per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Gli elementi possono essere:

   - Strumenti che si vuole installare nella VM, come agenti, Fiddler, Visual Studio.
   - Azioni che si desidera eseguire sulla macchina virtuale, ad esempio la clonazione di un archivio.
   - Applicazioni che si vuole testare.

   Molti elementi sono già immediatamente disponibili. È possibile creare elementi personalizzati per le proprie esigenze specifiche.

   Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare elementi personalizzati per le macchine virtuali di DevTest Labs](devtest-lab-artifact-author.md) |Creare elementi personalizzati per le macchine virtuali nel lab.|
   | [Aggiungere un repository Git per archiviare gli elementi personalizzati e i modelli di Azure Resource Manager per l'uso in Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Informazioni su come archiviare gli elementi personalizzati nel repository Git privato.|

5. **Controllare i costi**
   
    Azure DevTest Labs consente di impostare un criterio nel lab per specificare il numero massimo di macchine virtuali che possono essere create da un tester nel lab. 
   
    Se il team di test ha definito un piano di lavoro e si vuole arrestare tutte le macchine virtuali a una determinata ora del giorno e quindi riavviarle automaticamente il giorno seguente, è possibile impostare criteri di arresto automatico e di riavvio automatico nel lab. 
   
    Infine, al termine dello sviluppo dell'app, è possibile eliminare tutte le VM in una volta eseguendo un unico script di PowerShell. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Definire i criteri del lab](devtest-lab-set-lab-policy.md) |Controllare i costi impostando criteri nel lab. |
   | [Eliminare tutte le VM del lab usando uno script di PowerShell](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Eliminare tutti i lab con una sola operazione al termine del test.|

1. **Aggiungere una rete virtuale a un lab** 
   
    DevTest Labs crea una nuova rete virtuale quando viene creato un lab. Se si è configurata la propria rete virtuale, ad esempio usando ExpressRoute o la VPN da sito a sito, è possibile aggiungere questa rete virtuale alle impostazioni della rete virtuale del lab in modo che sia disponibile quando si creano le VM.

    È anche disponibile un elemento di aggiunta a un dominio di Azure Active Directory che aggiunge una macchina virtuale a un dominio quando la macchina virtuale viene creata. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md) |Informazioni su come configurare una rete virtuale in Azure DevTest Labs usando il portale di Azure.|

6. **Condividere il lab con ogni tester**
   
    I lab sono direttamente accessibili con un collegamento condiviso con i tester, che non devono nemmeno avere un account Azure, purché abbiano un [account Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). I tester non possono visualizzare le macchine virtuali create da altri tester.  
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Aggiungere un tester a un lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Usare il portale di Azure per aggiungere tester al lab.|
   | [Aggiungere tester al lab usando uno script di PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Usare PowerShell per automatizzare l'aggiunta di tester al lab. |
   | [Ottenere un collegamento al lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Informazioni su come i tester possono accedere direttamente a un lab tramite un collegamento ipertestuale.|

7. **Automatizzare la creazione del lab per più team** 
   
    È possibile automatizzare la creazione del lab, incluse le impostazioni personalizzate, creando un modello di Resource Manager e usandolo per creare altri lab identici. 
   
    Per altre informazioni, fare clic sui collegamenti nella tabella seguente:
   
   | Attività | Contenuto dell'esercitazione |
   | --- | --- |
   | [Creare un lab usando un modello di Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) |Creare lab in Azure DevTest Labs usando modelli di Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]


