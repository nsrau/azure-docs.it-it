---
title: Creare configurazioni da server esistenti per State Configuration di Automazione di Azure
description: Questo articolo illustra come creare configurazioni da server esistenti per State Configuration di Automazione di Azure.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186504"
---
# <a name="create-configurations-from-existing-servers"></a>Creare configurazioni da server esistenti

> Si applica a: Windows PowerShell 5.1

La creazione di configurazioni da server esistenti può essere un'attività complessa.
Potrebbe non essere necessario usare *tutte le impostazioni*, ma solo quelle a cui si è interessati.
Anche in quel caso è necessario conoscere l'ordine in cui devono essere applicate le impostazioni affinché la configurazione venga applicata correttamente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-reversedsc"></a>Progetto della community: ReverseDSC

È stata creata una soluzione gestita dalla community denominata [ReverseDSC](https://github.com/microsoft/reversedsc) da usare in quest'area avviando SharePoint.

La soluzione si basa sulla [risorsa SharePointDSC](https://github.com/powershell/sharepointdsc) e si estende per controllare la [raccolta di informazioni](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dai server SharePoint esistenti.
L'ultima versione dispone di più [modalità di estrazione](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) per determinare il livello di informazioni da includere.

Il risultato dell'uso di questa soluzione è la creazione [dati di configurazione](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) da usare con gli script di configurazione SharePointDSC.

Dopo aver generato i file di dati, è possibile usarli con gli [script di configurazione DSC](/powershell/scripting/dsc/overview/overview) per generare file MOF e [caricarli in Automazione di Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrare quindi i server da [locale](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) o [in Azure](./automation-dsc-onboarding.md#enable-azure-vms) per eseguire il pull delle configurazioni.

Per provare ReverseDSC, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/ReverseDSC/) e scaricare la soluzione oppure fare clic su "Sito di progetto" per visualizzare la [documentazione](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su PowerShell DSC, vedere [Panoramica della configurazione di Desired State Configuration per Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- In [Risorse DSC](/powershell/scripting/dsc/resources/resources) sono disponibili informazioni sulle risorse di PowerShell DSC.
- Per altri dettagli sulla configurazione di Configuration Manager locale, vedere [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig).
