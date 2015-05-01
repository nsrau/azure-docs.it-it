<properties 
   pageTitle="Account RunAs di Operations Manager per Operational Insights"
   description="Informazioni su come configurare gli account RunAs di Operations Manager usati con Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Account RunAs di Operations Manager per Operational Insights

Microsoft Azure Operational Insights usa l'agente e il gruppo di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights. Operational Insights si basa si Management Pack per i carichi di lavoro per fornire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account di dominio. È necessario fornire informazioni sulle credenziali configurando un account RunAs di Operations Manager.

Le sezioni seguenti illustrano come configurare account RunAs di Operations Manager per i carichi di lavoro seguenti:

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

## Impostare l'account RunAs per SQL Assessment

 Se si usa già il Management Pack di SQL Server, è consigliabile usare l'account RunAs corrispondente.

### Per configurare l'account RunAs di SQL in Operations Console

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights SQL Assessment Run As Profile**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs Windows che contiene le credenziali necessarie per SQL Server oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE] Il tipo dell'account RunAs deve essere Windows. L'account RunAs deve appartenere anche al gruppo Local Administrators in tutti i server Windows che ospitano istanze di SQL Server.

5. Fare clic su **Save**.

6. Modificare ed eseguire il seguente esempio T-SQL in ciascuna istanza di SQL Server per concedere le autorizzazioni minime richieste dall'account RunAs per eseguire la valutazione SQL. Tuttavia, non è necessario farlo se l'account RunAs fa già parte del ruolo server sysadmin nelle istanze di SQL Server.

---
    -- Sostituire <UserName> con il nome utente effettivo usato come account RunAs.
    USE master
    
    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Per configurare l'account RunAs di SQL con Windows PowerShell

Aprire una finestra di PowerShell ed eseguire il seguente script dopo averlo aggiornato con le informazioni:

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Impostare l'account RunAs per Virtual Machine Manager

Verificare che l'account RunAs disponga dei privilegi per le azioni seguenti:

- Per usare il modulo Windows PowerShell di VMM

- Per effettuare una query nel database VMM

- Per amministrare in remoto gli agenti VMM in esecuzione su host di virtualizzazione

Effettuare i passaggi seguenti per impostare l'account quando si connette Operational Insights a Operations Manager.

### Per impostare credenziali per VMM

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights VMM Run As Account**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs di Windows che include le credenziali necessarie per VMM oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE] Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.


## Impostare l'account RunAs per Lync Server

 L'account RunAs deve essere un membro dei gruppi di sicurezza Administrators locale e RTCUniversalUserAdmins.

### Per impostare le credenziali per un account di Lync

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights Lync Run As Profile**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs Windows che sia membro dei gruppi di sicurezza Administrators locale e RTCUniversalUserAdmins. 
	>[AZURE.NOTE] Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.


## Impostare l'account RunAs per SharePoint


### Per configurare le credenziali per un account di SharePoint

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights SharePoint Run As Account**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs di Windows che include le credenziali necessarie per SharePoint oppure fare clic su **New** per crearne uno. 
	>[AZURE.NOTE] Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.



<!--HONumber=52-->