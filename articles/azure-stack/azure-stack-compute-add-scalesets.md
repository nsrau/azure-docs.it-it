---
title: Scalabilità della macchina virtuale verificare imposta disponibile nello Stack di Azure | Documenti Microsoft
description: Informazioni su come un operatore cloud aggiungere scalabilità della macchina virtuale di Azure Marketplace Stack
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: cdabd2a9d336cdd8ac83d27460fe129c45b7e1c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Rendere il set di scalabilità di macchine virtuali disponibili nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure Stack. È possibile utilizzare tali per distribuire e gestire un set di macchine virtuali identiche. Con tutte le macchine virtuali configurati allo stesso set di scalabilità non richiedono pre-provisioning di macchine virtuali. Risulta più semplice compilare servizi su larga scala destinati a una soluzione big compute, dati e i carichi di lavoro nei contenitori.

In questo articolo in modo semplificato il processo per rendere disponibili i set di scalabilità in Azure Marketplace dello Stack. Dopo aver completato questa procedura, è possibile che gli utenti possono aggiungere scalabilità della macchina virtuale imposta per le sottoscrizioni.

Set di scalabilità di macchine virtuali nello Stack di Azure sono simili a set di scalabilità di macchine virtuali in Azure. Per ulteriori informazioni, vedere i video seguenti:
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich illustra i set di scalabilità di Azure)
* [Set di scalabilità di macchine virtuali con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Nello Stack di Azure, il set di scalabilità di macchine virtuali non supporta la scalabilità automatica. È possibile aggiungere più istanze di una scala impostata utilizzando il portale di Azure Stack, i modelli di gestione risorse o PowerShell.

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

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aggiornare le immagini in un set di scalabilità della macchina virtuale 
Dopo aver creato un set di scalabilità della macchina virtuale, gli utenti possono aggiornare le immagini nella scala dei set senza la scala impostata dover essere ricreato. Il processo di aggiornamento di un'immagine a seconda dei casi nei seguenti scenari:

1. Modello di distribuzione di set di scalabilità della macchina virtuale **specifica più recente** per *versione*:  

   Quando il *versione* viene impostato come **più recente** nel *imageReference* sezione del modello per una scala impostata, applicare la scalabilità verticale operazioni relative all'utilizzo di set di scalabilità della versione disponibile più recente Imposta le istanze dell'immagine per la scala. Una volta completata una scalabilità verticale, è possibile eliminare precedenti istanze set di scalabilità di macchine virtuali.  (I valori per *publisher*, *offrono*, e *sku* rimangono invariati). 

   Ecco un esempio di definizione *più recente*:  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   Prima di scalabilità verticale è possibile utilizzare una nuova immagine, è necessario scaricare la nuova immagine:  

   - Quando l'immagine in Marketplace è una versione più recente rispetto all'immagine nel set di scalabilità: scaricare la nuova immagine che sostituisce l'immagine precedente. Dopo l'immagine viene sostituita, un utente può procedere per la scalabilità verticale. 

   - Quando la versione dell'immagine in Marketplace è lo stesso come l'immagine nel set di scalabilità: eliminare l'immagine che è in uso nel set di scalabilità e quindi scaricare la nuova immagine. Il tempo intercorso tra la rimozione dell'immagine originale e il download dell'immagine nuova, è possibile scalare in verticale. 
      
     Questo processo è obbligatorio per resyndicate immagini che utilizzano il formato di file sparse, introdotto con la versione 1803. 
 

2. Modello di distribuzione di set di scalabilità della macchina virtuale **specificato non è più recente** per *versione* e specifica invece un numero di versione:  

     Se si scarica un'immagine con una versione più recente (che modifica la versione disponibile), il set di scalabilità non è possibile scalare in verticale. È per impostazione predefinita la versione dell'immagine specificata nel modello di set di scala deve essere disponibile.  

Per altre informazioni, vedere [dischi del sistema operativo e immagini](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Rimuovere un set di scalabilità della macchina virtuale

Per rimuovere una macchina virtuale elemento della raccolta di set di scalabilità, eseguire il comando PowerShell seguente:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> L'elemento della raccolta non può essere rimosso immediatamente. Si potrebbe essere necessario aggiornare il portale più volte prima che l'elemento viene mostrato come rimossa dal Marketplace.


## <a name="next-steps"></a>Passaggi successivi
[Domande frequenti per Azure Stack](azure-stack-faq.md)

