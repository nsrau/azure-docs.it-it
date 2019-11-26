---
title: Creare configurazioni da server esistenti-automazione di Azure
description: Informazioni su come creare configurazioni da server esistenti per automazione di Azure.
keywords: DSC, PowerShell, configurazione, configurazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 35f967e946854c3ca097db379015a7ee0bbe2f3d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231676"
---
# <a name="create-configurations-from-existing-servers"></a>Creazione di configurazioni da server esistenti

> Si applica a: Windows PowerShell 5,1

La creazione di configurazioni da server esistenti può essere un'attività complessa.
È possibile che non si desiderino *tutte* le impostazioni, solo quelle a cui si è interessati.
È anche necessario conoscere l'ordine in cui devono essere applicate le impostazioni affinché la configurazione venga applicata correttamente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-reversedsc"></a>Progetto della community: ReverseDSC

È stata creata una soluzione gestita dalla community denominata [ReverseDSC](https://github.com/microsoft/reversedsc) per lavorare in quest'area, avviando SharePoint.

La soluzione si basa sulla [risorsa SharePointDSC](https://github.com/powershell/sharepointdsc) e la estende per orchestrare la [raccolta di informazioni](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dai server di SharePoint esistenti.
La versione più recente include più [modalità di estrazione](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) per determinare il livello di informazioni da includere.

Il risultato dell'uso della soluzione è la generazione di [dati di configurazione](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) da usare con gli script di configurazione SharePointDSC.

Una volta generati i file di dati, è possibile usarli con gli [script di configurazione DSC](/powershell/scripting/dsc/overview/overview) per generare file MOF e [caricare i file MOF in automazione di Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Quindi registrare i server da [locale](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) o [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) per eseguire il pull delle configurazioni.

Per provare ReverseDSC, visitare il [PowerShell Gallery](https://www.powershellgallery.com/packages/ReverseDSC/) e scaricare la soluzione oppure fare clic su "Project Site" per visualizzare la [documentazione](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione della Configuration Manager locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
