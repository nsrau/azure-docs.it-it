<properties
	pageTitle="Modificare il livello di servizio e il livello delle prestazioni di un database SQL di Azure"
	description="La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso. Modificare il livello di prezzo di un database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


I livelli di servizio e di prestazioni descrivono le funzionalità e le risorse disponibili per il database SQL e possono essere aggiornati quando le esigenze dell’applicazione cambiano. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md).

Si noti che la modifica del livello di servizio e/o livello di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Molto raramente, soprattutto se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.


Utilizzare le informazioni contenute negli argomenti [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

- Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-overview.md) abilitata, è necessario aggiornare i database secondari al livello di prestazioni previsto prima di aggiornare il database primario.
- Quando si esegue il downgrade da un livello di servizio, è necessario prima terminare tutte le relazioni di replica geografica. 
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](sql-database-business-continuity.md).
- La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.



**Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:**

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- un database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).


## Modificare il livello di servizio e il livello delle prestazioni del database


Aprire il pannello del Database SQL per il database che si desidera scalare verso l’alto o verso il basso:

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **ESPLORA TUTTO**.
3.	Fare clic su **Database SQL**.
2.	Fare clic sul database che si desidera modificare.
3.	Nel pannello del database SQL selezionare **Tutte le impostazioni** e quindi **Piano tariffario (piano DTU)**.

    ![piano tariffario][1]


1.  Selezionare un nuovo livello e fare clic su **Seleziona**:

    Facendo clic su **Seleziona** si invia una richiesta di scalabilità per modificare il livello del database. A seconda delle dimensioni del database, l'operazione di ridimensionamento potrebbe richiedere diverso tempo. Fare clic sulla notifica per i dettagli e lo stato dell'operazione di ridimensionamento.

    > [AZURE.NOTE] La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![selezionare un livello di prezzo][2]

3.	Nella barra multifunzione di sinistra fare clic su **Notifiche**:

    ![notifiche][3]

## Verificare che il database sia sul livello di prezzo selezionato

   Dopo aver completato l'operazione di ridimensionamento controllare e confermare che il database è sul livello desiderato:

2.	Fare clic su **ESPLORA TUTTO**.
3.	Fare clic su **Database SQL**.
2.	Fare clic sul database aggiornato.
3.	Controllare il **piano tariffario** e verificare che sia impostato il piano corretto.

    ![nuovo prezzo][4]


## Passaggi successivi

- Modificare le dimensioni massime del database tramite [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Scalare orizzontalmente e verticalmente](sql-database-elastic-scale-get-started.md)
- [Connettersi al database SQL con SSMS ed eseguire query](sql-database-connect-query-ssms.md)
- [Esportare un database SQL di Azure](sql-database-export.md)

## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0608_2016-->