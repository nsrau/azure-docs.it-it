---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b48b68d354a21430d53799f9c6a58bf1f7768f6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820117"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Controlla i computer con impostazioni di sicurezza delle password non sicure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Questa iniziativa distribuisce i requisiti dei criteri e controlla i computer con impostazioni di sicurezza delle password non sicure. Per altre informazioni sui criteri di configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |9 |1.0.0 |
|[Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Questa iniziativa aggiunge un'identità gestita assegnata dal sistema e distribuisce l'estensione Configurazione guest appropriata per la piattaforma alle macchine virtuali che sono idonee per il monitoraggio da parte dei criteri di Configurazione guest. Si tratta di un prerequisito per i criteri di Configurazione guest e deve essere assegnato all'ambito di assegnazione dei criteri prima di usare qualsiasi criterio di Configurazione guest. Per altre informazioni su Configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0-preview |
|[I computer Windows devono soddisfare i requisiti per la baseline di sicurezza di Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Questa iniziativa controlla i computer Windows con impostazioni non conformi alla baseline di sicurezza di Azure. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
