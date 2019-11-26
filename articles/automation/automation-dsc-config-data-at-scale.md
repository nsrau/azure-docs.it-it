---
title: Dati di configurazione a livello di automazione di Azure
description: Informazioni su come configurare i dati su larga scala per la configurazione dello stato in automazione di Azure.
keywords: DSC, PowerShell, configurazione, configurazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3e742f18e86c22b2d798eec5f6b715dfb298670a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231698"
---
# <a name="configuration-data-at-scale"></a>Dati di configurazione su larga scala

> Si applica a: Windows PowerShell 5,1

La gestione di centinaia o migliaia di server può costituire un problema.
I clienti hanno fornito un feedback che l'aspetto più difficile è quello di gestire [i dati di configurazione](/powershell/scripting/dsc/configurations/configdata).
Organizzazione delle informazioni tra costrutti logici come la posizione, il tipo e l'ambiente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-datum"></a>Progetto della community: Datum

Per risolvere questo problema, è stata creata una soluzione gestita dalla community denominata [Datum](https://github.com/gaelcolas/Datum) .
Datum si basa su grandi idee di altre piattaforme di gestione della configurazione e implementa lo stesso tipo di soluzione per PowerShell DSC.
Le informazioni sono [organizzate in file di testo](https://github.com/gaelcolas/Datum#3-intended-usage) in base a idee logiche.
Esempi:

- Impostazioni da applicare a livello globale
- Impostazioni da applicare a tutti i server in un percorso
- Impostazioni da applicare a tutti i server di database
- Impostazioni del singolo server

Queste informazioni sono organizzate nel formato di file preferito (JSON, YAML o PSD1).
Vengono quindi forniti i cmdlet per generare file di dati di configurazione [consolidando le informazioni](https://github.com/gaelcolas/Datum#datum-tree) di ogni file in in una visualizzazione singola di un server o di un ruolo del server.

Una volta generati i file di dati, è possibile usarli con gli [script di configurazione DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) per generare file MOF e [caricare i file MOF in automazione di Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Quindi registrare i server da [locale](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) o [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) per eseguire il pull delle configurazioni.

Per provare Datum, visitare il [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) e scaricare la soluzione oppure fare clic su "Project Site" per visualizzare la [documentazione](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione della Configuration Manager locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
