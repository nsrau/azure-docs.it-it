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
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012576"
---
# <a name="create-a-netapp-account"></a>Creare un account di NetApp
La creazione di un account di NetApp consente di configurare un pool di capacità e successivamente di creare un volume. Usare il pannello di Azure NetApp Files per creare un nuovo account di NetApp.

## <a name="before-you-begin"></a>Prima di iniziare
È necessario aver ricevuto un messaggio di posta elettronica dal team dei file NetApp di Azure che conferma che è stato concesso l'accesso al servizio. Vedere Inviare una richiesta di [lista d'attesa per l'accesso al servizio](azure-netapp-files-register.md#waitlist).

È inoltre necessario aver registrato la sottoscrizione per l'utilizzo del provider di risorse NetApp.You must also have registered your subscription for using the NetApp Resource Provider. Vedere [Registrare il provider di risorse NetApp](azure-netapp-files-register.md#resource-provider).

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
   * **Percorso**  
     Selezionare l'area in cui si vogliono posizionare l'account e le relative risorse figlio.  

     ![Nuovo account di NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Fare clic su **Crea**.     
   L'account di NetApp creato verrà ora visualizzato nel pannello di Azure NetApp Files. 

> [!NOTE] 
> Se non è stato concesso l'accesso al servizio File NetApp di Azure, verrà visualizzato il seguente errore quando si tenta di creare il primo account NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Passaggi successivi  

[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)

