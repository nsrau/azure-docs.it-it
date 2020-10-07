---
title: Avvio rapido - Abilitare Gestione automatica di Azure per le macchine virtuali nel portale di Azure
description: Informazioni su come abilitare rapidamente Gestione automatica per le macchine virtuali in una VM nuova o esistente nel portale di Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: d66c19ce3a9786a5ca0f1390acb398c2a9cf502f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445794"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Avvio rapido: Abilitare Gestione automatica di Azure per le macchine virtuali nel portale di Azure

Questo argomento di avvio rapido spiega come usare il portale di Azure per abilitare Gestione automatica di Azure per le macchine virtuali in una VM nuova o esistente.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> Per abilitare Gestione automatica usando un account di Gestione automatica esistente, è necessario avere il ruolo **Collaboratore**. Se si abilita Gestione automatica con un nuovo account di Gestione automatica, è necessario avere le autorizzazioni seguenti: ruolo **Proprietario** o **Collaboratore** insieme al ruolo **Amministratore Accesso utenti**.


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Abilitare Gestione automatica per le VM in una VM esistente

1. Nella barra di ricerca cercare e selezionare **Gestione automatica - Procedure consigliate per le macchine virtuali di Azure**.

2. Selezionare **Abilita nella macchina virtuale esistente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Abilita nella macchina virtuale esistente.":::

3. Nel pannello **Seleziona macchine virtuali**:
    1. Filtrare le VM in base alla **sottoscrizione** e al **gruppo di risorse**.
    1. Selezionare la casella di controllo di ogni macchina virtuale di cui si vuole eseguire l'onboarding.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Abilita nella macchina virtuale esistente.":::

4. In **Profilo di configurazione** fare clic su **Esplorare e modificare profili e preferenze**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Abilita nella macchina virtuale esistente.":::

5. Nel pannello **Selezionare il profilo di configurazione e le preferenze**:
    1. Selezionare un profilo a sinistra: *Sviluppo/test* per l'ambiente di test, *Produzione* per quello di produzione.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Abilita nella macchina virtuale esistente.":::

6. Fare clic sul pulsante **Abilita**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Abilitare Gestione automatica per le VM in una nuova VM

Accedere al portale di Azure [qui](https://aka.ms/automanageportalnextstep) per creare una nuova VM e abilitare Gestione automatica.

1. Seguire la procedura di creazione in [Avvio rapido - Creare una macchina virtuale Windows nel portale di Azure](..\virtual-machines\windows\quick-create-portal.md).

2. Dopo la distribuzione della VM, verrà visualizzata la pagina di stato della distribuzione con i **passaggi successivi** consigliati nella parte inferiore.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Abilita nella macchina virtuale esistente.":::

3. In **Passaggi successivi** selezionare **Abilita Gestione automatica delle procedure consigliate per le macchine virtuali**.

4. Nella pagina **Gestione automatica - Procedure consigliate per le macchine virtuali di Azure** la VM appena creata verrà automaticamente inserita nell'elenco **Macchine virtuali**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Abilita nella macchina virtuale esistente.":::

5. In **Profilo di configurazione** fare clic su **Esplorare e modificare profili e preferenze**.

6. Nel pannello **Selezionare il profilo di configurazione e le preferenze**:
    1. Selezionare un profilo a sinistra: *Sviluppo/test* per l'ambiente di test, *Produzione* per quello di produzione.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Abilita nella macchina virtuale esistente.":::

7. Fare clic sul pulsante **Abilita**.

## <a name="disable-automanage-for-vms"></a>Disabilitare Gestione automatica per le VM

Per smettere di usare Gestione automatica di Azure, basta disabilitare la funzionalità.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Abilita nella macchina virtuale esistente.":::

1. Passare alla pagina **Gestione automatica - Procedure consigliate per le macchine virtuali di Azure** in cui sono elencate tutte le VM gestite automaticamente.
1. Selezionare la casella di controllo accanto alla macchina virtuale da disabilitare.
1. Fare clic sul pulsante **Disabilita gestione automatica**.
1. Leggere attentamente i messaggi nella finestra popup visualizzata prima di fare clic su **Disabilita**.


## <a name="clean-up-resources"></a>Pulire le risorse

Se per provare Gestione automatica di Azure per le macchine virtuali è stato creato un nuovo gruppo di risorse che ora non serve più, è possibile eliminarlo. Eliminando il gruppo di risorse vengono eliminate anche la VM e tutte le risorse all'interno del gruppo.

Gestione automatica di Azure crea gruppi di risorse predefiniti in cui archiviare le risorse. Selezionare i gruppi di risorse con la convenzione di denominazione "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" per pulire tutte le risorse.

1. Selezionare **Gruppo di risorse**.
1. Nella pagina del gruppo di risorse selezionare **Elimina**.
1. Quando richiesto, confermare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata abilitata la funzionalità Gestione automatica di Azure per le macchine virtuali. 

A questo punto è possibile apprendere come creare e applicare preferenze personalizzate quando si abilita Gestione automatica nella macchina virtuale. 

> [!div class="nextstepaction"]
> [Gestione automatica di Azure per le macchine virtuali - Profilo di configurazione personalizzato](virtual-machines-custom-preferences.md)
