---
title: Effettuare la registrazione per Azure NetApp Files | Microsoft Docs
description: Descrive come registrarsi per l'uso di file di Azure NetApp.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299221"
---
# <a name="register-for-azure-netapp-files"></a>Effettuare la registrazione per Azure NetApp Files

> [!IMPORTANT] 
> Prima di registrare il provider di risorse di Azure i file di NetApp, deve aver ricevuto un messaggio di posta elettronica dal team di file di Azure NetApp per confermare che è stato concesso l'accesso al servizio. 

In questo articolo, informazioni su come registrarsi per file di Azure NetApp, in modo da poter iniziare a usare il servizio.

## <a name="waitlist"></a>Inviare una richiesta di una lista d'attesa per l'accesso al servizio

1. Inviare una richiesta di una lista d'attesa per l'accesso al servizio Azure NetApp file tramite il [pagina di invio di file di Azure NetApp una lista d'attesa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    L'iscrizione a una lista d'attesa non garantisce l'accesso al servizio di controllo immediato. 

2. Attendere un messaggio di conferma ufficiale del team di file di Azure NetApp prima di continuare con altre attività. 

## <a name="resource-provider"></a>Registrare il Provider di risorse di NetApp

Per usare il servizio, è necessario registrare il provider di risorse di Azure per Azure NetApp Files.

> [!NOTE] 
> Sarà in grado di registrare correttamente il Provider di risorse NetApp anche senza viene concesso l'accesso per il servizio. Tuttavia, senza l'autorizzazione di accesso, qualsiasi richiesta di API per creare un account di NetApp o qualsiasi altra risorsa Azure NetApp file o il portale di Azure verrà rifiutato con l'errore seguente:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Dal portale di Azure, fare clic sull'icona di Azure Cloud Shell nell'angolo superiore destro:

      ![Icona di Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se si hanno più sottoscrizioni per l'account Azure, selezionare quella inserita nell'elenco di elementi consentiti per Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. Nella console di Azure Cloud Shell, immettere il comando seguente per verificare che la sottoscrizione sia stata inserita nell'elenco elementi consentiti:
    
        az feature list | grep NetApp

   L'output del comando viene visualizzato come segue:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` è l'ID di sottoscrizione personale.

    Se non è possibile visualizzare il nome della funzionalità `Microsoft.NetApp/ANFGA`, non si ha accesso al servizio. Arrestare in questo passaggio. Seguire le istruzioni [inviare una richiesta di una lista d'attesa per l'accesso al servizio](#waitlist) richiesta di accesso al servizio prima di continuare. 

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