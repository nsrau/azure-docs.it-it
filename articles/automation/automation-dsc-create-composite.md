---
title: Convertire le configurazioni in risorse composite per la configurazione dello stato - Automazione di AzureConvert configurations to composite resources for state configuration - Azure Automation
description: Informazioni su come convertire le configurazioni in risorse composte per la configurazione dello stato in Automazione di Azure.Learn how to convert configurations to composite resources for state configuration in Azure Automation.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585519"
---
# <a name="convert-configurations-to-composite-resources"></a>Convertire le configurazioni in risorse composite

> Si applica a: Windows PowerShell 5.1Applies To: Windows PowerShell 5.1

Una volta avviate le configurazioni di creazione, è possibile creare rapidamente "scenari" che gestiscono gruppi di impostazioni.
Alcuni esempi potrebbero essere:

- creare un server Web
- creare un server DNS
- creare un server SharePoint
- configurare un cluster SQL
- gestire le impostazioni del firewall
- gestire le impostazioni della password

Se si è interessati a condividere questo lavoro con altri utenti, l'opzione migliore consiste nel creare un pacchetto della configurazione come [risorsa composita](/powershell/scripting/dsc/resources/authoringresourcecomposite).
La creazione di risorse composite per la prima volta può essere complessa.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community Open Source.
> Il supporto è disponibile solo sotto forma di collaborazione GitHub, non da Microsoft.

## <a name="community-project-compositeresource"></a>Progetto comunitario: CompositeResourceCommunity project: CompositeResource

Per risolvere questa richiesta di verifica, è stata creata una soluzione gestita dalla community denominata [CompositeResource.A](https://github.com/microsoft/compositeresource) community maintained solution named CompositeResource has been created to resolve this challenge.

CompositeResource automatizza il processo di creazione di un nuovo modulo dalla configurazione.
Si inizia con dot [sourcing](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) lo script di configurazione sulla workstation (o server di compilazione) in modo che venga caricato in memoria.
Successivamente, anziché eseguire la configurazione per generare un file MOF, utilizzare la funzione fornita dal modulo CompositeResource per automatizzare una conversione.
Il cmdlet caricherà il contenuto della configurazione, otterrà l'elenco dei parametri e genererà un nuovo modulo con tutto il necessario.

Dopo aver generato un modulo, è possibile incrementare la versione e aggiungere note sulla versione ogni volta che si apportano modifiche e pubblicarla nel proprio [repository PowerShellGet.](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)

Dopo aver creato un modulo di risorse composito contenente la configurazione (o più configurazioni), è possibile usarli in Esperienza di [creazione componibile](/azure/automation/compose-configurationwithcompositeresources) in Azure o aggiungerli agli script di [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e caricare i file MOF in [Automazione di Azure.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registrare quindi i server in locale o [in Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) per eseguire il pull delle [configurazioni.](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)
L'ultimo aggiornamento del progetto ha anche pubblicato [runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) per Automazione di Azure per automatizzare il processo di importazione delle configurazioni da PowerShell Gallery.

Per provare ad automatizzare l'automazione della creazione di risorse composite per DSC, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) e scaricare la soluzione oppure fare clic su "Sito del progetto" per visualizzare la [documentazione](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
