---
title: Usare PowerShell per caricare il Centro sicurezza di Azure e proteggere la rete | Microsoft Docs
description: Questo documento fornisce indicazioni dettagliate sul processo di onboarding del Centro sicurezza di Azure tramite i cmdlet di PowerShell.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2018
ms.author: rkarlin
ms.openlocfilehash: 1b4cf6b5a27d985ee3d250a7d25a4a267a6810ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58133510"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizzare l'onboarding del Centro sicurezza di Azure con PowerShell

È possibile proteggere i carichi di lavoro di Azure a livello di codice usando il modulo PowerShell del Centro sicurezza di Azure.
PowerShell consente di automatizzare le attività ed evitare l'errore umano insito nelle attività manuali. Questo è particolarmente utile nelle distribuzioni su larga scala che coinvolgono numerose sottoscrizioni con centinaia o migliaia di risorse e che devono essere protette fin dall'inizio.

L'onboarding del Centro sicurezza di Azure tramite PowerShell consente di automatizzare l'onboarding e la gestione delle risorse di Azure a livello di codice e di aggiungere i controlli di sicurezza necessari.

Questo articolo fornisce uno script di PowerShell di esempio che può essere modificato e applicato all'ambiente in uso per implementare il Centro sicurezza in tutte le sottoscrizioni. 

In questo esempio si abiliterà il Centro sicurezza per una sottoscrizione con ID d07c0080-170c-4c24-861d-9c817742786c. Inoltre, si applicheranno le impostazioni consigliate che forniscono un elevato livello di protezione mediante l'implementazione del livello Standard del Centro sicurezza, che offre funzionalità avanzate di protezione e rilevamento delle minacce:

1. Impostare il [livello di protezione standard ASC](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Impostare l'area di lavoro di Log Analytics a cui Microsoft Monitoring Agent invierà i dati che raccolti nelle VM associate alla sottoscrizione. In questo esempio si tratta di un'area di lavoro esistente definita dall'utente (myWorkspace).

3. Attivare il provisioning automatico dell'agente del Centro sicurezza, che consente di [distribuire Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Impostare il [CISO dell'organizzazione come contatto di sicurezza per gli eventi significativi e gli avvisi ASC](security-center-provide-security-contact-details.md).

6. Assegnare i [criteri di sicurezza predefiniti](tutorial-security-policy.md) del Centro sicurezza.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i cmdlet del Centro sicurezza, è necessario seguire questa procedura:

1.  Eseguire PowerShell come amministratore.
2.  Eseguire i comandi seguenti in PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Caricare il Centro sicurezza usando PowerShell

1.  Registrare le sottoscrizioni nel provider di risorse del Centro sicurezza:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Facoltativo: Impostare il livello di copertura (piano tariffario) delle sottoscrizioni (se non è definito, il piano tariffario è impostato su Gratuito):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Configurare un'area di lavoro di Log Analytics a cui gli agenti invieranno i report. È necessario avere già creato un'area di lavoro di Log Analytics a cui le VM della sottoscrizione invieranno i report. È possibile definire l'invio di report alla stessa area di lavoro da più sottoscrizioni. Se non è specificata, verrà usata l'area di lavoro predefinita.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Effettuare il provisioning automatico dell'installazione di Microsoft Monitoring Agent nelle VM di Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > È consigliabile abilitare il provisioning automatico per assicurarsi che le macchine virtuali di Azure siano protette automaticamente dal Centro sicurezza di Azure.
    >

5.  Facoltativo: È consigliabile definire i dettagli dei contatti di sicurezza per le sottoscrizioni caricate, che verranno usati come destinatari delle notifiche e degli avvisi generati dal Centro sicurezza:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Assegnare i criteri predefiniti del Centro sicurezza:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

È stato completato l'onboarding del Centro sicurezza di Azure con PowerShell.

È ora possibile usare i cmdlet di PowerShell con gli script di automazione per eseguire l'iterazione a livello di codice nelle sottoscrizioni e nelle risorse. Ciò consente di risparmiare tempo e riduce la probabilità di errori umani. È possibile usare questo [script di esempio](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) come riferimento.






## <a name="see-also"></a>Vedere anche 
Per altre informazioni su come usare PowerShell per automatizzare l'onboarding nel Centro sicurezza, vedere l'articolo seguente:

* [Az.Security](https://github.com/Azure/azure-powershell/blob/preview/src/ResourceManager/Security/Commands.Security/help/AzureRM.Security.md).

Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
