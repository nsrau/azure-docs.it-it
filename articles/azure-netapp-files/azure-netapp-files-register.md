---
title: Effettuare la registrazione per Azure NetApp Files | Microsoft Docs
description: Viene descritto come inviare una richiesta di registrazione nel servizio Azure NetApp Files.
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122336"
---
# <a name="register-for-azure-netapp-files"></a>Effettuare la registrazione per Azure NetApp Files
Prima di usare Azure NetApp Files, è necessario inviare una richiesta di registrazione nel servizio Azure NetApp Files.  Dopo la registrazione, ci si registra per usare il servizio.

## <a name="request-to-enroll-in-the-service"></a>Richiesta di registrazione nel servizio
È necessario far parte del programma di anteprima pubblica ed essere nell'elenco degli elementi consentiti per l'accesso al provider di risorse Microsoft.NetApp di Azure. Per informazioni dettagliate sulla partecipazione al programma di anteprima pubblica, vedere la [pagina di iscrizione all'anteprima pubblica di Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Effettuare la registrazione del provider di risorse NetApp

Per usare il servizio, è necessario registrare il provider di risorse di Azure per Azure NetApp Files. 

1. Dal portale di Azure, fare clic sull'icona di Azure Cloud Shell nell'angolo superiore destro:

      ![Icona di Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se si hanno più sottoscrizioni per l'account Azure, selezionare quella inserita nell'elenco di elementi consentiti per Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. Nella console di Azure Cloud Shell, immettere il comando seguente per verificare che la sottoscrizione sia stata inserita nell'elenco elementi consentiti:
    
        az feature list | grep NetApp

   L'output del comando viene visualizzato come segue:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` è l'ID di sottoscrizione personale.

4. Nella console di Azure Cloud Shell, immettere il comando seguente per registrare il provider di risorse di Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Il parametro `--wait` indica alla console di attendere il completamento della registrazione. Il completamento del processo di registrazione può richiedere del tempo.

5. Nella console di Azure Cloud Shell, immettere il comando seguente per verificare la registrazione del provider di risorse di Azure: 
    
        az provider show --namespace Microsoft.NetApp

   L'output del comando viene visualizzato come segue:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` è l'ID di sottoscrizione personale.  Il valore del parametro `state` indica `Registered`.

6. Dal portale di Azure,fare clic sul pannello **Sottoscrizioni**.
7. Nel pannello Sottoscrizioni, fare clic sul proprio ID di sottoscrizione. 
8. Nelle impostazioni della sottoscrizione, fare clic su **Provider di risorse** per verificare che il provider di Microsoft.NetApp indichi lo stato registrato: 

      ![Microsoft.NetApp registrato](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Passaggi successivi  

[Creare un account di NetApp](azure-netapp-files-create-netapp-account.md)