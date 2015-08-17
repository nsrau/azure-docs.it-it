>[AZURE.NOTE]Quando si apportano modifiche all'adattatore StorSimple per la configurazione RBS di SharePoint, è necessario essere connessi con un account utente che appartiene al gruppo Domain Admins. Inoltre, è necessario accedere alla pagina configurazione da un browser in esecuzione nello stesso host di amministrazione centrale.

#### Per configurare RBS

1. Aprire la pagina Amministrazione centrale SharePoint e passare a**Impostazioni di sistema**. 

2. Nella sezione**Azure StorSimple**fare clic su**Configura l'adattatore StorSimple**.

    ![Configurare l'adattatore StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png)

3. Nella pagina**Configura l'adattatore StorSimple**:

    1. Assicurarsi che la casella di controllo**Abilita modifica percorso**sia selezionata.

    2. Nella casella di testo, digitare il percorso Universal Naming Convention (UNC) dell'archivio BLOB.

          >[AZURE.NOTE]Il volume dell'archivio BLOB deve essere ospitato in un volume iSCSI configurato sul dispositivo StorSimple.

    3. Fare clic sul pulsante**Abilita**sotto ciascuno dei database di contenuto che si desidera configurare per l'archiviazione remota.

          >[AZURE.NOTE]L'archivio BLOB deve essere condiviso e raggiungibile da tutti i server front-end web (WFE) e l'account utente configurato per la server farm di SharePoint deve avere accesso alla condivisione.

          ![Abilitare il provider RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)

           Quando si abilita o disabilita RBS, inoltre, verrà visualizzato il messaggio seguente.

          ![Configurare l’adattatore StorSimple Adapter Attivazione Disattivazione](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

    4. Fare clic sul pulsante**Aggiorna**per applicare la configurazione. Quando si fa clic sul pulsante **Aggiorna**, lo stato della configurazione RBS verrà aggiornato in tutti i server front-end web e l'intera farm risulterà abilitata per RBS. Viene visualizzato il messaggio seguente.

           ![Messaggio di configurazione dell’adattatore](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)

           >[AZURE.NOTE]Se si configura RBS per una farm di SharePoint che dispone di un numero molto elevato di database (più di 200), la pagina di amministrazione centrale SharePoint potrebbe scadere. In questo caso, aggiornare la pagina. Questo evento non influisce sul processo di configurazione.
 
4. Verificare la configurazione:

    1. Accedere al sito Web Amministrazione centrale SharePoint e individuare la pagina **Configura l'adattatore StorSimple**.

    2. Controllare i dettagli di configurazione per assicurarsi che corrispondano alle impostazioni immesse.

5. Verificare che RBS funzioni correttamente:

    1. Caricare un documento in SharePoint. 

    2. Selezionare il percorso UNC configurato. Assicurarsi che la struttura della directory RBS sia stata creata e che contenga l'oggetto caricato.

6. (Facoltativo) È possibile utilizzare Microsoft RBS `Migrate()` cmdlet PowerShell incluso in SharePoint per la migrazione del contenuto BLOB esistente nel dispositivo StorSimple. Per ulteriori informazioni, vedere [Migrazione del contenuto in o da RBS in SharePoint 2013][6] o [Migrazione del contenuto in o da RBS (SharePoint Foundation 2010)][7].

7. (Facoltativo) Nelle installazioni di prova, è possibile verificare che i BLOB siano stati spostati all'esterno del database del contenuto come segue:

    1. Avviare SQL Management Studio.

    2. Eseguire la query ListBlobsInDB\_2010.sql o ListBlobsInDB\_2013.sql, come indicato di seguito.

     **ListBlobsInDB\_2013.sql**

         USE WSS_Content
         GO
    
         SELECT DocStreams.DocId,
                LeafName AS Name,
                Content,
                AllDocs.Size AS OrigSizeOfContent,
                LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
                DocStreams.RbsId,
                TimeLastModified
    
         FROM DocStreams
              INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
         ORDER BY TimeLastModified DESC
         GO

     **ListBlobsInDB\_2010.sql**

         USE WSS_Content
         GO

         SELECT AllDocStreams.Id,
                LeafName AS Name,
                Content,
                AllDocs.Size AS OrigSizeOfContent,
                LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
                RbsId,
                TimeLastModified
         FROM AllDocStreams
              INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
         ORDER BY TimeLastModified DESC
         GO

     Se RBS è stato configurato correttamente, verrà visualizzato un valore NULL nella colonna SizeOfContentInDB per qualsiasi oggetto caricato e quindi esternalizzato con RBS.

8. (Facoltativo) Dopo aver configurato RBS e spostato tutto il contenuto BLOB nel dispositivo StorSimple, è possibile spostare il database del contenuto nel dispositivo. Se si sceglie di spostare il database del contenuto, è consigliabile configurare l'archiviazione del database del contenuto sul dispositivo come volume principale. Utilizzare le tradizionali procedure consigliate per la migrazione di SQL Server per spostare il database del contenuto nel dispositivo StorSimple.

     >[AZURE.NOTE]Lo spostamento del database del contenuto nel dispositivo è supportato solo per il dispositivo StorSimple serie 8000, (non è supportato per la serie 5000 e quella 7000).
 
     Se si archiviano i BLOB e il database del contenuto in volumi separati nel dispositivo StorSimple, si consiglia di configurarli nello stesso contenitore di volume. Ciò garantisce che essi saranno sottoposti insieme al backup.

       >[AZURE.WARNING]Se RBS non è abilitato, non è consigliabile spostare il database del contenuto nel dispositivo StorSimple. Si tratta di una configurazione non testata.
 
9. Andare al passaggio successivo: [Configurare garbage collection](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx

<!---HONumber=August15_HO6-->