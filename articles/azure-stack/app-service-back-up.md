---
title: Eseguire il backup del servizio App in Azure Stack | Microsoft Docs
description: Indicazioni dettagliate per il backup del servizio App di Azure Stack.
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
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340550"
---
# <a name="back-up-app-service-on-azure-stack"></a>Eseguire il backup del servizio App in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*  

Questo documento fornisce istruzioni su come eseguire il backup del servizio App in Azure Stack.

> [!IMPORTANT]
> Servizio App in Azure Stack non viene eseguito il come parte della [backup di Azure Stack dell'infrastruttura](azure-stack-backup-infrastructure-backup.md). Un operatore di Stack di Azure, è necessario eseguire passaggi per assicurarsi che il servizio App possono essere ripristinato correttamente se necessario.

Servizio App di Azure in Azure Stack include quattro componenti principali da considerare durante la pianificazione per il ripristino di emergenza:
1. L'infrastruttura del provider di risorse; i ruoli del server, piani di lavoro e così via. 
2. I segreti di servizio App
3. L'hosting di App del servizio SQL Server e database di controllo
4. Il servizio App utente carico di lavoro contenuto archiviato nella condivisione file di servizio App   

## <a name="back-up-app-service-secrets"></a>Eseguire il backup dei segreti del servizio App
Quando si ripristina il servizio App da un backup, è necessario fornire le chiavi del servizio App usate per la distribuzione iniziale. Queste informazioni devono essere salvate come servizio App è distribuita correttamente e archiviato in un luogo sicuro. La configurazione dell'infrastruttura del provider di risorse verrà ricreata dal backup durante il ripristino utilizzando i cmdlet di PowerShell il ripristino del servizio App.

Usare il portale di amministrazione per eseguire il backup dei segreti del servizio app seguendo questa procedura: 

1. Accedere al portale di amministrazione di Azure Stack come amministratore del servizio.

2. Passare a **servizio App** -> **segreti**. 

3. Selezionare **Scarica segreti**.

   ![Download dei segreti](./media/app-service-back-up/download-secrets.png)

4. Quando i segreti sono pronti per il download, fare clic su **salvare** e archiviare i segreti di servizio App (**SystemSecrets.JSON**) file in un luogo sicuro. 

   ![Salvare i segreti](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Ripetere questi passaggi ogni volta che i segreti di servizio App vengono ruotati.

## <a name="back-up-the-app-service-databases"></a>Il backup dei database di servizio App
Per ripristinare il servizio App, è necessario il **Appservice_hosting** e **Appservice_metering** backup dei database. È consigliabile usare i piani di manutenzione di SQL Server o Server di Backup di Azure per assicurarsi che questi database vengono sottoposti a backup e salvati in modo sicuro a intervalli regolari. Tuttavia, vengono create qualsiasi metodo per garantire backup regolari SQL può essere utilizzato.

Per eseguire manualmente il backup dei database mentre si è connessi a SQL Server, è possibile usare i comandi PowerShell seguenti:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Se è necessario eseguire il backup dei database di SQL AlwaysOn, seguire [queste istruzioni](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Dopo che tutti i database sono stato eseguito correttamente il, copiare i file con estensione bak in una posizione sicura insieme alle informazioni dei segreti del servizio App.

## <a name="back-up-the-app-service-file-share"></a>Eseguire il backup della condivisione file di servizio App
Servizio App archivi app informazioni sul tenant nella condivisione file. Questo è necessario eseguire il backup a intervalli regolari con i database di servizio App in modo che un minimo possibile di dati vanno persi se è necessario un ripristino. 

Per eseguire il backup della condivisione di file di servizio App contenuta, che è possibile usare il Server di Backup di Azure o un altro metodo per copiare regolarmente la condivisione file di contenuto nel percorso sono state salvate tutte le informazioni di ripristino precedenti. 

Ad esempio, è possibile utilizzare questi passaggi per usare robocopy da una sessione di console di Windows PowerShell (non PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Passaggi successivi
[Ripristino del servizio App in Azure Stack](app-service-recover.md)