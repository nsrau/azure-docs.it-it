---
title: Creare configurazioni da server esistenti - Automazione di AzureCreate configurations from existing servers - Azure Automation
description: Informazioni su come creare configurazioni da server esistenti per Automazione di Azure.Learn how to create configurations from existing servers for Azure Automation.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030133"
---
# <a name="create-configurations-from-existing-servers"></a>Creare configurazioni da server esistenti

> Si applica a: Windows PowerShell 5.1Applies To: Windows PowerShell 5.1

La creazione di configurazioni da server esistenti può essere un'attività complessa.
Potresti non volere *tutte le* impostazioni, solo quelle che ti interessano.
Anche allora è necessario sapere in quale ordine le impostazioni devono essere applicate affinché la configurazione si applichi correttamente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community Open Source.
> Il supporto è disponibile solo sotto forma di collaborazione GitHub, non da Microsoft.

## <a name="community-project-reversedsc"></a>Progetto comunitario: ReverseDSC

Una soluzione gestita dalla comunità denominata ReverseDSC è stata creata per funzionare in quest'area a partire da SharePoint.A community maintained solution named [ReverseDSC](https://github.com/microsoft/reversedsc) has been created to work in this area starting SharePoint.

La soluzione si basa sulla [risorsa SharePointDSC](https://github.com/powershell/sharepointdsc) e la estende per orchestrare la raccolta di [informazioni](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dai server SharePoint esistenti.
La versione più recente dispone di più [modalità](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) di estrazione per determinare il livello di informazioni da includere.

Il risultato dell'utilizzo della soluzione è la generazione di dati di [configurazione](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) da utilizzare con gli script di configurazione SharePointDSC.

Dopo aver generato i file di dati, è possibile utilizzarli con gli script di [configurazione DSC](/powershell/scripting/dsc/overview/overview) per generare file MOF e caricare i file MOF in [Automazione di Azure.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registrare quindi i server in locale o [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) per eseguire il pull delle [configurazioni.](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)

Per provare ReverseDSC, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/ReverseDSC/) e scaricare la soluzione o fare clic su "Sito del progetto" per visualizzare la [documentazione](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
