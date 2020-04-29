---
title: Guida alla configurazione dell'account Lab accelerato per Azure Lab Services
description: Questa guida consente agli amministratori di configurare rapidamente un account Lab da usare all'interno della scuola.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879582"
---
# <a name="lab-account-setup-guide"></a>Guida all'installazione dell'account Lab

Come primo passaggio, gli amministratori devono configurare un account Lab nella sottoscrizione di Azure. Un account Lab è un contenitore per i Lab della classe e richiede solo pochi minuti per la configurazione.

## <a name="understand-your-schools-lab-account-requirements"></a>Informazioni sui requisiti dell'account Lab della scuola

Per informazioni su come configurare l'account Lab in base alle esigenze della scuola, è necessario prendere in considerazione queste domande.

### <a name="do-i-have-access-to-an-azure-subscription"></a>È possibile accedere a una sottoscrizione di Azure?

Per creare un account Lab, è necessario accedere a una sottoscrizione di Azure configurata per la propria scuola. La scuola potrebbe avere una o più sottoscrizioni. Si usa una sottoscrizione per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure, inclusi gli account Lab.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Quanti account Lab è necessario creare?

Per iniziare rapidamente, creare un singolo account Lab e successivamente creare account Lab aggiuntivi in base alle esigenze. Ad esempio, è possibile che si disponga di un account Lab per reparto.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Chi deve essere proprietari e collaboratori dell'account Lab?

Gli amministratori sono in genere proprietari e collaboratori di un account Lab. Sono responsabili della gestione dei criteri che si applicano a tutti i Lab contenuti nell'account Lab. La persona che crea l'account Lab è automaticamente un proprietario. È possibile aggiungere altri proprietari e collaboratori, in genere dal tenant Azure Active Directory (Azure AD) associato alla sottoscrizione. Questo può essere utile per gestire un account Lab assegnando il ruolo di proprietario o collaboratore a livello di account Lab.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Chi sarà autorizzato a creare e gestire i Lab?

È possibile scegliere di creare e gestire i Lab per gli amministratori e i membri docenti. Questi utenti (in genere dal tenant Azure AD associato alla sottoscrizione) vengono assegnati al ruolo Lab Creator nell'account Lab.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Si desidera concedere ai creatori di Lab la possibilità di salvare immagini che possono essere condivise tra più laboratori?

Una raccolta di immagini condivise è un repository che è possibile usare per salvare e condividere le immagini. Se sono presenti diverse classi che necessitano delle stesse immagini, i creatori di Lab possono creare l'immagine una volta e condividerla tra più laboratori. Tuttavia, per iniziare, non è necessaria necessariamente una raccolta di immagini condivise, perché è sempre possibile aggiungerne una in un secondo momento.

Se la risposta è "Sì" a questa domanda, è necessario creare o alleghiare una raccolta di immagini condivise all'account Lab. Se hai risposto "non so", puoi rimandare questa decisione fino a un secondo momento.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Quali immagini di Azure Marketplace useranno i laboratori in aula?

Azure Marketplace offre centinaia di immagini che è possibile abilitare per consentire ai creatori di Lab di usare l'immagine per creare il proprio Lab. Alcune immagini possono includere tutti gli elementi necessari per un Lab. In altri casi, è possibile utilizzare un'immagine come punto di partenza, quindi l'autore del Lab può personalizzarlo installando applicazioni o strumenti aggiuntivi.

Se non si conoscono le immagini che è necessario usare, sarà sempre possibile tornare a questa pagina in un secondo momento per abilitarle. Inoltre, il modo migliore per vedere quali immagini sono disponibili è creare innanzitutto un account Lab. Questo consente di accedere, in modo che sia possibile esaminare l'elenco delle immagini disponibili e il relativo contenuto.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Le macchine virtuali del lab devono avere accesso ad altre risorse di Azure o locali?

Quando si configura un account Lab, è anche possibile eseguire il peering con una rete virtuale. Per decidere se è necessario, prendere in considerazione le domande seguenti:

- **È necessario fornire l'accesso a un server licenze?**
  
   Se si prevede di usare le immagini di Azure Marketplace, il costo della licenza del sistema operativo viene aggregato ai prezzi per Lab Services. Non è quindi necessario fornire licenze per il sistema operativo stesso. Tuttavia, per altre applicazioni e software installati, è necessario fornire una licenza in base alle esigenze.

- **Le macchine virtuali del lab devono accedere ad altre risorse locali, ad esempio una condivisione file o un database?**

   Si crea una rete virtuale per fornire l'accesso alle risorse locali, in genere usando un gateway di rete virtuale da sito a sito. Se non è stata configurata una rete virtuale, è necessario investire ulteriore tempo per questa operazione.

- **Le macchine virtuali del lab devono accedere ad altre risorse di Azure che si trovano all'interno di una rete virtuale?**

   Se è necessario accedere alle risorse di Azure *non* protette all'interno di una rete virtuale, è possibile accedere a queste risorse attraverso la rete Internet pubblica, senza eseguire alcun peering.

Se la risposta è "Sì" a una o più domande, sarà necessario eseguire il peering dell'account Lab in una rete virtuale. Se hai risposto "non so", puoi rimandare questa decisione fino a un secondo momento. È sempre possibile scegliere di eseguire il peering di una rete virtuale dopo aver creato l'account Lab.

## <a name="set-up-your-lab-account"></a>Configurare l'account Lab

Dopo aver compreso i requisiti per l'account Lab, si è pronti per la configurazione.

1. **Creare l'account Lab.** Per istruzioni, vedere l'esercitazione sulla [creazione di un account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .

   Quando si crea un account Lab, può risultare utile acquisire familiarità con le risorse di Azure. Per altre informazioni, vedere gli articoli seguenti:

   - [Sottoscrizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Gruppo di risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Lab in aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selezione di un'area e una località](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Linee guida per la denominazione delle risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Aggiungere utenti al ruolo Lab Creator.** Per istruzioni, vedere [aggiunta di utenti al ruolo Lab Creator](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Inoltre, per altre informazioni sui diversi ruoli che possono essere assegnati agli utenti che gestiranno gli account Lab e i Lab, vedere la [Guida alla gestione dell'identità](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Connettersi a una rete virtuale peer.** Per istruzioni, vedere [connessione della rete del Lab con una rete virtuale peer](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Potrebbe anche essere necessario fare riferimento alle istruzioni sulla [configurazione dell'intervallo di indirizzi delle VM Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Abilitare ed esaminare le immagini.** Per istruzioni, vedere [Abilitazione di immagini di Azure Marketplace per gli autori di Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Per esaminare il contenuto di ogni immagine di Azure Marketplace, selezionare il nome dell'immagine. Ad esempio, lo screenshot seguente mostra i dettagli per l'immagine di Ubuntu Data Science VM:

   ![Screenshot di esaminare le immagini di Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Se si dispone di una raccolta di immagini condivise collegata all'account Lab e si desidera consentire la condivisione delle immagini personalizzate da parte degli autori del Lab, completare i passaggi simili a quelli mostrati nello screenshot seguente:

   ![Screenshot dell'abilitazione di immagini personalizzate in una raccolta di immagini condivise](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli account Lab](how-to-manage-lab-accounts.md)

- [Guida all'installazione di Lab in aula](setup-guide.md)
