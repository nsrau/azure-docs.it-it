---
title: Gestire Azure Analysis Services | Documentazione Microsoft
description: Informazioni su come gestire un server di Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: d41dc3c05d0d0cd0d9466c43bbebadcffd21e0dd
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017


---
# <a name="manage-analysis-services"></a>Gestire Analysis Services
Dopo aver creato un server Analysis Services in Azure, potrà essere necessario eseguire alcune attività di amministrazione e gestione immediatamente o dopo un intervallo di tempo. Ad esempio, eseguire l'elaborazione per i dati di aggiornamento, controllare quali utenti possono accedere ai modelli nel server o monitorare l'integrità del server. Alcune attività di gestione possono essere eseguite solo nel portale di Azure, altre in SQL Server Management Studio (SSMS) e altre ancora possono essere eseguite in entrambi gli ambienti.

## <a name="azure-portal"></a>Portale di Azure
Nel [portale di Azure](http://portal.azure.com/) è possibile creare ed eliminare i server, monitorare le risorse del server, modificare le dimensioni e gestire chi ha accesso ai server.  Se si verificano problemi, è inoltre possibile inviare una richiesta di supporto.

![Ottenere il nome del server in Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
La connessione al server in Azure è un'attività analoga alla connessione a un'istanza del server all'interno dell'organizzazione. Da SSMS, è possibile eseguire molte delle attività, ad esempio elaborare i dati o creare uno script di elaborazione, gestire i ruoli e usare PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Scaricare e installare SSMS
Per ottenere tutte le funzionalità più recenti e un'esperienza ottimale quando ci si connette al server di Azure Analysis Services, assicurarsi di usare la versione più recente di SSMS. 

[Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Per connettersi con SSMS
 Quando si usa SSMS, prima di connettersi al server la prima volta assicurarsi che il nome utente è incluso nel gruppo di amministratori di Analysis Services. Per altre informazioni, vedere la sezione [Amministratori del server](#server-administrators) più avanti in questo articolo.

1. Prima di connettersi, è necessario ottenere il nome del server. In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
    ![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. In SSMS > **Esplora oggetti** fare clic su **Connetti** > **Analysis Services**.
3. Nella finestra di dialogo **Connetti al server** incollare il nome del server e quindi in **Autenticazione** scegliere uno dei tipi di autenticazione seguenti:
   
    **Autenticazione di Windows** per usare le credenziali di dominio\nome utente e password di Windows.

    **Autenticazione della password Active Directory** per usare un account aziendale. Ad esempio, per la connessione da un computer non incluso nel dominio.

    **Autenticazione universale di Active Directory** per usare [l'autenticazione non interattiva o multi-factor](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Connessione in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Amministratori del server e utenti del database
In Azure Analysis Services ci sono due tipi di utenti, gli amministratori del server e gli utenti del database. Entrambi i tipi di utenti devono essere in Azure Active Directory ed essere specificati dal nome UPN o dall'indirizzo di posta elettronica dell'organizzazione. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Risoluzione dei problemi di connessione
Quando ci si connette tramite SSMS, se si riscontra un problema, potrebbe essere necessario cancellare la cache di accesso. Non viene memorizzato nulla nella cache su disco. Per cancellare la cache, chiudere e riavviare il processo di connessione. 

## <a name="next-steps"></a>Passaggi successivi
Se non è mai stato distribuito un modello tabulare nel nuovo server, questo è il momento migliore. Per altre informazioni, vedere [Distribuire in Azure Analysis Services](analysis-services-deploy.md).

Se un modello è stato distribuito nel server, si è pronti per connettersi a tale server tramite un client o un browser. Per altre informazioni, vedere [Get data from Azure Analysis Services server](analysis-services-connect.md) (Ottenere dati dal server Azure Analysis Services).


