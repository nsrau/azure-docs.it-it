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
ms.openlocfilehash: cf8e7340ce3554bf27364335457a5b7eadcdc049
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937643"
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
> Per abilitare la gestione, è necessaria l'autorizzazione RBAC seguente: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Abilitare la gestione delle macchine virtuali in una macchina virtuale esistente

1. Nella barra di ricerca cercare e selezionare **automanage: procedure consigliate per le macchine virtuali di Azure**.

2. Selezionare **Abilita in una macchina virtuale esistente**.

3. Nel pannello **Seleziona computer** :
    1. Filtrare l'elenco di macchine virtuali in base alla **sottoscrizione** e al **gruppo di risorse**.
    1. Selezionare la casella di controllo relativa a ogni macchina virtuale che si desidera caricare.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selezionare VM esistente dall'elenco delle macchine virtuali disponibili.":::

4. In **profilo di configurazione**fare clic su **Sfoglia e modificare i profili e le preferenze**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Sfogliare e modificare i profili e le preferenze.":::

5. Nel pannello **selezionare il profilo di configurazione** e le preferenze selezionare un profilo sul lato sinistro: *sviluppo/test* per il testing, *Prod* per la produzione.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Esplora profilo di configurazione di produzione.":::

6. Nel profilo scelto, in **Preferenze di configurazione** è disponibile un elenco a discesa in cui è possibile modificare determinati servizi.
    1. Fare clic su **Crea nuove preferenze**.
    1. Nel pannello **Crea una preferenza di configurazione** compilare la scheda nozioni di base:
        1. Subscription
        1. Resource group
        1. Nome preferenza
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Compilare le preferenze di configurazione.":::

7. Passare alla scheda Preferenze e modificare le preferenze di configurazione desiderate.
        
    > [!NOTE]
    > Quando si modificano le configurazioni del profilo, saranno consentite solo le rettifiche che si adattano ai limiti superiore e inferiore delle procedure consigliate.

8. Esaminare il profilo di configurazione.
9. Fare clic sul pulsante **Crea**.

10. Fare clic sul pulsante **Abilita**.


## <a name="disable-automanage-for-vms"></a>Disabilitare la gestione delle macchine virtuali

Interrompi rapidamente l'uso di Azure automanage per le macchine virtuali disabilitando la gestione.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Disabilitazione di automanage in una macchina virtuale.":::

1. Passare alla pagina **gestione automatica-procedure consigliate per macchine virtuali di Azure** in cui sono elencate tutte le macchine virtuali gestite automaticamente.
1. Selezionare la casella di controllo accanto alla macchina virtuale che si desidera disabilitare.
1. Fare clic sul pulsante **Disabilita automanagent** .
1. Leggere attentamente la messaggistica nel popup risultante prima di accettare la **disabilitazione**.


## <a name="clean-up-resources"></a>Pulire le risorse

Se è stato creato un nuovo gruppo di risorse per provare Azure automanage per le macchine virtuali e non è più necessario, è possibile eliminare il gruppo di risorse. Eliminando il gruppo vengono eliminate anche la macchina virtuale e tutte le risorse nel gruppo di risorse.

Azure automanage crea gruppi di risorse predefiniti in cui archiviare le risorse. Controllare i gruppi di risorse con la convenzione di denominazione "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" per pulire tutte le risorse.

1. Selezionare **Gruppo di risorse**.
1. Nella pagina del gruppo di risorse selezionare **Elimina**.
1. Quando richiesto, confermare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

Per ottenere risposte alle domande più frequenti, vedere le domande frequenti. 

> [!div class="nextstepaction"]
> [Domande frequenti](faq.md)