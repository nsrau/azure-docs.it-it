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
ms.date: 10/31/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 55a016a0943885a3aaa636316808939777afb0f8


---
# <a name="manage-analysis-services"></a>Gestire Analysis Services
Dopo aver creato un server Analysis Services in Azure, potrà essere necessario eseguire alcune attività di amministrazione e gestione immediatamente o dopo un intervallo di tempo. Ad esempio, eseguire l'elaborazione per i dati di aggiornamento, controllare quali utenti possono accedere ai modelli nel server o monitorare l'integrità del server. Alcune attività di gestione possono essere eseguite solo nel portale di Azure, altre in SQL Server Management Studio (SSMS) e altre ancora possono essere eseguite in entrambi gli ambienti.

## <a name="azure-portal"></a>Portale di Azure
Nel [portale di Azure](http://portal.azure.com/) è possibile creare ed eliminare i server, monitorare le risorse del server, modificare le dimensioni e gestire chi ha accesso ai server.  Se si verificano problemi, è inoltre possibile inviare una richiesta di supporto.

![Ottenere il nome del server in Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
La connessione al server in Azure è un'attività analoga alla connessione a un'istanza del server all'interno dell'organizzazione. Da SSMS, è possibile eseguire molte delle attività, ad esempio elaborare i dati o creare uno script di elaborazione, gestire i ruoli e usare PowerShell.

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
   
    Nota: se Autenticazione Active Directory non è presente, può essere necessario [abilitare l'autenticazione di Azure Active Directory](#enable-azure-active-directory-authentication) in SSMS.
   
    ![Connessione in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Poiché la gestione del server in Azure tramite SSMS è molto simile alla gestione di un server locale, non verrà illustrata in dettaglio in questo articolo. Tutte le informazioni necessarie sono disponibili in [Gestione di un'istanza di Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) su MSDN.

## <a name="server-administrators"></a>Amministratori del server
È possibile usare **Amministratori di Analysis Services** nel pannello di controllo del server nel portale di Azure o SSMS per gestire gli amministratori del server. Gli amministratori di Analysis Services sono gli amministratori del server di database con diritti per attività di amministrazione di database comuni, ad esempio l'aggiunta e la rimozione di database e la gestione degli utenti. Per impostazione predefinita, l'utente che crea il server nel portale di Azure viene automaticamente aggiunto come amministratore di Analysis Services.

È inoltre utile sapere che:

* Windows Live ID non è un tipo di identità supportato per Azure Analysis Services.  
* Gli amministratori di Analysis Services devono essere utenti validi di Azure Active Directory.
* Se si crea un server di Azure Analysis Services tramite modelli di Azure Resource Manager, gli amministratori di Analysis Services accettano una matrice JSON degli utenti che devono essere aggiunti come amministratori.

Gli amministratori di Analysis Services possono essere diversi dagli amministratori di risorse di Azure, che possono gestire le risorse per le sottoscrizioni di Azure. In questo modo, viene mantenuta la compatibilità con i comportamenti di gestione XMLA e TSML esistenti in Analysis Services e si consente di separare i compiti tra la gestione delle risorse di Azure e la gestione del database di Azure.

Per visualizzare tutti i ruoli e accedere ai tipi della risorsa di Azure Analysis Services, usare il Controllo di accesso (IAM) nel Pannello di controllo.

## <a name="database-users"></a>Utenti database
Gli utenti del database modello di Azure Analysis Services devono trovarsi in Azure Active Directory. I nomi utente specificati per il database modello devono basarsi sull'indirizzo di posta elettronica aziendale o sull'UPN. Questo scenario è diverso dai database modello locali che supportano gli utenti in base ai nomi utente di dominio di Windows.

È possibile aggiungere utenti usando le [assegnazioni di ruolo in Azure Active Directory](../active-directory/role-based-access-control-configure.md) o il [linguaggio di scripting del modello tabulare](https://msdn.microsoft.com/library/mt614797.aspx) in SQL Server Management Studio.

**Script di esempio del linguaggio di scripting del modello tabulare**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

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




<!--HONumber=Nov16_HO3-->


