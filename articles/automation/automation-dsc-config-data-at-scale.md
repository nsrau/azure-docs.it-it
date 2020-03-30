---
title: Dati di configurazione su larga scala - Automazione di AzureConfiguration data at scale - Azure Automation
description: Informazioni su come configurare i dati su larga scala per la configurazione dello stato in Automazione di Azure.Learn how to configure data at scale for state configuration in Azure Automation.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028297"
---
# <a name="configuration-data-at-scale"></a>Dati di configurazione su larga scala

> Si applica a: Windows PowerShell 5.1Applies To: Windows PowerShell 5.1

Gestire centinaia o migliaia di server può essere una sfida.
I clienti hanno fornito un feedback che l'aspetto più difficile è in realtà la gestione dei dati di [configurazione](/powershell/scripting/dsc/configurations/configdata).
Organizzazione delle informazioni tra costrutti logici come posizione, tipo e ambiente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community Open Source.
> Il supporto è disponibile solo sotto forma di collaborazione GitHub, non da Microsoft.

## <a name="community-project-datum"></a>Progetto comunitario: Datum

Per risolvere questa sfida è stata creata una soluzione gestita dalla comunità denominata [Datum.](https://github.com/gaelcolas/Datum)
Datum si basa su grandi idee da altre piattaforme di gestione della configurazione e implementa lo stesso tipo di soluzione per PowerShell DSC.
Le informazioni sono [organizzate in file di testo](https://github.com/gaelcolas/Datum#3-intended-usage) sulla base di idee logiche.
Alcuni esempi potrebbero essere:

- Impostazioni da applicare a livello globale
- Impostazioni da applicare a tutti i server in una posizione
- Impostazioni da applicare a tutti i server di database
- Impostazioni del singolo server

Queste informazioni sono organizzate nel formato di file preferito (JSON, Yaml o PSD1).
Vengono quindi forniti cmdlet per generare file di dati di configurazione [consolidando le informazioni](https://github.com/gaelcolas/Datum#datum-tree) di ogni file in una singola visualizzazione di un ruolo del server o del server.

Dopo aver generato i file di dati, è possibile utilizzarli con gli script di [configurazione DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) per generare file MOF e caricare i file MOF in [Automazione di Azure.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registrare quindi i server in locale o [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) per eseguire il pull delle [configurazioni.](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)

Per provare Datum, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) e scaricare la soluzione o fare clic su "Sito del progetto" per visualizzare la [documentazione](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
