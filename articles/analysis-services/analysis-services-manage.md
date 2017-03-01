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
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: fdc4e495fb3fb99022b0f7c487001b2fba42cccc
ms.lasthandoff: 02/16/2017


---
# <a name="manage-analysis-services"></a>Gestire Analysis Services
Dopo aver creato un server Analysis Services in Azure, potrà essere necessario eseguire alcune attività di amministrazione e gestione immediatamente o dopo un intervallo di tempo. Ad esempio, eseguire l'elaborazione per i dati di aggiornamento, controllare quali utenti possono accedere ai modelli nel server o monitorare l'integrità del server. Alcune attività di gestione possono essere eseguite solo nel portale di Azure, altre in SQL Server Management Studio (SSMS) e altre ancora possono essere eseguite in entrambi gli ambienti.

## <a name="azure-portal"></a>Portale di Azure
Nel [portale di Azure](http://portal.azure.com/) è possibile creare ed eliminare i server, monitorare le risorse del server, modificare le dimensioni e gestire chi ha accesso ai server.  Se si verificano problemi, è inoltre possibile inviare una richiesta di supporto.

![Ottenere il nome del server in Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
La connessione al server in Azure è un'attività analoga alla connessione a un'istanza del server all'interno dell'organizzazione. Da SSMS, è possibile eseguire molte delle attività, ad esempio elaborare i dati o creare uno script di elaborazione, gestire i ruoli e usare PowerShell. [Scaricare e installare SSMS](#download-and-install-ssms).
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Una delle differenze principali è data dal tipo di autenticazione usata per connettersi al server. Per connettersi al server Azure Analysis Services, è necessario selezionare **Autenticazione della password Active Directory**.

 Quando si usa SSMS, prima di connettersi al server la prima volta assicurarsi che il nome utente è incluso nel gruppo di amministratori di Analysis Services. Per altre informazioni, vedere la sezione [Amministratori del server](#server-administrators) più avanti in questo articolo.

### <a name="to-connect-with-ssms"></a>Per connettersi con SSMS
1. Prima di connettersi, è necessario ottenere il nome del server. In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
    ![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. In SSMS > **Esplora oggetti** fare clic su **Connetti** > **Analysis Services**.
3. Nella finestra di dialogo **Connetti al server** incollare il nome del server e quindi in **Autenticazione** scegliere una delle impostazioni seguenti:
   
    **Autenticazione integrata di Active Directory** per usare Single Sign-On con Active Directory nella federazione di Azure Active Directory.
   
    **Autenticazione della password Active Directory** per usare un account aziendale. Ad esempio, per la connessione da un computer non incluso nel dominio.
   
    Nota: se non viene visualizzata la finestra di autenticazione di Active Directory, potrebbe essere necessario eseguire l'aggiornamento alla [versione più recente di SQL Server Management Studio](#download-and-install-ssms).
   
    ![Connessione in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Poiché la gestione del server in Azure tramite SSMS è molto simile alla gestione di un server locale, non verrà illustrata in dettaglio in questo articolo. Tutte le informazioni necessarie sono disponibili in [Gestione di un'istanza di Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) su MSDN.

## <a name="server-administrators-and-database-users"></a>Amministratori del server e utenti del database
In Azure Analysis Services ci sono due tipi di utenti, gli amministratori del server e gli utenti del database. Entrambi i tipi di utenti devono essere in Azure Active Directory ed essere specificati dal nome UPN o dall'indirizzo di posta elettronica dell'organizzazione. Questo scenario è diverso dai database modello tabulare locali che supportano gli amministratori del server e gli utenti del database in base ai nomi utente di dominio di Windows. Per altre informazioni, vedere [Gestire gli utenti in Azure Analysis Services](analysis-services-manage-users.md).

## <a name="download-and-install-ssms"></a>Scaricare e installare SSMS
[Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

Nella pagina di download sono presenti due opzioni:
 
* Versione finale candidata - consigliabile quando si esegue la connessione solo a un'anteprima di Azure Analysis Services. Tale versione ottiene gli aggiornamenti più recenti e funziona meglio con l'anteprima di Azure Analysis Services.
* Versione di produzione corrente - consigliabile quando si esegue la connessione sia ai server di produzione che a un'anteprima di Azure Analysis Services. Quando si usa questa versione, può essere necessario modificare il Registro di sistema per abilitare l'autenticazione di Azure Active Directory.

## <a name="enable-azure-active-directory-authentication"></a>Abilitare l'autenticazione di Azure Active Directory

Per abilitare la funzionalità di autenticazione di Azure Active Directory per SSMS nel Registro di sistema, creare un file di testo denominato EnableAAD.reg, quindi copiare e incollare quanto segue:

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Salvare e quindi eseguire il file.

## <a name="troubleshooting-connection-problems"></a>Risoluzione dei problemi di connessione
Quando ci si connette al server usando SSMS, se nel passaggio 3 si prova ad accedere tramite un account non federato o un account non presente in Azure Active Directory e non è possibile connettersi, potrebbe essere necessario cancellare la cache di accesso. Chiudere SSMS prima di seguire questa procedura.

1. In Esplora file passare a `C:\Users\<user_name>\AppData\Local\`.
2. Eliminare la cartella **AADCacheOM**.
3. Nella cartella **Local** cercare i file .dat che iniziano con il nome **omlibs-token-cache**. Se presenti, eliminarli.
4. Aprire SSMS e ripetere i passaggi indicati in [Per connettersi con SSMS](#to-connect-with-ssms).

## <a name="next-steps"></a>Passaggi successivi
Se non è mai stato distribuito un modello tabulare nel nuovo server, questo è il momento migliore. Per altre informazioni, vedere [Distribuire in Azure Analysis Services](analysis-services-deploy.md).

Se un modello è stato distribuito nel server, si è pronti per connettersi a tale server tramite un client o un browser. Per altre informazioni, vedere [Get data from Azure Analysis Services server](analysis-services-connect.md) (Ottenere dati dal server Azure Analysis Services).


