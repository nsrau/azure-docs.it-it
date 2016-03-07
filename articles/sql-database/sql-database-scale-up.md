<properties
	pageTitle="Modificare il livello di servizio e il livello delle prestazioni di un database SQL di Azure"
	description="La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso. Modificare il livello di prezzo di un database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL

**Database singolo**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

In questo articolo viene illustrato come modificare il livello del servizio e il livello delle prestazioni del database SQL con il [portale di Azure](https://portal.azure.com).

Utilizzare le informazioni contenute negli argomenti [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

> [AZURE.IMPORTANT] La modifica del livello di servizio e del livello delle prestazioni di un database SQL è un'operazione in linea. Ciò significa che il database rimane online e disponibile durante l'intera operazione senza tempi di inattività.

- Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
- Quando si aggiorna un database con la [replica geografica standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) o la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
- Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Terminare una relazione di copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) per arrestare il processo di replica tra il database primario e i database secondari attivi.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- È possibile apportare fino a quattro modifiche di singoli database (livello di servizio o di prestazioni) nell'arco di un periodo di 24 ore.
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

<!---HONumber=AcomDC_0224_2016-->