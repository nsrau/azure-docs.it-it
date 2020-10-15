---
title: Isolamento rete in Azure DevTest Labs
description: Informazioni sull'isolamento rete in Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875771"
---
# <a name="network-isolation-in-devtest-labs"></a>Isolamento rete in DevTest Labs

Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) funge da limite di sicurezza, isolando le risorse di Azure dalla rete Internet pubblica. È anche possibile aggiungere una rete virtuale di Azure alla rete locale per potersi connettere in modo sicuro alle risorse locali. In DevTest Labs è possibile scegliere di [isolare tutte le macchine virtuali](devtest-lab-configure-vnet.md) e gli [ambienti](connect-environment-lab-virtual-network.md) Lab nella rete per assicurarsi che le risorse del Lab seguano i criteri di rete dell'organizzazione. 

In qualità di proprietario del Lab, è anche possibile scegliere di isolare completamente il Lab, ovvero isolando le macchine virtuali e gli ambienti alla rete selezionata, è anche possibile isolare l'account di archiviazione Lab e gli insiemi di credenziali delle chiavi creati nella sottoscrizione. Questo articolo illustra la creazione di un Lab con isolamento rete. 

Vedere anche gli articoli seguenti:

- [Come DevTest Labs USA l'account di archiviazione Lab](encrypt-storage.md)
- [Come DevTest Labs USA gli insiemi di credenziali delle chiavi](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> L'isolamento rete è attualmente supportato solo per le nuove creazioni Lab.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Passaggi per abilitare l'isolamento rete durante la creazione del Lab

1. Durante la creazione del Lab, passare alla scheda **rete** .
1. È possibile selezionare una rete **predefinita** che il Lab creerà per l'utente oppure selezionare una rete esistente dall'elenco a discesa. Sarà possibile selezionare solo le reti che si trovano nella stessa area e nella stessa sottoscrizione di quella del Lab. 

    > [!div class="mx-imgBorder"]
    > ![Creare un lab](./media/network-isolation/create-lab.png)
1. Selezionare una subnet.

    > [!div class="mx-imgBorder"]
    > ![Creare una subnet](./media/network-isolation/create-lab-subnet.png)
1. Se si sceglie di isolare le risorse del Lab (account di archiviazione Lab e insieme di credenziali delle chiavi) nella rete predefinita, non è necessaria alcuna azione aggiuntiva e il Lab gestirà le risorse di isolamento in futuro.
 
    > [!div class="mx-imgBorder"]
    > ![Isolamento rete](./media/network-isolation/isolate-lab-resources.png)
1. Se si sceglie di isolare le risorse del Lab (account di archiviazione Lab e insieme di credenziali delle chiavi) in una rete esistente selezionata, è necessario completare la procedura seguente dopo la creazione del Lab per verificare che il Lab continui a funzionare in modalità isolata. 
 
    > [!div class="mx-imgBorder"]
    > ![Isolare le risorse](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Il proprietario del lab deve completare questi passaggi prima di configurare e/o creare risorse nel Lab.

### <a name="steps-to-follow-post-lab-creation"></a>Passaggi per seguire la creazione del Lab

1. Nella home page per il Lab selezionare il gruppo di **risorse** nella pagina **Panoramica** . Verrà visualizzata la pagina del **gruppo di risorse** per il gruppo di risorse che contiene il Lab. 
 
   > [!div class="mx-imgBorder"]
   > ![Laboratorio contoso](./media/network-isolation/contoso-lab.png)
1. Selezionare l'account di archiviazione di Azure del Lab. La convenzione di denominazione per l'account di archiviazione Lab è: a<*labNameWithoutInvalidCharacters* > *<numero a 4 cifre*>. Ad esempio, se il nome del Lab è contosolab, il nome dell'account di archiviazione potrebbe essere acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Test di contoso](./media/network-isolation/contoso-test.png)
1. Nell'account di archiviazione passare a firewall e reti virtuali e assicurarsi che la casella di controllo "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione" sia selezionata. Poiché [DevTest Labs è un servizio Microsoft attendibile](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services), questa opzione consentirà al Lab di funzionare normalmente in modalità di isolamento rete. 

   > [!div class="mx-imgBorder"]
   > ![Firewall di Contoso Lab](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Fare quindi clic su **+ Aggiungi rete virtuale esistente**, selezionare la rete virtuale e la subnet selezionata durante la creazione del Lab e fare clic su **Abilita**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso My VNET](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Una volta che l'endpoint del servizio è stato abilitato per la rete virtuale selezionata, fare clic su **Aggiungi**. 

   > [!div class="mx-imgBorder"]
   > ![Aggiungere](./media/network-isolation/contoso-firewall-add.png)
 
Con questa operazione, archiviazione di Azure consentirà le connessioni in ingresso dalla rete virtuale aggiunta e consentirà al Lab di funzionare correttamente in modalità di isolamento rete. 

È anche possibile scegliere di automatizzare questi passaggi per configurare questa impostazione per più Lab. 

[Altre informazioni sulla gestione delle regole di accesso alla rete predefinite per archiviazione di Azure con PowerShell e CLI](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Aspetti da ricordare quando si usa un Lab in modalità di isolamento rete

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Accesso all'account di archiviazione del Lab all'esterno del Lab 

All'interno di un Lab con isolamento rete, per azioni come il caricamento di un disco rigido virtuale nell'account di archiviazione del Lab per la creazione di immagini personalizzate da, il proprietario del Lab dovrà abilitare in modo esplicito l'accesso all'account di archiviazione da un endpoint consentito. A tale scopo, è possibile creare una macchina virtuale e accedere in modo sicuro all'account di archiviazione del Lab dalla macchina virtuale. 

[Altre informazioni sull'accesso privato a un account di archiviazione da una macchina virtuale](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Esportazione dei dati di utilizzo dal Lab 

All'interno di un Lab con isolamento rete, per [esportare i dati di utilizzo personali per il Lab](personal-data-delete-export.md), il proprietario del Lab dovrà specificare in modo esplicito l'account di archiviazione e generare un BLOB nell'account per archiviare i dati. 

Se non viene specificato un account di archiviazione, questa operazione avrà esito negativo in modalità di isolamento rete perché l'account di archiviazione del Lab non è accessibile al Lab per utilizzarlo in caso di nessun account di archiviazione fornito dal cliente. 

[Altre informazioni sull'esportazione dei dati di utilizzo del Lab in un account di archiviazione specifico](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Passaggi successivi

[Creare o modificare automaticamente i lab usando i modelli di Azure Resource Manager con PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
