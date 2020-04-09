---
title: Guida alla configurazione dell'account lab accelerato per Azure Lab ServicesAccelerated lab account setup guide for Azure Lab Services
description: Questa guida consente agli amministratori di configurare rapidamente un account lab per l'utilizzo all'interno della scuola.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879582"
---
# <a name="lab-account-setup-guide"></a>Guida alla configurazione dell'account lab

Come primo passaggio, gli amministratori devono configurare un account lab all'interno della sottoscrizione di Azure.As a first step, administrators should set up a lab account within your Azure subscription. Un account lab è un contenitore per i laboratori della classe e richiede solo pochi minuti per la configurazione.

## <a name="understand-your-schools-lab-account-requirements"></a>Comprendere i requisiti dell'account lab della scuola

Per comprendere come configurare l'account lab in base alle esigenze dell'istituto di istruzione, è necessario considerare queste domande.

### <a name="do-i-have-access-to-an-azure-subscription"></a>È possibile accedere a una sottoscrizione di Azure?

Per creare un account lab, è necessario accedere a una sottoscrizione di Azure configurata per l'istituto di istruzione. La scuola potrebbe avere uno o più abbonamenti. Si usa una sottoscrizione per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure, inclusi gli account lab.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Quanti account lab devono essere creati?

Per iniziare rapidamente, creare un singolo account lab e quindi creare altri account lab in base alle esigenze. Ad esempio, si potrebbe avere un account lab per reparto.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Chi devono essere proprietari e collaboratori dell'account di laboratorio?

Gli amministratori sono in genere i proprietari e i collaboratori di un account lab. Sono responsabili della gestione dei criteri che si applicano a tutti i lab contenuti nell'account lab. La persona che crea l'account lab è automaticamente un proprietario. È possibile aggiungere altri proprietari e collaboratori, in genere dal tenant di Azure Active Directory (Azure AD) associato alla sottoscrizione. Ciò può essere utile per gestire un account lab assegnando il ruolo di proprietario o collaboratore a livello di account lab.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>A chi sarà consentito creare e gestire i laboratori?

È possibile scegliere di fare in modo che gli amministratori e i membri della facoltà creino e gestiscano i lab. Questi utenti (in genere dal tenant di Azure AD associato alla sottoscrizione) vengono assegnati al ruolo Lab Creator all'interno dell'account lab.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Vuoi offrire ai creatori di laboratori la possibilità di salvare immagini che possono essere condivise tra laboratori?

Una raccolta di immagini condivise è un repository che è possibile utilizzare per salvare e condividere immagini. Se hai più classi che richiedono le stesse immagini, i creatori di laboratorio possono creare l'immagine una sola volta e condividerla tra i laboratori. Tuttavia, per iniziare, non è necessaria necessariamente una raccolta di immagini condivise, perché è sempre possibile aggiungerne una in un secondo momento.

Se hai risposto "sì" a questa domanda, devi creare o allegare una galleria di immagini condivise al tuo account lab. Se hai risposto "Non lo so", puoi rinviare la decisione più tardi.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Quali immagini in Azure Marketplace utilizzeranno i laboratori della classe?

Azure Marketplace offre centinaia di immagini che è possibile abilitare in modo che i creatori di lab possano usare l'immagine per la creazione del lab. Alcune immagini potrebbero includere tutto ciò di cui un lab ha già bisogno. In altri casi, è possibile utilizzare un'immagine come punto di partenza e quindi l'autore del lab può personalizzarla installando applicazioni o strumenti aggiuntivi.

Se non sai quali immagini dovrai usare, puoi sempre tornare su questo in un secondo momento per abilitarle. Inoltre, il modo migliore per vedere quali immagini sono disponibili è creare prima un account lab. In questo modo è possibile accedere, in modo da poter esaminare l'elenco delle immagini disponibili e il relativo contenuto.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Le macchine virtuali del lab devono avere accesso ad altre risorse di Azure o locali?

Quando si configura un account lab, è anche possibile eseguire il peer con una rete virtuale. Per decidere se è necessario, considerare le seguenti domande:

- **È necessario fornire l'accesso a un server licenze?**
  
   Se si prevede di usare le immagini di Azure Marketplace, il costo della licenza del sistema operativo viene incluso nei prezzi per i servizi lab. Pertanto, non è necessario fornire licenze per il sistema operativo stesso. Tuttavia, per il software aggiuntivo e le applicazioni installate, è necessario fornire una licenza in base alle esigenze.

- **Le macchine virtuali del lab devono accedere ad altre risorse locali, ad esempio una condivisione file o un database?**

   Si crea una rete virtuale per fornire l'accesso alle risorse locali, in genere utilizzando un gateway di rete virtuale da sito a sito. Se non è configurata una rete virtuale, è necessario investire ulteriore tempo per questo.

- **Le macchine virtuali lab devono accedere ad altre risorse di Azure che si trovano all'interno di una rete virtuale?**

   Se è necessario accedere a risorse di Azure *non* protette all'interno di una rete virtuale, è possibile accedere a queste risorse tramite Internet pubblico, senza eseguire alcun peering.

Se hai risposto "sì" a una o più domande, dovrai creare un peer dell'account lab a una rete virtuale. Se hai risposto "Non lo so", puoi rinviare la decisione più tardi. È sempre possibile scegliere di eseguire il peernetworka una rete virtuale dopo aver creato l'account lab.

## <a name="set-up-your-lab-account"></a>Configurare l'account lab

Dopo aver compreso i requisiti per l'account lab, è possibile configurarlo.

1. **Creare l'account lab.** Per istruzioni, fare riferimento all'esercitazione sulla [creazione di un account lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)

   Quando si crea un account lab, potrebbe essere utile acquisire familiarità con le risorse di Azure coinvolte. Per altre informazioni, vedere gli articoli seguenti:

   - [Sottoscrizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Gruppo di risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Account di laboratorio](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratorio di classroom](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selezione di una regione e di una posizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Linee guida per la denominazione delle risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Aggiungere utenti al ruolo di creatore lab.** Per istruzioni, vedere [Aggiunta di utenti al ruolo di creatore lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Inoltre, per ulteriori informazioni sui diversi ruoli che possono essere assegnati agli utenti che gestiranno gli account lab e i lab, vedere la [guida sulla gestione dell'identità](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Connettersi a una rete virtuale peer.** Per istruzioni, vedere [Connessione della rete del lab a una rete virtuale peer.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Potrebbe anche essere necessario fare riferimento alle istruzioni sulla [configurazione dell'intervallo](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)di indirizzi delle macchine virtuali lab.

4. **Abilitare e rivedere le immagini.** Per istruzioni, vedere Abilitazione delle immagini di [Azure Marketplace per i creatori di lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

   Per esaminare il contenuto di ogni immagine di Azure Marketplace, selezionare il nome dell'immagine. Ad esempio, la schermata seguente mostra i dettagli per l'immagine della macchina virtuale Ubuntu Data Science:For example, the following screenshot shows the details for the Ubuntu Data Science VM image:

   ![Screenshot di Esaminare le immagini di Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Se all'account lab è associata una raccolta di immagini condivisa e si desidera abilitare la condivisione di immagini personalizzate da parte dei creatori del lab, completare passaggi simili a quelli illustrati nella schermata seguente:If you have a shared image gallery attached to your lab account, and you want to enable custom images to be shared by lab creators, complete steps similar to those shown in the following screenshot:

   ![Screenshot dell'abilitazione di immagini personalizzate in una raccolta immagini condivise](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli account Lab](how-to-manage-lab-accounts.md)

- [Guida alla configurazione del laboratorio di Classroom](setup-guide.md)
