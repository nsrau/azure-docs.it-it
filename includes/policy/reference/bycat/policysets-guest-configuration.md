---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 25029c14d62c0d52cf3c8d3c3e20a1975092790c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311602"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Controlla i computer con impostazioni di sicurezza delle password non sicure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Questa iniziativa distribuisce i requisiti dei criteri e controlla i computer con impostazioni di sicurezza delle password non sicure. Per altre informazioni sui criteri di configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |9 |1.0.0 |
|[Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Questa iniziativa aggiunge un'identità gestita assegnata dal sistema e distribuisce l'estensione Configurazione guest appropriata per la piattaforma alle macchine virtuali che sono idonee per il monitoraggio da parte dei criteri di Configurazione guest. Si tratta di un prerequisito per i criteri di Configurazione guest e deve essere assegnato all'ambito di assegnazione dei criteri prima di usare qualsiasi criterio di Configurazione guest. Per altre informazioni su Configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0-preview |
|[I computer Windows devono soddisfare i requisiti per la baseline di sicurezza di Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Questa iniziativa controlla i computer Windows con impostazioni non conformi alla baseline di sicurezza di Azure. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
