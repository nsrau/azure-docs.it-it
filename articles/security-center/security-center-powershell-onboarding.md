---
title: Eseguire l'onboarding nel Centro sicurezza di Azure con PowerShell
description: Questo documento fornisce indicazioni dettagliate sul processo di onboarding del Centro sicurezza di Azure tramite i cmdlet di PowerShell.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 20fce643defd12897eceb1accc7ade8b10548568
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341166"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizzare l'onboarding del Centro sicurezza di Azure con PowerShell

È possibile proteggere i carichi di lavoro di Azure a livello di codice usando il modulo PowerShell del Centro sicurezza di Azure.
PowerShell consente di automatizzare le attività ed evitare l'errore umano insito nelle attività manuali. Questo è particolarmente utile nelle distribuzioni su larga scala che coinvolgono numerose sottoscrizioni con centinaia o migliaia di risorse e che devono essere protette fin dall'inizio.

L'onboarding del Centro sicurezza di Azure tramite PowerShell consente di automatizzare l'onboarding e la gestione delle risorse di Azure a livello di codice e di aggiungere i controlli di sicurezza necessari.

Questo articolo fornisce uno script di PowerShell di esempio che può essere modificato e applicato all'ambiente in uso per implementare il Centro sicurezza in tutte le sottoscrizioni. 

In questo esempio si abiliterà il Centro sicurezza in una sottoscrizione con ID d07c0080-170c-4c24-861d-9c817742786c e si applicheranno le impostazioni raccomandate che forniscono un livello elevato di protezione abilitando Azure Defender, che offre funzionalità avanzate di protezione e rilevamento delle minacce:

1. Abilitare [Azure Defender](azure-defender.md). 
 
2. Impostare l'area di lavoro Log Analytics a cui l'agente di Log Analytics invierà i dati raccolti nelle VM associate alla sottoscrizione, in questo esempio un'area di lavoro esistente definita dall'utente (myWorkspace).

3. Attivare il provisioning automatico dell'agente del Centro sicurezza che [distribuisce l'agente di Log Analytics](security-center-enable-data-collection.md#auto-provision-mma).

5. Impostare il [CISO dell'organizzazione come contatto di sicurezza per gli eventi significativi e gli avvisi del Centro sicurezza](security-center-provide-security-contact-details.md).

6. Assegnare i [criteri di sicurezza predefiniti](tutorial-security-policy.md) del Centro sicurezza.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i cmdlet del Centro sicurezza, è necessario seguire questa procedura:

1. Eseguire PowerShell come amministratore.

1. Eseguire i comandi seguenti in PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Caricare il Centro sicurezza usando PowerShell

1. Registrare le sottoscrizioni nel provider di risorse del Centro sicurezza:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Facoltativo: Impostare il livello di copertura (Azure Defender attivato/disattivato) delle sottoscrizioni. Se questa impostazione non viene definita, Defender è disattivato:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Configurare un'area di lavoro Log Analytics a cui gli agenti invieranno i report. È necessario avere già creato un'area di lavoro Log Analytics a cui le VM della sottoscrizione invieranno i report. È possibile definire l'invio di report alla stessa area di lavoro da più sottoscrizioni. Se non è specificata, verrà usata l'area di lavoro predefinita.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Effettuare il provisioning automatico dell'installazione dell'agente di Log Analytics nelle VM di Azure:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > È consigliabile abilitare il provisioning automatico per assicurarsi che le macchine virtuali di Azure siano protette automaticamente dal Centro sicurezza di Azure.
    >

1. (Facoltativo) È consigliabile definire i dettagli dei contatti di sicurezza per le sottoscrizioni caricate, che verranno usati come destinatari delle notifiche e degli avvisi generati dal Centro sicurezza:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Assegnare i criteri predefiniti del Centro sicurezza:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Enable Monitoring in Azure Security Center'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

L'onboarding nel Centro sicurezza di Azure con PowerShell è stato completato.

È ora possibile usare i cmdlet di PowerShell con gli script di automazione per eseguire l'iterazione a livello di codice nelle sottoscrizioni e nelle risorse. Ciò consente di risparmiare tempo e riduce la probabilità di errori umani. È possibile usare questo [script di esempio](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) come riferimento.




## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare PowerShell per automatizzare l'onboarding nel Centro sicurezza, vedere l'articolo seguente:

* [Az.Security](/powershell/module/az.security)

Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.