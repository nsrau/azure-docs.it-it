---
title: Eseguire l'onboarding di un gruppo di gestione nel Centro sicurezza di Azure
description: Informazioni su come usare una definizione di Criteri di Azure fornita per abilitare il Centro sicurezza di Azure per tutte le sottoscrizioni in un gruppo di gestione.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 4ecd436b548c29c520a7538970d4d703cc8488d2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027568"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Abilitare il Centro sicurezza per tutte le sottoscrizioni in un gruppo di gestione

È possibile usare Criteri di Azure per abilitare il Centro sicurezza di Azure per tutte le sottoscrizioni di Azure nello stesso gruppo di gestione. Questo approccio è più comodo rispetto all'accesso singolo dal portale e funziona anche se le sottoscrizioni appartengono a proprietari diversi. 

Per eseguire l'onboarding di un gruppo di gestione e di tutte le relative sottoscrizioni:

1. Come utente con autorizzazioni **Amministratore della sicurezza** , aprire Criteri di Azure e cercare la definizione **Abilitare Centro sicurezza di Azure nella sottoscrizione**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Definizione Abilitare Centro sicurezza di Azure nella sottoscrizione di Criteri di Azure":::

1. Selezionare **Assegna** e assicurarsi di impostare l'ambito sul livello del gruppo di gestione.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Assegnazione della definizione Abilitare Centro sicurezza di Azure nella sottoscrizione":::

    > [!TIP]
    > Non sono previsti parametri obbligatori, ad eccezione dell'ambito.

1. Selezionare **Crea un'attività di correzione** per assicurarsi che verrà eseguito l'onboarding di tutte le sottoscrizioni esistenti per cui il Centro sicurezza non è abilitato.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Creazione di un'attività di correzione per la definizione Abilitare Centro sicurezza di Azure nella sottoscrizione di Criteri di Azure":::

1. Dopo l'assegnazione, la definizione:

    1. Rileverà tutte le sottoscrizioni nel gruppo di gestione che non sono ancora registrate nel Centro sicurezza.
    1. Contrassegnerà tali sottoscrizioni come "non conformi".
    1. Contrassegnerà come "conformi" tutte le sottoscrizioni registrate (indipendentemente dall'attivazione o dalla disattivazione di Azure Defender).

    L'attività di correzione abiliterà quindi, gratuitamente, il Centro sicurezza per le sottoscrizioni non conformi.

> [!IMPORTANT]
> La definizione di criteri abiliterà il Centro sicurezza solo per sottoscrizioni **esistenti**. Per registrare le sottoscrizioni appena create, aprire la scheda Conformità, selezionare le sottoscrizioni non conformi pertinenti e creare un'attività di correzione. Ripetere questa procedura se sono presenti una o più nuove sottoscrizioni da monitorare con il Centro sicurezza.

## <a name="optional-modifications"></a>Modifiche facoltative

È possibile scegliere di modificare la definizione di Criteri di Azure in diversi modi: 

- **Definire la conformità in modo diverso** : il criterio fornito classifica come "non conformi" tutte le sottoscrizioni nel gruppo di gestione che non sono registrate con il Centro sicurezza. È possibile scegliere di impostarlo su tutte le sottoscrizioni per cui non è attivato Azure Defender.

    La definizione fornita consente di specificare *una* delle impostazioni di 'pricing' seguenti come conforme. Questo significa che una sottoscrizione impostata su 'standard' o 'free' è conforme.

    > [!TIP]
    > Quando un piano di Azure Defender è abilitato, l'impostazione usata per descriverlo è 'Standard'; quando è disabilitato, è 'Gratuito'. [Altre informazioni sui piani di Azure Defender](security-center-pricing.md)

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Se lo si imposta come segue, solo le sottoscrizioni impostate su 'standard ' verranno classificate come conformi:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definire alcuni piani di Azure Defender da applicare quando si abilita il Centro sicurezza** : il criterio fornito abilita il Centro sicurezza senza i piani facoltativi di Azure Defender. È possibile scegliere di abilitarne uno o entrambi.

    La sezione `deployment` della definizione fornita include un parametro `pricingTier`. Per impostazione predefinita, tale parametro è impostato su `free`, ma può essere modificato. 


## <a name="next-steps"></a>Passaggi successivi:

Ora che è stato eseguito l'onboarding di un intero gruppo di gestione, abilitare le protezioni avanzate di Azure Defender. 

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](security-center-pricing.md)
