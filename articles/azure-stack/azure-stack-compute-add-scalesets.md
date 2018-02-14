---
title: "Rendere il set di scalabilità di macchine virtuali disponibili nello Stack di Azure"
description: "Informazioni su come un amministratore del cloud aggiungere scalabilità della macchina virtuale di Azure Marketplace Stack"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Rendere il set di scalabilità di macchine virtuali disponibili nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure Stack. È possibile utilizzare tali per distribuire e gestire un set di macchine virtuali identiche. Con tutte le macchine virtuali configurati allo stesso set di scalabilità non richiedono pre-provisioning di macchine virtuali. Risulta più semplice compilare servizi su larga scala destinati a una soluzione big compute, dati e i carichi di lavoro nei contenitori.

Questo argomento descrive il processo per rendere disponibili i set di scalabilità in Azure Marketplace dello Stack. Dopo aver completato questa procedura, è possibile che gli utenti possono aggiungere scalabilità della macchina virtuale imposta per le sottoscrizioni.

Set di scalabilità di macchine virtuali nello Stack di Azure sono simili a set di scalabilità di macchine virtuali in Azure. Per ulteriori informazioni, vedere i video seguenti:
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich illustra i set di scalabilità di Azure)
* [Set di scalabilità di macchine virtuali con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Nello Stack di Azure, il set di scalabilità di macchina virtuale non supportano la scalabilità automatica. È possibile aggiungere più istanze di una scala impostata utilizzando il portale di Azure Stack, i modelli di gestione risorse o PowerShell.

## <a name="prerequisites"></a>Prerequisiti
* **PowerShell e strumenti**

   Installare e PowerShell configurato per lo Stack di Azure e gli strumenti di Azure Stack. Vedere [diventare operativi con PowerShell nello Stack di Azure](azure-stack-powershell-configure-quickstart.md).

   Dopo aver installato gli strumenti di Azure Stack, accertarsi di aver importato il modulo di PowerShell seguente (percorso relativo ai. \ComputeAdmin cartella nella cartella AzureStack-strumenti-master):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Immagine del sistema operativo**

   Se è stato aggiunto un'immagine del sistema operativo a Stack Azure Marketplace, vedere [aggiungere l'immagine di macchina virtuale di Windows Server 2016 nel Marketplace Azure Stack](azure-stack-add-default-image.md).

   Per il supporto di Linux, scaricare Ubuntu Server 16.04 e aggiungerlo mediante ```Add-AzsVMImage``` con i seguenti parametri: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Aggiungere il set di scalabilità della macchina virtuale

Modificare lo script di PowerShell seguente per l'ambiente e quindi eseguire il codice per aggiungere un scalabilità della macchina virtuale impostato su Stack Azure Marketplace. 

``$User`` è l'account usato per connettere il portale dell'amministratore. Ad esempio, serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Rimuovere un set di scalabilità della macchina virtuale

Per rimuovere una macchina virtuale elemento della raccolta di set di scalabilità, eseguire il comando PowerShell seguente:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> L'elemento della raccolta non può essere rimosso immediatamente. Si potrebbe essere necessario aggiornare il portale più volte prima che venga rimossa dal Marketplace.


## <a name="next-steps"></a>Passaggi successivi
[Domande frequenti per Azure Stack](azure-stack-faq.md)

