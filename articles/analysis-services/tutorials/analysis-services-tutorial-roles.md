---
title: 'Esercitazione: Configurare i ruoli di Azure Analysis Services | Microsoft Docs'
description: Informazioni su come configurare i ruoli utente e amministratore di Azure Analysis Services con il portale di Azure oppure SQL Server Management Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 3abcfe61f365c3c96dfb6b8eb2ca6cc9e5d3797e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572368"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Esercitazione: Configurare i ruoli amministratore e utente del server

 In questa esercitazione si usa SQL Server Management Studio (SSMS) per connettersi al server in Azure per configurare i ruoli del database modello e di amministratore del server. Viene anche presentato [Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL è un linguaggio di scripting basato su JSON per i modelli tabulari ai livelli di compatibilità 1200 e superiori. Può essere usato per automatizzare molte attività di modellazione tabulare. TMSL viene spesso usato con PowerShell, ma in questa esercitazione si usa l'editor di query XMLA in SSMS. Con questa esercitazione, si completano le attività seguenti: 
  
> [!div class="checklist"]
> * Ottenere il nome del server dal portale
> * Connettersi al server usando SSMS
> * Aggiungere un utente o un gruppo al ruolo di amministratore del server 
> * Aggiungere un utente o un gruppo al ruolo di amministratore del database modello
> * Aggiungere un nuovo ruolo del database modello e aggiungere un utente o un gruppo

Per altre informazioni sulla sicurezza utente in Azure Analysis Services, vedere [Autenticazione e autorizzazioni utente](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Prerequisiti

- Azure Active Directory nella sottoscrizione.
- Avere creato un [server di Azure Analysis Services](../analysis-services-create-server.md) nella sottoscrizione.
- Avere autorizzazioni di [amministratore del server](../analysis-services-server-admins.md).
- [Aggiungere il modello di esempio adventureworks](../analysis-services-create-sample-model.md) al server.
- [Installare la versione più recente di SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/).

## <a name="get-server-name"></a>Ottenere il nome del server
Per connettersi al server da SSMS, è necessario prima di tutto il nome del server. È possibile ottenere il nome del server dal portale.

In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
   ![Ottenere il nome del server in Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Connessione in SSMS

Per le restanti attività, si usa SSMS per connettersi al server e gestirlo.

1. In SSMS > **Esplora oggetti** fare clic su **Connetti** > **Analysis Services**.

    ![Connettere](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. Nella finestra di dialogo **Connetti a server** incollare il nome del server copiato dal portale in **Nome del server**. In **Autenticazione** scegliere **Active Directory - Universale con supporto MFA**, quindi immettere l'account utente e infine fare clic su **Connetti**.
   
    ![Connessione in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > È consigliabile scegliere Active Directory - Universale con supporto MFA. Questo tipo di autenticazione supporta l'[autenticazione a più fattori e non interattiva](../../sql-database/sql-database-ssms-mfa-authentication.md). 

3. In **Esplora oggetti** espandere per visualizzare gli oggetti del server. Fare clic con il pulsante destro del mouse per visualizzare le proprietà del server.
   
    ![Connessione in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Aggiungere un account utente al ruolo di amministratore del server

In questa attività si aggiunge un account utente o gruppo da Azure AD al ruolo di amministratore del server. Se si aggiunge un gruppo di sicurezza, la proprietà `MailEnabled` di questo gruppo di sicurezza deve essere impostata su `True`.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul nome del server e quindi scegliere **Proprietà**. 
2. Nella finestra **Proprietà computer Analysis Server** fare clic su **Sicurezza** > **Aggiungi**.
3. Nella finestra **Seleziona un utente o un gruppo** immettere un account utente o gruppo in Azure AD e quindi fare clic su **Aggiungi**. 
   
     ![Aggiungere l'amministratore del server](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Fare clic su **OK** per chiudere **Proprietà computer Analysis Server**.

    > [!TIP]
    > È anche possibile aggiungere gli amministratori del server usando **Amministratori di Analysis Services** nel portale. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Aggiungere un utente al ruolo di amministratore del database modello

In questa attività si aggiunge un account utente o gruppo al ruolo Internet Sales Administrator già esistente nel modello. Questo ruolo ha le autorizzazioni Controllo completo (amministratore) per il database modello di esempio adventureworks. Questa attività usa il comando TMSL [CreateOrReplace](https://docs.microsoft.com/bi-reference/tmsl/createorreplace-command-tmsl) in uno script creato automaticamente.

1. In **Esplora oggetti** espandere **Database** > **adventureworks** > **Ruoli**. 
2. Fare clic con il pulsante destro del mouse su **Internet Sales Administrator**, quindi scegliere **Crea script per ruolo** > **CREATE OR REPLACE per** > **Nuova finestra editor di query**.

    ![Nuova finestra editor di query](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. In **XMLAQuery** sostituire il valore di **"memberName":** con un account utente o gruppo in Azure AD. Per impostazione predefinita, l'account usato per l'accesso è incluso. Non è tuttavia necessario aggiungere il proprio account perché si è già un amministratore del server.

    ![Script TMSL nella query XMLA](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Premere **F5** per eseguire lo script.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Aggiungere un nuovo ruolo del database modello e aggiungere un utente o un gruppo

In questa attività si usa il comando [Create](https://docs.microsoft.com/bi-reference/tmsl/create-command-tmsl) in uno script TMSL per creare un nuovo ruolo Internet Sales Global, specificare le autorizzazioni di *lettura* per il ruolo e aggiungere un account utente o gruppo da Azure AD.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **adventureworks** e quindi scegliere **Nuova query** > **XMLA**. 
2. Copiare e incollare lo script TMSL seguente nell'editor di query:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Sostituire il valore dell'oggetto `"memberName": "globalsales@adventureworks.com"` con un account utente o gruppo di Azure AD.
4. Premere **F5** per eseguire lo script.

## <a name="verify-your-changes"></a>Verificare le modifiche

1. In **Esplora oggetti** fare clic sul nome del server e quindi fare clic su **Aggiorna** o premere **F5**.
2. Espandere **Database** > **adventureworks** > **Ruoli**. Verificare che vengano visualizzate le modifiche all'account utente e al nuovo ruolo aggiunte nelle attività precedenti.   

    ![Verificare in Esplora oggetti](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare gli account utente o gruppo e i ruoli. A questo scopo, usare **Proprietà ruolo** > **Appartenenza** per rimuovere gli account utente o fare clic con il pulsante destro del mouse su un ruolo e quindi scegliere **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come connettersi al server di Azure AS ed esplorare i database modello di esempio adventureworks e le proprietà in SSMS. È stato anche illustrato come usare gli script SSMS e TMSL per aggiungere utenti o gruppi ai ruoli nuovi ed esistenti. Ora che le autorizzazioni utente sono state configurate per il server e il database modello di esempio, tutti gli utenti possono connettersi usando applicazioni client come Power BI. Per altre informazioni, continuare con l'esercitazione successiva. 

> [!div class="nextstepaction"]
> [Esercitazione: Connettersi con Power BI Desktop](analysis-services-tutorial-pbid.md)

