<properties
   pageTitle="Domande frequenti sul ritiro dell'edizione Web e Business dei database di SQL Azure | Microsoft Azure"
   description="Informazioni sul ritiro dei database Web e Business di SQL Azure e sulle caratteristiche e funzionalità dei nuovi livelli di servizio."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# Domande frequenti sul ritiro dell'edizione Web e Business

I database SQL di Azure Web e Business sono stati ritirati e sostituiti dai livelli Basic, Standard, Premium ed Elastic.

Per agevolare l'aggiornamento dei database Web e Business, il servizio database SQL consiglia un livello di servizio e un livello di prestazioni appropriato (piano tariffario) per ogni database, in base al carico di lavoro cronologico.

**Per ottenere indicazioni sul piano tariffario:**

- [Eseguire l'aggiornamento a database SQL V12 tramite il portale di Azure](sql-database-upgrade-server-portal.md)
- [Eseguire l'aggiornamento a database SQL V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)
- [Modificare il piano tariffario di un database Web o Business](sql-database-service-tier-advisor.md)



## Perché i database dell'edizione Web e Business sono visualizzati come ritirati nel portale di Azure?

Poiché i database dell'edizione Web e Business non saranno disponibili dopo settembre 2015, nel portale questi database verranno etichettati come ritirati. Questa etichetta fornisce inoltre un promemoria della necessità di aggiornare tutti i database Web e Business ai livelli Premium, Basic o Standard. Per informazioni dettagliate sull'aggiornamento dei database Web o Business esistenti ai nuovi livelli di servizio, vedere [Eseguire l'aggiornamento a database SQL V12 di Azure](sql-database-upgrade-server-portal.md).

## Quale tra i nuovi livelli di servizio rappresenta la scelta ottimale per l'aggiornamento di un database Web o Business esistente?

La selezione di un nuovo livello di servizio e di un livello di prestazioni appropriati per un database Web o Business esistente dipende dai requisiti specifici per funzionalità e prestazioni dell'applicazione.

Fare riferimento alle raccomandazioni per il piano tariffario sopra indicate oppure vedere [Eseguire l'aggiornamento a database SQL V12 di Azure](sql-database-upgrade-server-portal.md) per informazioni dettagliate utili per la scelta di un nuovo livello di servizio appropriato.

## Perché Microsoft sta introducendo nuovi livelli di servizio?

In base ai suggerimenti dei clienti, per il database SQL di Azure sono stati introdotti nuovi livelli di servizio per consentire ai clienti di supportare più facilmente i carichi di lavoro di database relazionale. I nuovi livelli sono progettati per offrire prestazioni prevedibili su una gamma di sette livelli per esigenze di applicazioni transazionali da leggere a onerose. Inoltre, i nuovi livelli offrono una gamma di funzionalità per la continuità aziendale, un contratto di servizio che garantisce tempi di attività migliori, dimensioni dei database maggiori a un costo minore e una migliore esperienza per la fatturazione.

## Dove è possibile ottenere ulteriori informazioni su nuovi livelli di servizio?

Per informazioni dettagliate sui nuovi livelli di servizio e il modello di prestazioni, vedere [Livelli di servizio](sql-database-service-tiers.md). Per informazioni dettagliate sui prezzi per i nuovi livelli di servizio, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Quali caratteristiche o funzionalità non saranno disponibili nei livelli Basic, Standard e Premium?

La funzionalità per le federazioni sarà ritirata con le edizioni Web e Business. I clienti con esigenze di scalabilità orizzontale per i loro database sono invitati a usare in alternativa o a eseguire la migrazione agli [strumenti per i database elastici](sql-database-elastic-scale-get-started.md) per [database SQL di Azure](sql-database-elastic-scale-get-started.md), che semplificano la creazione e la gestione di un'applicazione che usa il partizionamento orizzontale. Una libreria client .NET consente alle applicazioni di definire la modalità di mapping dei dati in partizioni e indirizza le richieste OLTP ai database appropriati. Per supportare operazioni di gestione che riconfigurano la modalità di distribuzione dei dati tra le partizioni, è incluso un modello di servizio cloud di Azure che è possibile ospitare nella sottoscrizione di Azure. Oltre agli [strumenti per database elastici](sql-database-elastic-scale-get-started.md), Microsoft continuerà a creare e pubblicare [indicazioni su modelli e procedure per il partizionamento orizzontale personalizzato](https://msdn.microsoft.com/library/azure/dn764977.aspx) in base alle esperienze e informazioni ricavate dall'analisi approfondita dell'engagement dei clienti. I nuovi clienti con esigenze di scalabilità orizzontale dovrebbero valutare gli [strumenti per database elastici](sql-database-elastic-scale-get-started.md) e/o contattare il supporto Microsoft per valutare le opzioni disponibili.

Microsoft sta inoltre modificando l'esperienza di copia del database con i database Premium. Dato che la quota per i database Premium era in precedenza limitata, l'istruzione CREATE DATABASE … AS A COPY OF in T-SQL consentiva di creare un database Premium sospeso senza risorse riservate, fatturato allo stesso costo di un database Business. Considerata la maggiore disponibilità attuale della quota Premium, lo stato Premium sospeso non è più supportato. Le copie di database verranno ora create con la stessa edizione e lo stesso livello di prestazioni dell'origine e fatturate di conseguenza. I clienti possono scegliere di effettuare il downgrade dei database copiati a un livello di servizio o di prestazioni diverso per ridurre i costi. I database con stato Premium sospeso esistenti verranno convertiti all'edizione Business nell'ambito di questa versione. Si prevede che l'introduzione del [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) ridurrà la necessità di creare copie di backup dei database.

## In quale modo verrà migliorata l'esperienza di fatturazione con i livelli Basic, Standard e Premium?

I database SQL di Azure Basic, Standard e Premium vengono fatturati su base oraria e si ha la possibilità di aumentare o ridurre le risorse di ognuno di essi. La fatturazione è basata su una tariffa fissa associata ai livelli di servizio e di prestazioni più alti scelti per ogni ora. Inoltre, i livelli di prestazioni (ad esempio: Basic, S1 e P2) sono suddivisi nella fattura per poter individuare più facilmente il numero di giorni/ore di database addebitati in un mese per ogni livello di prestazioni. I database Web e Business continueranno a essere fatturati tramite unità database in base alle dimensioni del database. Visitare la pagina [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) per altre informazioni sui prezzi e le differenze tra i nuovi livelli di servizio.


## Vedere anche

[Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Prezzi Web e Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Livelli di servizio](sql-database-service-tiers.md)

[Eseguire l'aggiornamento a database SQL V12 di Azure](sql-database-upgrade-server-portal.md)

<!---HONumber=AcomDC_0810_2016-->