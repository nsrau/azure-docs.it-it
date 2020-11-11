---
title: Guida alla configurazione dell'account Lab accelerato per Azure Lab Services
description: Questa guida consente agli amministratori di configurare rapidamente un account Lab da usare all'interno della scuola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4d9a64fe23c3e5b74e77e704154f5e74bf2066d9
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490560"
---
# <a name="lab-account-setup-guide"></a>Guida all'installazione dell'account Lab
Per configurare l'ambiente di Azure Lab Services, gli amministratori devono prima configurare un **account Lab** nella sottoscrizione di Azure. Un account Lab è un contenitore per i Lab e richiede solo pochi minuti per la configurazione.

Questa guida include tre sezioni:
-  La prima sezione è incentrata sui prerequisiti che devono essere completati *prima* di configurare l'account Lab.
-  La seconda sezione fornisce informazioni aggiuntive sulla pianificazione delle impostazioni dell'account Lab.
-  Nella terza sezione vengono fornite istruzioni dettagliate per la configurazione di un account Lab.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Prerequisiti per la configurazione dell'account Lab
Questa sezione descrive i prerequisiti che è necessario completare prima di poter configurare un account Lab.

### <a name="obtain-an-azure-subscription"></a>Ottenere una sottoscrizione di Azure
Per creare un account Lab, è necessario accedere a una sottoscrizione di Azure configurata per la propria scuola. La scuola può avere una o più sottoscrizioni. Si usa una sottoscrizione per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure, inclusi gli account Lab.  Le sottoscrizioni di Azure sono in genere gestite dal reparto IT.  Per ulteriori informazioni, leggere l'argomento seguente:
 - [Guida dell'amministratore-sottoscrizione](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Stimare il numero di VM e le dimensioni delle VM necessarie
È necessario stimare il numero di macchine virtuali (VM) e le [dimensioni delle VM](https://docs.microsoft.com/azure/lab-services/administrator-guide#vm-sizing) necessarie per la propria scuola.  Leggere il post di Blog seguente per istruzioni su come strutturare i labs\images.  Questo post di Blog consentirà anche di decidere il numero di VM e le dimensioni delle VM necessarie:
- [Passaggio da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Vedere anche questo articolo che illustra le istruzioni aggiuntive su come strutturare i Lab:
- [Guida dell'amministratore-Lab](https://docs.microsoft.com/azure/lab-services/administrator-guide#classroom-lab)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Informazioni sui limiti delle VM di sottoscrizione e sulla capacità delle macchine virtuali regionali
Una volta stimato il numero di VM e le dimensioni delle VM per i Lab, è necessario:

- Verificare che il limite di capacità della sottoscrizione di Azure consenta il numero di VM e le dimensioni della macchina virtuale che si intende usare nei Lab.

- Creare l'account Lab all'interno di un'area con una capacità di macchina virtuale sufficiente disponibile.

Leggere il post di Blog seguente per altre informazioni: [limiti della sottoscrizione VM e capacità a livello](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)di area.

### <a name="decide-how-many-lab-accounts-to-create"></a>Decidere il numero di account Lab da creare

Per iniziare rapidamente, creare un singolo account Lab all'interno del proprio gruppo di risorse.  Successivamente, è possibile creare altri account Lab (e gruppi di risorse) in base alle esigenze. Ad esempio, si potrebbe avere un account Lab e un gruppo di risorse per reparto come un modo per separare chiaramente i costi.  Leggere gli articoli seguenti per altre informazioni sugli account Lab, i gruppi di risorse e la separazione dei costi:
- [Guida dell'amministratore-gruppo di risorse](https://docs.microsoft.com/azure/lab-services/administrator-guide#resource-group)
- [Guida dell'amministratore-account Lab](https://docs.microsoft.com/azure/lab-services/administrator-guide#lab-account) 
- [Gestione dei costi per Azure Lab Services](https://docs.microsoft.com/azure/lab-services/cost-management-guide)

## <a name="planning-your-lab-accounts-settings"></a>Pianificazione delle impostazioni dell'account Lab

Per pianificare le impostazioni dell'account Lab, è necessario prendere in considerazione le domande seguenti.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Chi deve essere proprietari e collaboratori dell'account Lab?

   Gli amministratori IT della scuola sono in genere i proprietari e i collaboratori di un account Lab. Sono responsabili della gestione dei criteri che si applicano a tutti i Lab contenuti nell'account Lab. La persona che crea l'account Lab è automaticamente un proprietario. È possibile aggiungere altri proprietari e collaboratori dal tenant di Azure Active Directory (AD) associato alla sottoscrizione. Per ulteriori informazioni sul proprietario dell'account Lab e sui ruoli collaboratore, vedere:
   -  [Guida dell'amministratore-gestione dell'identità](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Gli utenti del Lab visualizzano un solo elenco delle macchine virtuali a cui hanno accesso tra i tenant all'interno Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Chi sarà autorizzato a creare Lab?

   È possibile scegliere di creare Lab per i membri IT e docenti. Quando un utente crea un Lab, viene assegnato automaticamente come proprietario del Lab.  Per creare Lab, gli utenti (in genere dal tenant Azure AD associato alla sottoscrizione) devono essere assegnati al ruolo di autore del Lab nell'account Lab.  Per ulteriori informazioni sul ruolo di autore del Lab, vedere:
   -  [Guida dell'amministratore-gestione dell'identità](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Chi sarà autorizzato a gestire i Lab?

   È anche possibile scegliere di fare in modo che i membri del personale IT e dei docenti own\manage Labs *senza* la possibilità di creare Lab.  In questo caso, gli utenti del tenant Azure AD della sottoscrizione vengono assegnati al proprietario o al collaboratore dei Lab esistenti.  Per ulteriori informazioni sui ruoli di proprietario e collaboratore di un Lab, vedere:
   - [Guida dell'amministratore-gestione dell'identità](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Salvare le immagini che possono essere condivise tra i Lab?
Una raccolta di immagini condivise è un repository che è possibile usare per salvare e condividere le immagini. Per le classi che necessitano della stessa immagine, i creatori di Lab possono creare l'immagine e quindi esportarla nella raccolta immagini condivise.  Una volta esportate immagini nella raccolta immagini condivise, è possibile usarle per creare nuovi Lab.

Inoltre, è possibile creare le immagini nell'ambiente fisico e quindi importarle nella raccolta immagini condivise.  Per altri dettagli su questo processo, vedere il post di Blog seguente: 
- [Importare un'immagine personalizzata in una raccolta di immagini condivise](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Se si decide di usare una raccolta di immagini condivise, è necessario creare o alleghiare una raccolta di immagini condivise all'account Lab. È anche possibile rimandare questa decisione fino a un momento successivo, perché può essere collegata a un account Lab in qualsiasi momento.  Per ulteriori informazioni sulla raccolta di immagini condivise, vedere:
- [Guida dell'amministratore-raccolta di immagini condivise](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery)
- [Guida dell'amministratore-prezzi della raccolta immagini condivisa](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Quali immagini in Azure Marketplace useranno i laboratori?
Azure Marketplace offre centinaia di immagini che è possibile abilitare per consentire ai creatori di Lab di usare l'immagine per creare il proprio Lab. Alcune immagini possono includere tutti gli elementi necessari per un Lab. In altri casi, è possibile utilizzare un'immagine come punto di partenza, quindi l'autore del Lab può personalizzarlo installando applicazioni o strumenti aggiuntivi.

Se non si conoscono le immagini necessarie, è possibile tornare in seguito per abilitarle. Inoltre, il modo migliore per vedere quali immagini sono disponibili è creare innanzitutto un account Lab. In questo modo è possibile accedere a per esaminare l'elenco di immagini disponibili e il relativo contenuto.  Per ulteriori informazioni sulle immagini del Marketplace, leggere:
- [Specificare le immagini del Marketplace disponibili per gli autori di lab](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Le macchine virtuali del lab devono poter accedere ad altre risorse di Azure o locali?
Quando si configura un account Lab, è anche possibile fare il peering dell'account Lab con una rete virtuale (VNet).  Tenere presente che l'account VNet e lab deve trovarsi nella stessa area.  Per decidere se è necessario eseguire il peering con un VNet, considerare gli scenari seguenti:

- **Accesso a un server licenze**
  
   Quando si usano le immagini di Azure Marketplace, il costo della licenza del sistema operativo viene aggregato ai prezzi per Lab Services. Tuttavia, non è necessario fornire licenze per il sistema operativo stesso. Tuttavia, per altre applicazioni e software installati, è necessario fornire una licenza in base alle esigenze.  Per accedere a un server licenze:
   - È possibile scegliere di connettersi a un server licenze locale.  Per la connessione a un server licenze locale è necessaria un'installazione aggiuntiva.
   - Un'altra opzione, che è più veloce da configurare, consiste nel creare un server licenze che si ospita in una macchina virtuale di Azure.  La VM di Azure si trova all'interno di una rete virtuale di cui si esegue il peering nell'account Lab.

- **Accesso ad altre risorse locali, ad esempio una condivisione file o un database**

   Si crea un VNet per fornire l'accesso alle risorse locali, in genere usando un gateway di rete virtuale da sito a sito. La configurazione di questo tipo di ambiente richiederà ulteriore tempo.

- **Accesso ad altre risorse di Azure situate al di fuori di una VNet**

   Se è necessario accedere alle risorse di Azure *non* protette in una VNet, è possibile accedere a queste risorse attraverso la rete Internet pubblica, senza eseguire alcun peering.

Per ulteriori informazioni sulle reti virtuali, vedere:
- [Nozioni fondamentali sull'architettura-rete virtuale](https://docs.microsoft.com/azure/lab-services/classroom-labs-fundamentals#virtual-network)
- [Come connettersi a una rete virtuale](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network)
- [Come creare un Lab con una risorsa condivisa in Azure Lab Services](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource)

## <a name="set-up-your-lab-account"></a>Configurare l'account Lab

Al termine della pianificazione, si è pronti per configurare l'account Lab.  Gli stessi passaggi sono applicabili per la configurazione di un Lab [Azure Lab Services con i team](https://docs.microsoft.com/azure/lab-services/lab-services-within-teams-overview).

1. **Creare l'account Lab.** Per istruzioni, vedere l'esercitazione sulla [creazione di un account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .
   
    Per ulteriori informazioni sulla denominazione, vedere l'articolo seguente:

   - [Linee guida per la denominazione delle risorse](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Aggiungere utenti al ruolo Lab Creator.** Per istruzioni, vedere [aggiunta di utenti al ruolo Lab Creator](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).


3. **Connettersi a una rete virtuale peer.** Per istruzioni, vedere [connessione della rete del Lab con una rete virtuale peer](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Potrebbe anche essere necessario fare riferimento alle istruzioni sulla [configurazione dell'intervallo di indirizzi delle VM Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Abilitare ed esaminare le immagini.** Per istruzioni, vedere [Abilitazione di immagini di Azure Marketplace per gli autori di Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Per esaminare il contenuto di ogni immagine di Azure Marketplace, selezionare il nome dell'immagine. Ad esempio, lo screenshot seguente mostra i dettagli per l'immagine di Ubuntu Data Science VM:

   ![Screenshot di esaminare le immagini di Azure Marketplace](./media/setup-guide/review-marketplace-images.png)

   Se una raccolta di immagini condivise è associata all'account Lab e si vuole abilitare la condivisione delle immagini personalizzate da parte degli autori del Lab, completare passaggi simili, come illustrato nello screenshot seguente:

   ![Screenshot dell'abilitazione di immagini personalizzate in una raccolta di immagini condivise](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Passaggi successivi

Passaggi successivi comuni per la configurazione di un ambiente lab:

- [Gestire gli account Lab](how-to-manage-lab-accounts.md)
- [Guida all'installazione di Lab](setup-guide.md)
