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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010345"
---
# <a name="create-a-netapp-account"></a>Creare un account di NetApp
La creazione di un account di NetApp consente di configurare un pool di capacità e successivamente di creare un volume. Usare il pannello di Azure NetApp Files per creare un nuovo account di NetApp.

## <a name="before-you-begin"></a>Prima di iniziare
È necessario essere inclusi nell'elenco elementi consentiti per accedere al provider di risorse di Azure Microsoft.NetApp ed essere configurati per l'uso del servizio Azure NetApp Files.  

[Pagina di iscrizione all'anteprima pubblica di Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="steps"></a>Passaggi 

1. Individuare l'URL del portale di Azure dell'anteprima dall'invito a partecipare all'anteprima ed eseguire l'accesso al portale. 
2.  Accedere al pannello di Azure NetApp Files tramite uno dei metodi seguenti:  
  * Cercare **Azure NetApp Files** nella casella di ricerca del portale di Azure.  
  * Fare clic su **Tutti i servizi** nel riquadro di spostamento e quindi filtrare per Azure NetApp Files.  

  È possibile impostare il pannello di Azure NetApp Files come "preferito" facendo clic sull'icona a forma di stella. 

3. Fare clic su **+ Aggiungi** per creare un nuovo account di NetApp.  
  Verrà visualizzata la finestra Nuovo account NetApp.  

4. Specificare le informazioni seguenti per l'account di NetApp: 
  * **Nome account**  
    Specificare un nome univoco per la sottoscrizione.
  *  **Sottoscrizione**  
    Selezionare una sottoscrizione tra le sottoscrizioni esistenti.
  * **Gruppo di risorse**   
    Usare un gruppo di risorse esistente oppure crearne uno nuovo.
  * **Posizione**  
    Selezionare l'area in cui si vogliono posizionare l'account e le relative risorse figlio.  
    Attualmente, il servizio Azure NetApp Files è supportato solo nell'area Stati Uniti orientali.  

    ![Nuovo account di NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Fare clic su **Crea**.     
  L'account di NetApp creato verrà ora visualizzato nel pannello di Azure NetApp Files. 

## <a name="next-steps"></a>Passaggi successivi  

1. [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
2. [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
3. [Configurare i criteri di esportazione per un volume (facoltativo)](azure-netapp-files-configure-export-policy.md)
