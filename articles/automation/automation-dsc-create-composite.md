---
title: Convertire le configurazioni in risorse composite per la configurazione dello stato-automazione di Azure
description: Informazioni su come convertire le configurazioni in risorse composite per la configurazione dello stato in automazione di Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585519"
---
# <a name="convert-configurations-to-composite-resources"></a>Convertire le configurazioni in risorse composite

> Si applica a: Windows PowerShell 5,1

Una volta avviata la creazione di configurazioni, è possibile creare rapidamente "scenari" per la gestione dei gruppi di impostazioni.
Alcuni esempi potrebbero essere:

- creazione di un server Web
- creazione di un server DNS
- creazione di un server SharePoint
- configurare un cluster SQL
- gestire le impostazioni del firewall
- Gestisci impostazioni password

Se si è interessati a condividere questo lavoro con altri utenti, l'opzione migliore consiste nel creare un pacchetto della configurazione come [risorsa composita](/powershell/scripting/dsc/resources/authoringresourcecomposite).
La creazione di risorse composite per la prima volta può risultare eccessiva.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-compositeresource"></a>Progetto della community: CompositeResource

Per risolvere questo problema, è stata creata una soluzione gestita dalla community denominata [CompositeResource](https://github.com/microsoft/compositeresource) .

CompositeResource automatizza il processo di creazione di un nuovo modulo dalla configurazione.
Si inizia con l'assegnazione di un [punto](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) allo script di configurazione nella workstation (o nel server di compilazione), in modo che venga caricato in memoria.
Quindi, invece di eseguire la configurazione per generare un file MOF, usare la funzione fornita dal modulo CompositeResource per automatizzare una conversione.
Il cmdlet caricherà il contenuto della configurazione, otterrà l'elenco dei parametri e genererà un nuovo modulo con tutti gli elementi necessari.

Dopo aver generato un modulo, è possibile incrementare la versione e aggiungere le note sulla versione ogni volta che si apportano modifiche e lo si pubblica nel [repository PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Dopo aver creato un modulo di risorsa composita contenente la configurazione (o più configurazioni), è possibile usarli nell' [esperienza di creazione componibile](/azure/automation/compose-configurationwithcompositeresources) in Azure oppure aggiungerli agli script di [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e [caricare i file MOF in automazione di Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Quindi registrare i server da [locale](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) o [in Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) per eseguire il pull delle configurazioni.
L'aggiornamento più recente del progetto ha pubblicato anche [manuali operativi](https://www.powershellgallery.com/packages?q=DscGallerySamples) per automazione di Azure per automatizzare il processo di importazione delle configurazioni dal PowerShell Gallery.

Per provare ad automatizzare la creazione di risorse composite per DSC, visitare il [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) e scaricare la soluzione oppure fare clic su "progetto sito" per visualizzare la [documentazione](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione della Configuration Manager locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
