<properties 
	pageTitle="Novità della versione 12 del database SQL | Microsoft Azure" 
	description="Descrive i motivi per cui i sistemi aziendali che usano il database SQL di Azure nel cloud otterranno vantaggi dall'aggiornamento alla versione 12." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="genemi"/>


# Novità della versione 12 del database SQL


Questo argomento descrive i molti vantaggi offerti dalla versione 12 del database SQL di Azure rispetto alla versione 11.


Altre funzionalità continueranno a essere aggiunte alla versione 12. È quindi consigliabile visitare la pagina Web Aggiornamenti del servizio di Azure e usare i filtri disponibili:


- Filtrato per [servizio Database SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrato per Disponibilità generale[annunci](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) per le funzionalità del database SQL.


Le informazioni più recenti sui limiti delle risorse per il Database SQL sono documentate in:<br/>[limiti delle risorse di Database SQL Azure](sql-database-resource-limits.md).


## Miglioramento della compatibilità delle applicazioni con SQL Server


Uno degli obiettivi principali della versione 12 del database SQL è stato il miglioramento della compatibilità con Microsoft SQL Server 2014. Tra le altre aree, la versione 12 raggiunge la parità con SQL Server nell'importante ambito della programmabilità. Ad esempio:


- [Funzioni finestra](http://msdn.microsoft.com/library/bb934097.aspx) con [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 
- [Indici XML](http://msdn.microsoft.com/library/bb934097.aspx) e [indici XML selettivi](http://msdn.microsoft.com/library/jj670104.aspx)
- [Rilevamento modifiche](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [Ricerca full-text](http://msdn.microsoft.com/library/ms142571.aspx)


Per informazioni sul piccolo set di funzionalità non ancora supportate nel database SQL, fare clic [qui](sql-database-transact-sql-information.md).


## Più prestazioni Premium, nuovi livelli di prestazioni


Nella versione 12, le unità di prestazioni di database espresse in DTU (Database Transaction Unit) allocate a tutti i livelli di prestazioni sono state aumentate del 25% senza costi aggiuntivi. Con le nuove funzionalità, come quelle elencate di seguito, si possono ottenere miglioramenti delle prestazioni ancora maggiori:


- Supporto per gli [indici columnstore](http://msdn.microsoft.com/library/gg492153.aspx) in memoria.
- [Partizionamento delle tabelle per riga](http://msdn.microsoft.com/library/ms187802.aspx) con miglioramenti correlati a [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- La disponibilità di viste a gestione dinamica [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) consente di monitorare e ottimizzare le prestazioni.


### Prestazioni affidabili


Se il programma client si connette a SQL Database V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire i seguenti intervalli di porta nella macchina virtuale:

- 11000-11999
- 14000-14999


Fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md) per ulteriori informazioni sulle porte per il Database SQL V12. Le porte sono necessarie per il miglioramento delle prestazioni in SQL Database V12.


## Migliore supporto per i fornitori di soluzioni cloud SaaS


Solo nella versione 12 sono stati rilasciati il nuovo livello di prestazioni Standard S3 e l'anteprima pubblica dei [pool di database elastici](sql-database-elastic-pool.md). Questa soluzione è stata appositamente progettata per i fornitori di soluzioni cloud SaaS. Con i pool di database elastici è possibile:


- Condividere DTU tra i database per ridurre i costi per un numero elevato di database.
- Eseguire [processi di database elastici](sql-database-elastic-jobs-overview.md) per la gestione di database su vasta scala.


## Miglioramenti della sicurezza


La sicurezza è della massima importanza per chiunque gestisca la propria attività aziendale nel cloud. Le funzionalità di sicurezza più recenti rilasciate nella versione 12 includono:


- [Sicurezza a livello di riga](http://msdn.microsoft.com/library/dn765131.aspx)
- [Maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md)
- [Database indipendenti](http://msdn.microsoft.com/library/ff929188.aspx)
- [Ruoli applicazione](http://msdn.microsoft.com/library/ms190998.aspx) gestiti con GRANT, DENY, REVOKE
- [Crittografia dati trasparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)
- [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](sql-database-aad-authentication.md)
 - Il Database SQL supporta ora l'autenticazione di Azure Active Directory, un meccanismo di connessione al Database SQL utilizzando le identità in Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft.
- [Sempre crittografati](https://msdn.microsoft.com/library/mt163865.aspx) (in anteprima) rende la crittografia trasparente alle applicazioni e consente ai client di crittografare dati riservati all'interno delle applicazioni client senza condividere le chiavi di crittografia con il database SQL.


## Aumento della continuità aziendale quando è necessario il ripristino


La versione 12 offre obiettivi del punto di ripristino (RPO) e tempi di recupero stimato (ERT) significativi:


| Funzionalità per la continuità aziendale | Versione precedente | Versione 12 |
| :-- | :-- | :-- |
| Ripristino geografico | • RPO < 24 ore.<br/>• ERT < 12 ore. | • RPO < 1 ora.<br/>• ERT < 12 ore. |
| Replica geografica standard | • RPO < 30 minuti.<br/>• ERT < 2 ore. | • RPO < 5 secondi.<br/>• ERT < 30 secondi. |
| Replica geografica attiva | • RPO < 5 minuti.<br/>• ERT < 1 ora. | • RPO < 5 secondi.<br/>• ERT < 30 secondi. |


Per altre informazioni, vedere [Continuità aziendale del database SQL](sql-database-business-continuity.md).


## Altri motivi per eseguire subito l'aggiornamento


Ci sono molti altri buoni motivi per i quali i clienti dovrebbero eseguire l'aggiornamento dalla versione 11 alla versione 12 del database SQL di Azure:


- La versione 12 del database SQL include un lungo elenco di funzionalità oltre a quelle della versione 11.
- Alla versione 12 continuano a essere aggiunte nuove funzionalità, mentre non ne verranno aggiunte alla versione 11.
- La maggior parte delle nuove funzionalità viene rilasciata nella versione 12 del database SQL prima di esserlo per Microsoft SQL Server.


## Se si sta già usando la versione 12


Per verificare in modo semplice se è in esecuzione un database o un server logico in una versione precedente del servizio Database SQL, eseguire le operazioni seguenti:


1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia**.
3. Fare clic su **SQL Server**.
4. L'icona accanto al server o al database è significativa:
 - ![Icona del server versione 12](./media/sql-database-v12-whats-new/v12_icon.png) **Server logico versione 12**
 - ![Icona del server di versioni precedenti](./media/sql-database-v12-whats-new/earlier_icon.png) **Versione precedente del server logico**


Un altra tecnica per verificare la versione consiste nell'eseguire l'istruzione `SELECT @@version;` nel database e visualizzare risultati simili ai seguenti:


- **12**.0.2000.10 &nbsp; *(versione 12)*
- **11**.0.9228.18 &nbsp; *(versione 11)*


Un database versione 12 può essere ospitato solo in un server logico della versione 12. Un server versione 12 può ospitare solo database della versione 12.


Se ancora non si usa la versione 12, è possibile aggiornare il server logico seguendo i passaggi descritti nell'articolo relativo all'[aggiornamento sul posto alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Aree generali di disponibilità


- Dal 31 luglio 2015, tutte le aree sono state promosse a Disponibilità generale (GA).
- La versione 12 è stata rilasciata a dicembre 2014, ma solo con lo stato di Anteprima.

[Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!---HONumber=AcomDC_0302_2016-->