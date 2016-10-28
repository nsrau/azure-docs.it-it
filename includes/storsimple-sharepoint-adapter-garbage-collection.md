<!--author=SharS last changed: 9/17/15-->

In questa procedura, si apprenderà come:

1. [Prepararsi a eseguire il Maintainer eseguibile](#to-prepare-to-run-the-maintainer).

2. [Preparare il database del contenuto e il Cestino per l'eliminazione immediata dei BLOB orfani](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).

3. [Eseguire Maintainer.exe](#to-run-the-maintainer).

4. [Ripristinare il database del contenuto e le impostazioni del Cestino](#to-revert-the-content-database-and-recycle-bin-settings).

#### Per preparare l'esecuzione di Maintainer

1. Sul server front-end web, aprire la Shell di gestione di SharePoint 2013 come amministratore.

2. Passare alla cartella *unità di avvio*:\\Programmi\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer.

3. Rinominare**Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config**con**web.config**.

4. Utilizzare`aspnet_regiis -pdf connectionStrings`per decrittografare il file web.config.

5. Nel file web.config decrittografato sotto al nodo `connectionStrings`, aggiungere la stringa di connessione per l'istanza del server SQL e il nome del database del contenuto. Vedere l'esempio seguente.

    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=";Remote Blob Storage Maintainer for WSS_Content";" providerName="System.Data.SqlClient" />`

6. Utilizzare`aspnet_regiis –pef connectionStrings`per crittografare nuovamente il file web.config.

7. Rinominare web.config con Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config.

#### Per preparare il database del contenuto e il Cestino per l'eliminazione immediata dei BLOB orfani.

1. In SQL Server in SQL Management Studio, eseguire le query di aggiornamento seguenti per il database del contenuto di destinazione: 

       `use WSS_Content`

       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. Sul server web front-end, in**Amministrazione centrale**modificare **Impostazioni generali applicazione Web**per il database del contenuto desiderato per disabilitare temporaneamente il Cestino. Questa azione svuoterà inoltre il Cestino per le relative raccolte siti. A tale scopo, fare clic su**Amministrazione centrale** -> **Gestione dell’applicazione** -> **Applicazioni Web (Gestisci applicazioni web)** -> **SharePoint - 80** -> **Impostazioni generali dell’applicazione**. Impostare lo**Stato del Cestino**su**OFF**.

    ![Impostazioni generali dell’applicazione Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### Per eseguire Maintainer

- Sul server web front-end, nella Shell di gestione di SharePoint 2013, eseguire Maintainer come segue:

      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`

    >[AZURE.NOTE]Solo l’operazione`GarbageCollection`è supportata per StorSimple in questo momento. Si noti inoltre che i parametri per Microsoft.Data.SqlRemoteBlobs.Maintainer.exe distinguono tra maiuscole e minuscole.
 
#### Per ripristinare il database del contenuto e le impostazioni del Cestino

1. In SQL Server in SQL Management Studio, eseguire le query di aggiornamento seguenti per il database del contenuto di destinazione:

      `use WSS_Content`

      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`

2. Sul server web front-end, in**Amministrazione centrale**modificare le **Impostazioni generali dell’applicazione Web**per il database del contenuto desiderato per abilitare nuovamente il Cestino. A tale scopo, fare clic su**Amministrazione centrale** -> **Gestione dell’applicazione** -> **Applicazioni Web (Gestisci applicazioni web)** -> **SharePoint - 80** -> **Impostazioni generali dell’applicazione**. Impostare lo stato del Cestino su**ON**.

<!---HONumber=AcomDC_0107_2016-->