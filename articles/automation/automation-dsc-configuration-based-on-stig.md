---
title: Configuration based on STIG to use in state configuration - Azure Automation
description: Informazioni sulle configurazioni basate su STIG per la configurazione dello stato in Automazione di Azure.Learn about configurations based on STIG for state configuration in Azure Automation.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028268"
---
# <a name="configuration-based-on-stig"></a>Configurazione basata su STIG

> Si applica a: Windows PowerShell 5.1Applies To: Windows PowerShell 5.1

La creazione di contenuto di configurazione per la prima volta può essere complessa.
In molti casi, l'obiettivo è automatizzare la configurazione dei server seguendo una "linea di base" che si spera si allinei a una raccomandazione del settore.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community Open Source.
> Il supporto è disponibile solo sotto forma di collaborazione GitHub, non da Microsoft.

## <a name="community-project-powerstig"></a>Progetto comunitario: PowerSTIG

Un progetto comunitario denominato [PowerSTIG](https://github.com/microsoft/powerstig) mira a risolvere questo problema generando contenuti DSC basati su [informazioni pubbliche](https://public.cyber.mil/stigs/) fornite su STIG (Security Technical Implementation Guide),

Trattare con le linee di base è più complicato di quanto sembri.
Molte organizzazioni devono [documentare le eccezioni](https://github.com/microsoft/powerstig#powerstigdata) alle regole e gestire i dati su larga scala.
PowerSTIG risolve il problema fornendo [risorse composite](https://github.com/microsoft/powerstig#powerstigdsc) per risolvere ogni area della configurazione anziché tentare di risolvere l'intero intervallo di impostazioni in un unico file di grandi dimensioni.

Dopo aver generato le configurazioni, è possibile usare gli script di [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e caricare i file MOF in [Automazione di Azure.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registrare quindi i server in locale o [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) per eseguire il pull delle [configurazioni.](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)

Per provare PowerSTIG, visitare [PowerShell Gallery](https://www.powershellgallery.com) e scaricare la soluzione o fare clic su "Sito del progetto" per visualizzare la [documentazione](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
