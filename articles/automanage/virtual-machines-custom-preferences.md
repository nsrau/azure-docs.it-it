---
title: Creare una preferenza personalizzata in Azure automanage per le macchine virtuali
description: Informazioni su come modificare il profilo di configurazione in gestione automatica di Azure per le macchine virtuali e impostare le proprie preferenze.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 7a716ed9bca6d34ad4dbcd2566837a839f71153b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450308"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Creare una preferenza personalizzata in Azure automanage per le macchine virtuali

Gestione automatica di Azure per le procedure consigliate per le macchine virtuali con profili di configurazione predefiniti che possono essere modificati, se necessario. Questo articolo illustra come è possibile impostare le proprie preferenze del profilo di configurazione quando si Abilita la gestione automatica in una macchina virtuale nuova o esistente.

Attualmente sono supportate le personalizzazioni in [backup di Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [Microsoft antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Non è possibile modificare il profilo di configurazione o la preferenza nella macchina virtuale mentre è abilitata la funzionalità Gestione automatica. È necessario disabilitare la gestione automatica per tale macchina virtuale e quindi abilitare di nuovo la gestione automatica con il profilo di configurazione e le preferenze desiderate.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> L'autorizzazione RBAC seguente è necessaria per abilitare la gestione autogestita: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Abilitare Gestione automatica per le VM in una VM esistente

1. Nella barra di ricerca cercare e selezionare **Gestione automatica - Procedure consigliate per le macchine virtuali di Azure**.

2. Selezionare **Abilita nella macchina virtuale esistente**.

3. Nel pannello **Seleziona macchine virtuali**:
    1. Filtrare le VM in base alla **sottoscrizione** e al **gruppo di risorse**.
    1. Selezionare la casella di controllo di ogni macchina virtuale di cui si vuole eseguire l'onboarding.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

4. In **Profilo di configurazione** fare clic su **Esplorare e modificare profili e preferenze**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

5. Nel pannello **selezionare il profilo di configurazione** e le preferenze selezionare un profilo sul lato sinistro: *sviluppo/test* per il testing, *Prod* per la produzione.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

6. Nel profilo scelto, in **Preferenze di configurazione** è disponibile un elenco a discesa in cui è possibile modificare determinati servizi.
    1. Fare clic su **Crea nuove preferenze**.
    1. Nel pannello **Crea una preferenza di configurazione** compilare la scheda nozioni di base:
        1. Sottoscrizione
        1. Resource group
        1. Nome preferenza
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

7. Passare alla scheda Preferenze e modificare le preferenze di configurazione desiderate.
        
    > [!NOTE]
    > Quando si modificano le configurazioni del profilo, saranno consentite solo le rettifiche che si adattano ai limiti superiore e inferiore delle procedure consigliate.

8. Esaminare il profilo di configurazione.
9. Fare clic sul pulsante **Crea**.

10. Fare clic sul pulsante **Abilita**.


## <a name="disable-automanage-for-vms"></a>Disabilitare Gestione automatica per le VM

Per smettere di usare Gestione automatica di Azure, basta disabilitare la funzionalità.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

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

Per ottenere risposte alle domande più frequenti, vedere le domande frequenti. 

> [!div class="nextstepaction"]
> [Domande frequenti](faq.md)