---
title: Aggiornare Windows Defender Antivirus in Azure Stack
description: Informazioni dettagliate su come antivirus vengono mantenuti aggiornati in Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7899f235853991160e4d8b2c2c832d9a77171e83
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242196"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Aggiornare Windows Defender Antivirus in Azure Stack

[Windows Defender Antivirus](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) è una soluzione antimalware che offre sicurezza e protezione da virus. Ogni componente dell'infrastruttura di Azure Stack (host Hyper-V e macchine virtuali) è protetto con Windows Defender Antivirus. Per la protezione aggiornata, definizioni, motore e piattaforma di Windows Defender Antivirus richiedono gli aggiornamenti periodici. Come vengono applicati gli aggiornamenti dipendono dalla configurazione.

## <a name="connected-scenario"></a>Scenario connesso

Per definizione e il motore aggiornamenti antimalware, Azure Stack [provider di risorse di aggiornamento](azure-stack-updates.md#the-update-resource-provider) Scarica le definizioni antimalware e aggiornamenti del motore più volte al giorno. Ogni componente dell'infrastruttura di Azure Stack Ottiene l'aggiornamento dal provider di risorse di aggiornamento e applica automaticamente l'aggiornamento.

Per gli aggiornamenti della piattaforma antimalware, si applicano i [aggiornamento mensile di Azure Stack](azure-stack-apply-updates.md). L'aggiornamento mensile di Azure Stack include gli aggiornamenti della piattaforma Windows Defender Antivirus per il mese.

## <a name="disconnected-scenario"></a>Situazione di disconnessione

 Si applicano i [aggiornamento mensile di Azure Stack](azure-stack-apply-updates.md). L'aggiornamento mensile di Azure Stack include definizioni di Windows Defender Antivirus, motore e gli aggiornamenti della piattaforma per il mese.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulla sicurezza di Azure Stack](azure-stack-security-foundations.md)
