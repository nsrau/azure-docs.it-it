---
title: Rendere il set di scalabilità di macchine virtuali disponibili in Azure Stack | Microsoft Docs
description: Informazioni su come un operatore cloud può aggiungere set di scalabilità di macchine virtuali in Azure Stack Marketplace
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: ddde2e6bad8a373df405ac05e78a5dbccd0257fc
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "34800641"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Rendere il set di scalabilità di macchine virtuali disponibili in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure Stack. È possibile utilizzarli per distribuire e gestire un set di macchine virtuali identiche. Con tutte le macchine virtuali configurato allo stesso modo, i set di scalabilità non necessitano di pre-provisioning di macchine virtuali. È più semplice creare servizi su larga scala destinati a big compute, big data e i carichi di lavoro in contenitori.

Questo articolo illustra il processo per rendere disponibili i set di scalabilità in Azure Stack Marketplace. Dopo aver completato questa procedura, gli utenti possono aggiungere set di scalabilità di macchine virtuali per le sottoscrizioni.

Set di scalabilità di macchine virtuali in Azure Stack sono simili a set di scalabilità di macchine virtuali in Azure. Per altre informazioni, vedere i video seguenti:
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich illustra i set di scalabilità di Azure)
* [Set di scalabilità di macchine virtuali con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

In Azure Stack, il set di scalabilità di macchine virtuali non supporta la scalabilità automatica. È possibile aggiungere più istanze per un set di scalabilità tramite PowerShell, CLI o modelli di Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

- **Diffusione di Marketplace**  
    Registrare Azure Stack con Azure globale, per abilitare la diffusione di Marketplace. Seguire le istruzioni in [registrare Azure Stack con Azure](azure-stack-registration.md).
- **Immagine del sistema operativo**  
    Se è stata aggiunta un'immagine del sistema operativo per il Marketplace di Azure Stack, vedere [aggiungere un elemento del marketplace Azure Stack da Azure](asdk/asdk-marketplace-item.md).

## <a name="add-the-virtual-machine-scale-set"></a>Aggiungere il Set di scalabilità di macchine virtuali

1. Aprire il Marketplace di Azure Stack e connettersi ad Azure. Selezionare **gestione Marketplace**> **+ Aggiungi da Azure**.

    ![Gestione di Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Aggiungere e scaricare l'elemento del marketplace Set di scalabilità di macchine virtuali.

    ![Set di scalabilità di macchine virtuali](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aggiornare le immagini in un Set di scalabilità di macchine virtuali

Dopo aver creato un set di scalabilità di macchine virtuali, gli utenti possono aggiornare le immagini in set di scalabilità senza dover essere ricreato di set di scalabilità. Il processo per aggiornare un'immagine dipende gli scenari seguenti:

1. Modello di distribuzione di set di scalabilità di macchine virtuali **specifica più recente** per *versione*:  

   Quando la *versione* viene impostato come **più recente** nel *imageReference* sezione del modello per una scala impostata, scalare le operazioni relative all'utilizzo di set di scalabilità la versione più recente disponibile Imposta le istanze dell'immagine per la scala. Dopo aver completato un scalabilità verticale, è possibile eliminare precedenti istanze di set di scalabilità di macchine virtuali.  (I valori per *server di pubblicazione*, *offrono*, e *sku* restano invariati). 

   Di seguito è riportato un esempio di definizione *più recente*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Prima di aumento delle prestazioni è possibile usare una nuova immagine, è necessario scaricare che la nuova immagine:  

   - Quando l'immagine in Marketplace è una versione più recente rispetto all'immagine del set di scalabilità: scaricare la nuova immagine che sostituisce l'immagine precedente. Dopo la sostituzione dell'immagine, un utente può continuare per aumentare le prestazioni. 

   - Quando la versione dell'immagine nel Marketplace è quello utilizzato per l'immagine del set di scalabilità: Elimina l'immagine è in uso nel set di scalabilità e quindi scaricare la nuova immagine. Durante l'intervallo tra la rimozione dell'immagine originale e il download della nuova immagine, è possibile scalare in verticale. 
      
     Questo processo è obbligatorio per resyndicate immagini che utilizzano il formato di file sparse, introdotto con la versione 1803. 
 

2. Modello di distribuzione di set di scalabilità di macchine virtuali **specificato non è più recente** per *versione* e specifica invece un numero di versione:  

    Se si scarica un'immagine con una versione più recente (che cambia la versione disponibile), il set di scalabilità non è possibile aumentare le prestazioni. È per impostazione predefinita la versione dell'immagine specificata nel modello di set di scalabilità deve essere disponibile.  

Per altre informazioni, vedere [dischi del sistema operativo e immagini](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Rimuovere un Set di scalabilità di macchine virtuali

Per rimuovere una macchina virtuale di elemento della raccolta di set di scalabilità, eseguire il comando PowerShell seguente:

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> L'elemento della raccolta non può essere rimosso immediatamente. Si potrebbe essere necessario aggiornare il portale più volte prima che l'elemento viene illustrato come rimuovere dal Marketplace.

## <a name="next-steps"></a>Passaggi successivi
[Domande frequenti per Azure Stack](azure-stack-faq.md)