---
title: Configurare i dati in base a STIG per State Configuration di Automazione di Azure
description: Questo articolo descrive come configurare i dati in base a STIG per State Configuration di Automazione di Azure.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 113a6a259f0c69bdcc3b1684803af54ed7ecbddf
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186487"
---
# <a name="configure-data-based-on-stig"></a>Configurare i dati in base a STIG

> Si applica a: Windows PowerShell 5.1

La creazione di contenuti per la configurazione per la prima volta può risultare un'operazione complessa.
In molti casi l'obiettivo è quello di automatizzare la configurazione dei server conformi a una "baseline" che dovrebbe essere allineata a una raccomandazione del settore.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-powerstig"></a>Progetto della community: PowerSTIG

L'obiettivo di un progetto della community denominato [PowerSTIG](https://github.com/microsoft/powerstig) è proprio quello di risolvere questo problema grazie alla generazione di contenuti DSC basati sulle [informazioni pubbliche](https://public.cyber.mil/stigs/) fornite su STIG (Security Technical Implementation Guide).

La gestione delle baseline è più complessa di quanto sembri.
Molte organizzazioni devono [documentare le eccezioni](https://github.com/microsoft/powerstig#powerstigdata) alle regole e gestire tali dati su larga scala.
Per risolvere il problema, PowerSTIG fornisce [risorse composite](https://github.com/microsoft/powerstig#powerstigdsc) che consentono di gestire le singole aree della configurazione, invece di provare a gestire tutte le impostazioni in un unico file di grandi dimensioni.

Dopo aver generato le configurazioni, è possibile usare gli [script di configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e [caricarli tali file in Automazione di Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrare quindi i server da [locale](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) o [in Azure](./automation-dsc-onboarding.md#enable-azure-vms) per eseguire il pull delle configurazioni.

Per provare PowerSTIG, visitare [PowerShell Gallery](https://www.powershellgallery.com) e scaricare la soluzione oppure fare clic su "Sito di progetto" per visualizzare la [documentazione](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su PowerShell DSC, vedere [Panoramica della configurazione di Desired State Configuration per Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- In [Risorse DSC](/powershell/scripting/dsc/resources/resources) sono disponibili informazioni sulle risorse di PowerShell DSC.
- Per altri dettagli sulla configurazione di Configuration Manager locale, vedere [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig).
