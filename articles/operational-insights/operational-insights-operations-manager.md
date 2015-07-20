<properties
   pageTitle="Considerazioni su Operations Manager con Operational Insights"
   description="Se si usa Microsoft Azure Operational Insights con Operations Manager, la configurazione si baserà su una distribuzione degli agenti e dei gruppi di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights per l'analisi."
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
   ms.date="07/02/2015"
   ms.author="banders" />

# Considerazioni su Operations Manager con Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Se si usa Microsoft Azure Operational Insights con Operations Manager, la configurazione si baserà su una distribuzione degli agenti e dei gruppi di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights per l'analisi. Se tuttavia si usano agenti che si connettono direttamente al servizio Web, Operations Manager non è necessario. Prendere in considerazione i problemi seguenti quando si usa Operational Insights con Operations Manager.

Inoltre, è necessario specificare le credenziali RunAs per i carichi di lavoro monitorati da Operations Manager in Operational Insights.

## Funzioni e requisiti software di Operational Insights

Operational Insights è costituito da un servizio Web nel cloud e da agenti che si connettono direttamente al servizio Web o da agenti Operations Manager e gruppi di gestione di Operations Manager che gestiscono i computer nell'ambiente.

Prima di selezionare agenti Operations Manager (per la raccolta dei dati) e gruppi di gestione (per la gestione degli agenti e l'invio dei dati al servizio Operational Insights), assicurarsi di avere compreso i concetti seguenti:

- L'agente Operations Manager viene installato in tutti i server da cui si vogliono raccogliere e analizzare dati.

- Il gruppo di gestione di Operations Manager trasferisce i dati dagli agenti al servizio Web di Operational Insights. Non esegue l'analisi dei dati.

- Il gruppo di gestione di Operations Manager deve avere accesso a Internet per caricare i dati nel servizio Web.

- Per risultati ottimali, non installare il server di gestione di Operations Manager in un computer già configurato come controller di dominio.

- Un agente Operations Manager deve disporre della connettività di rete al gruppo di gestione di Operations Manager per il trasferimento dei dati.

Per raccogliere e analizzare dati, Operational Insights può usare il Servizio integrità di System Center. Operations Manager dipende dal Servizio integrità di System Center. Quando si visualizzano i programmi installati nel server, il software dell'agente System Center Operations Manager risulterà elencato, in particolare in Installazione applicazioni. Non rimuovere questo software, perché Operational Insights dipende da esso. Se si rimuove il software dell'agente Operations Manager, Operational Insights non funzionerà più.

## Coesistenza con Operations Manager

Quando si usa Operations Manager, Operational Insights è supportato con l'agente Operations Manager solo in System Center Operations Manager 2012 R2 o System Center Operations Manager 2012 SP1. Non è supportato con le versioni precedenti di System Center Operations Manager. Poiché l'agente Operations Manager viene usato per raccogliere dati, usa credenziali specifiche (account azione o account RunAs) per supportare alcuni dei carichi di lavoro analizzati, ad esempio SharePoint 2012.

## Operational Insights e SQL Server 2012

Quando si usa Operations Manager, il Servizio integrità di System Center viene eseguito con l'account di sistema locale. Nelle versioni di SQL Server precedenti a SQL Server 2008 R2, l'account di sistema locale è abilitato per impostazione predefinita ed è membro del ruolo del server amministratore di sistema. In SQL Server 2012 l'accesso con l'account di sistema locale non fa parte del ruolo del server amministratore di sistema. Di conseguenza, quando si usa Operational Insights, l'istanza di SQL Server 2012 non può essere monitorata completamente e non tutte le regole possono generare avvisi.

## Connettività Internet e di rete interna

Quando si usano agenti che si connettono direttamente al servizio Web, questi devono poter accedere a Internet per inviare dati al servizio Web e per ricevere informazioni di configurazione aggiornate dal servizio Web.

Quando si usa Operations Manager, il server di gestione deve poter accedere a Internet per inviare dati al servizio Web e per ricevere informazioni di configurazione aggiornate dal servizio Web. Per gli agenti non è tuttavia necessario accedere a Internet. Se gli agenti Operations Manager sono installati in server non connessi a Internet, è possibile usare il servizio Web se sono in grado di comunicare con un server di gestione connesso a Internet.

## Supporto del clustering

L'agente Operations Manager è supportato nei computer che eseguono Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008 configurato come parte di un cluster di failover di Windows. I cluster sono visibili nel portale di Operational Insights. Nella pagina relativa ai server, i cluster sono identificati come TYPE=CLUSTER (diversamente da TYPE=AGENT, che indica la modalità di identificazione dei computer fisici).

Le regole di individuazione e configurazione vengono eseguite nei nodi attivi e passivi del cluster, tuttavia gli avvisi generati nei nodi passivi vengono ignorati. Se un nodo passa da passivo ad attivo, i relativi avvisi vengono visualizzati automaticamente, senza alcun intervento da parte dell'utente.

Alcuni avvisi potrebbero essere generati due volte, a seconda della regola che genera l'avviso. Ad esempio, una regola che rileva un driver non valido esaminando il sistema operativo che genera gli avvisi per il server fisico e per il cluster.

L'analisi della configurazione dei nodi passivi non è supportata.

Operational Insights non supporta il raggruppamento o il collegamento di computer che eseguono Windows Server appartenenti allo stesso cluster di failover.

## Ridimensionamento dell'ambiente di Operational Insights

Quando si pianifica la distribuzione di Operational Insights, specialmente quando si analizza il numero di agenti Operations Manager che dovranno trasferire dati tramite un unico gruppo di gestione, considerare la capacità del server in termini di spazio per i file.

Considerare le variabili seguenti.

- Numero di agenti per gruppo di gestione.

- Dimensioni medie dei dati trasferiti dall'agente al gruppo di gestione ogni giorno. Per impostazione predefinita, ogni agente carica file CAB nel gruppo di gestione due volte al giorno. Le dimensioni dei file CAB dipendono della configurazione del server (ad esempio il numero di motori di SQL Server e il numero di database) e dall'integrità del server (ad esempio il numero di avvisi generati). Nella maggior parte dei casi, le dimensioni dei dati caricati giornalmente sono in genere meno di 100 KB.

- Periodo di archiviazione per la conservazione dei dati nel gruppo di gestione (il valore predefinito è di 5 giorni).

Ad esempio, presupponendo dimensioni di caricamento giornaliere di 100 KB per ogni agente e il periodo di archiviazione predefinito, per il gruppo di gestione sarà necessario lo spazio di archiviazione seguente:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Numero di agenti</b></td>
		<td><b>Spazio stimato necessario per il gruppo di gestione</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2,5 MB (5 agenti x 100 KB di dati/giorno x 5 giorni = 2.500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 agenti x 100 KB di dati/giorno x 5 giorni = 25.000 KB)</td>
    </tr>

    </tbody>
    </table>

## Account RunAs di Operations Manager per Operational Insights

Operational Insights usa l'agente e il gruppo di gestione di Operations Manager per raccogliere e inviare dati al servizio Operational Insights. Operational Insights si basa si Management Pack per i carichi di lavoro per fornire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account di dominio. È necessario fornire informazioni sulle credenziali configurando un account RunAs di Operations Manager.

Le sezioni seguenti illustrano come configurare account RunAs di Operations Manager per i carichi di lavoro seguenti:

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

### Impostare l'account RunAs per SQL Assessment

 Se si usa già il Management Pack di SQL Server, è consigliabile usare l'account RunAs corrispondente.

#### Per configurare l'account RunAs di SQL in Operations Console

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights SQL Assessment Run As Profile**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs Windows che contiene le credenziali necessarie per SQL Server oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE]Il tipo dell'account RunAs deve essere Windows. L'account RunAs deve appartenere anche al gruppo Local Administrators in tutti i server Windows che ospitano istanze di SQL Server.

5. Fare clic su **Save**.

6. Modificare ed eseguire il seguente esempio T-SQL in ciascuna istanza di SQL Server per concedere le autorizzazioni minime richieste dall'account RunAs per eseguire la valutazione SQL. Tuttavia, non è necessario farlo se l'account RunAs fa già parte del ruolo server sysadmin nelle istanze di SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
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

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### Impostare l'account RunAs per Virtual Machine Manager

Verificare che l'account RunAs disponga dei privilegi per le azioni seguenti:

- Per usare il modulo Windows PowerShell di VMM

- Per effettuare una query nel database VMM

- Per amministrare in remoto gli agenti VMM in esecuzione su host di virtualizzazione

Effettuare i passaggi seguenti per impostare l'account quando si connette Operational Insights a Operations Manager.

#### Per impostare credenziali per VMM

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights VMM Run As Account**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs di Windows che include le credenziali necessarie per VMM oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE]Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.


### Impostare l'account RunAs per Lync Server

 L'account RunAs deve essere un membro dei gruppi di sicurezza Administrators locale e Lync RTCUniversalUserAdmins.

#### Per impostare le credenziali per un account di Lync

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights Lync Run As Account**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs Windows che sia membro dei gruppi di sicurezza Administrators locale e RTCUniversalUserAdmins.
	>[AZURE.NOTE]Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.


### Impostare l'account RunAs per SharePoint


#### Per configurare le credenziali per un account di SharePoint

1. In Operations Manager aprire la console Operations e quindi fare clic su **Administration**.

2. In **Run As Configuration** fare clic su **Profiles** e aprire **Operational Insights SharePoint Run As Account**.

3. Nella pagina **Run As Account** fare clic su **Add**.

4. Selezionare un account RunAs di Windows che include le credenziali necessarie per SharePoint oppure fare clic su **New** per crearne uno.
	>[AZURE.NOTE]Il tipo dell'account RunAs deve essere Windows.

5. Fare clic su **Save**.



## Posizioni geografiche

Per analizzare i dati di server in diverse posizioni geografiche, considerare la configurazione di un gruppo di gestione per posizione. Questo approccio può migliorare le prestazioni per il trasferimento dei dati dall'agente al gruppo di gestione.

<!---HONumber=July15_HO2-->