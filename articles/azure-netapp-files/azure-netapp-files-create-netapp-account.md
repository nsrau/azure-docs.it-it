---
title: Creare un account di NetApp per accedere ad Azure NetApp Files | Microsoft Docs
description: Descrive come accedere ad Azure NetApp Files e creare un account di NetApp, in modo da poter configurare un pool di capacità e creare un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: bb43a75b6a221c15c8724302797d04c22e04c8d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113640"
---
# <a name="create-a-netapp-account"></a>Creare un account di NetApp
La creazione di un account di NetApp consente di configurare un pool di capacità e successivamente di creare un volume. Usare il pannello di Azure NetApp Files per creare un nuovo account di NetApp.

## <a name="before-you-begin"></a>Prima di iniziare
È necessario aver registrato la sottoscrizione per l'uso del provider di risorse NetApp e della funzionalità di anteprima pubblica.

[Effettuare la registrazione ad Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>Passaggi 

1. Accedere al portale di Azure. 
2. Accedere al pannello di Azure NetApp Files tramite uno dei metodi seguenti:  
   * Cercare **Azure NetApp Files** nella casella di ricerca del portale di Azure.  
   * Fare clic su **Tutti i servizi** nel riquadro di spostamento e quindi filtrare per Azure NetApp Files.  

   È possibile impostare il pannello di Azure NetApp Files come "preferito" facendo clic sull'icona a forma di stella. 

3. Fare clic su **+ Aggiungi** per creare un nuovo account di NetApp.  
   Verrà visualizzata la finestra Nuovo account NetApp.  

4. Specificare le informazioni seguenti per l'account di NetApp: 
   * **Nome account**  
     Specificare un nome univoco per la sottoscrizione.
   * **Sottoscrizione**  
     Selezionare una sottoscrizione tra le sottoscrizioni esistenti.
   * **Gruppo di risorse**   
     Usare un gruppo di risorse esistente oppure crearne uno nuovo.
   * **Posizione**  
     Selezionare l'area in cui si vogliono posizionare l'account e le relative risorse figlio.  

     ![Nuovo account di NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Fare clic su **Create**(Crea).     
   L'account di NetApp creato verrà ora visualizzato nel pannello di Azure NetApp Files. 

## <a name="next-steps"></a>Passaggi successivi  

[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)

