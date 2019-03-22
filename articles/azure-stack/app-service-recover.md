---
title: Ripristinare il servizio App in Azure Stack | Microsoft Docs
description: Indicazioni dettagliate per il ripristino di emergenza di servizio App di Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340243"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Ripristino del servizio App in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*  

Questo documento vengono fornite istruzioni sulle azioni da intraprendere per il ripristino di emergenza di servizio App.

Per ripristinare il servizio App in Azure Stack da backup occorre adottare le azioni seguenti:
1.  Ripristinare i database di servizio App
2.  Ripristinare il contenuto della condivisione file server
3.  Ripristinare i servizi e i ruoli del servizio App

Se l'archiviazione di Azure Stack è stato usato per l'archiviazione delle App per le funzioni, è necessario eseguire questa procedura per ripristinare l'App per le funzioni.

## <a name="restore-the-app-service-databases"></a>Ripristinare i database di servizio App
I database di App del servizio SQL Server devono essere ripristinati in un'istanza di SQL Server pronta di produzione. 

Dopo aver [preparazione dell'istanza di SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) per ospitare i database di servizio App, usare la procedura per ripristinare i database dal backup:

1. Accedere a SQL Server che ospiterà i database ripristinati di servizio App con le autorizzazioni di amministratore.
2. Usare i comandi seguenti per ripristinare i database di servizio App da un prompt dei comandi con autorizzazioni di amministratore:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Verificare che entrambi i database di servizio App sono stati ripristinati correttamente e uscire da SQL Server Management Studio.

> [!NOTE]
> Per il ripristino da un errore di istanza, cluster di failover [queste istruzioni](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Ripristinare il contenuto della condivisione file del servizio App
Dopo aver [preparazione del server di file](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) per ospitare la condivisione di file di servizio App, è necessario ripristinare il contenuto della condivisione file tenant da un backup. È possibile usare qualsiasi metodo disposizione copiare i file nel percorso della condivisione file del servizio App appena creato. Esecuzione di questo esempio nel file server userà PowerShell e robocopy per connettersi a una condivisione remota e copiare i file nella condivisione:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Oltre a copiare il contenuto di condivisione file, è anche necessario reimpostare le autorizzazioni della condivisione di file stesso. A tale scopo, aprire un prompt dei comandi amministrativo nel computer del server di file ed eseguire la **ReACL.cmd** file. Il **ReACL.cmd** file si trova nel file di installazione del servizio App nel **BCDR** directory.

## <a name="restore-app-service-roles-and-services"></a>Ripristinare i servizi e i ruoli del servizio App
Dopo che sono stati ripristinati i database di servizio App e il contenuto della condivisione file, è necessario quindi usare PowerShell per ripristinare i ruoli del servizio App e servizi. Questi passaggi ripristinerà i segreti del servizio App e le configurazioni del servizio.  

1. Accedere al controller del servizio App **CN0-VM** macchina virtuale come **roleadmin** usando la password specificata durante l'installazione del servizio App. 
    > [!TIP]
    > È necessario modificare gruppo di sicurezza di rete della macchina virtuale per consentire le connessioni RDP. 
2. Copia il **SystemSecrets.JSON** file in locale per la macchina virtuale del controller. È necessario specificare il percorso di questo file come il `$pathToExportedSecretFile` parametro nel passaggio successivo. 
3. Usare i comandi seguenti in una finestra di console di PowerShell con privilegi elevata per ripristinare i servizi e i ruoli del servizio App:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Si consiglia di chiudere la sessione di PowerShell al completamento del comando.

## <a name="restore-function-apps"></a>Ripristinare l'App per le funzioni 
Servizio app di Azure Stack non supporta il ripristino di dati diverso dal contenuto della condivisione file o le applicazioni degli utenti tenant. Di conseguenza, questi deve essere eseguito il backup e ripristinati di fuori di servizio App di eseguire il backup e le operazioni di ripristino. Se l'archiviazione di Azure Stack è stato usato per l'archiviazione delle App per le funzioni, la procedura seguente deve essere utilizzata per recuperare dati persi:

1. Creare un nuovo account di archiviazione da usare per l'App per le funzioni. Questa risorsa di archiviazione può essere archiviazione di Azure Stack, archiviazione di Azure o qualsiasi archiviazione compatibile.
2. Recuperare la stringa di connessione per lo spazio di archiviazione.
3. Aprire il portale di funzioni, selezionare l'app per le funzioni.
4. Selezionare il **funzionalità della piattaforma** scheda e fare clic su **le impostazioni dell'applicazione**.
5. Change **AzureWebJobsDashboard** e **AzureWebJobsStorage** per la nuova stringa di connessione e fare clic su **Salva**.
6. Passare a **Panoramica**.
7. Riavviare l'app. Potrebbero essere necessari diversi tentativi per cancellare tutti gli errori.

## <a name="next-steps"></a>Passaggi successivi
[Servizio app nella panoramica di Azure Stack](azure-stack-app-service-overview.md)