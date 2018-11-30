---
title: Note sulla versione di Azure Stack di convalida come servizio | Microsoft Docs
description: Note sulla versione di Azure Stack di convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a6245472f0fc3f65dcbeebdf251387efed5f02b8
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333019"
---
# <a name="release-notes-for-validation-as-a-service"></a>Note sulla versione per la convalida come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Questo articolo contiene le note sulla versione per la convalida di Azure Stack come servizio.

## <a name="version-401"></a>Versione 4.0.1

8 ottobre 2018

- Prerequisiti VaaS

    `Install-VaaSPrerequisites` non richiede le credenziali di amministratore di cloud. Se si esegue la versione più recente di questo cmdlet, vedere [scaricare e installare l'agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) per i comandi rivisti per l'installazione dei prerequisiti. Ecco i comandi:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Versione 4.0.0

29 agosto 2018

- Gli aggiornamenti prerequisiti VaaS e disco rigido virtuale

    `Install-VaaSPrerequisites` è ora necessario cloud le credenziali di amministratore per risolvere un problema durante la convalida del pacchetto. La documentazione all'indirizzo [scaricare e installare l'agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) è stato aggiornato con il codice seguente:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Il `$CloudAdminCreds` richiesto dallo script sia per Azure Stack istanza da convalidare. Non sono le credenziali di Azure Active Directory usate da un tenant VaaS.

- Aggiornamento dell'agente locale

    La versione precedente dell'agente locale non è compatibile con la versione corrente 4.0.0 versione del servizio. Tutti gli utenti devono aggiornare i rispettivi agenti locali. Visualizzare [scaricare e installare l'agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) per istruzioni sull'installazione dell'agente più recente.

- Aggiornamento di automazione di PowerShell

    Sono state apportate modifiche `LaunchVaaSTests` gli script di PowerShell che richiedono la versione più recente dei pacchetti di scripting. Visualizzare [avviare il flusso di lavoro di superamento Test](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) per istruzioni su come installare la versione più recente del pacchetto di scripting.

- Convalida di un portale del servizio

  - Le notifiche di firma del pacchetto

    Quando viene inviato un pacchetto di personalizzazione OEM come parte del flusso di lavoro di convalida del pacchetto, il formato del pacchetto verrà convalidato per assicurarsi che segue la specifica pubblicata. Se il pacchetto non è conforme, l'esecuzione avrà esito negativo. Notifiche tramite posta elettronica verranno inviate all'indirizzo di posta elettronica del contatto per il tenant Azure Active Directory registrato.

  - Categoria di test interattiva

    Il **Interactive** categoria di test è stato aggiunto. Questi test lasciare che i partner per esercitarsi con scenari di Azure Stack interattivi, non automatica.

  - Verifica di caratteristiche interattive

    La possibilità di fornire commenti e suggerimenti con lo stato attivo per alcune funzionalità è ora disponibile nel flusso di lavoro di superamento Test. Il `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` test verifica se determinati aggiornamenti sono stati applicati correttamente e quindi raccoglie i commenti.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di convalida come servizio](azure-stack-vaas-troubleshoot.md)