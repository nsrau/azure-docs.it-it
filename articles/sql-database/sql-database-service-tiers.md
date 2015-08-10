<properties
   pageTitle="Livelli di servizio del database SQL"
   description="Confrontare le prestazioni e le funzionalità di continuità aziendale dei vari livelli di servizio del database SQL di Azure per trovare il giusto compromesso tra costi e funzionalità a fronte di un livello di scalabilità su richiesta senza tempi di inattività."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="07/24/2015"
   ms.author="shkurhek"/>

# Livelli di servizio

Il [database SQL](sql-database-technical-overview.md) è disponibile in anteprima per i livelli di servizio Basic, Standard e Premium. Tutti i tre livelli di servizio garantiscono un tempo di attività previsto dal [contratto di servizio](http://azure.microsoft.com/support/legal/sla/) del 99,99% e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Con più livelli di prestazioni all'interno di ogni livello di servizio, è possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro specifico. Se è necessario applicare la scalabilità verso l'alto o verso il basso, è possibile modificare facilmente i livelli del database nel portale di Azure senza tempi di inattività per l'applicazione. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](https://msdn.microsoft.com/library/azure/dn369872.aspx).

> [AZURE.IMPORTANT]Le edizioni Web e Business verranno ritirate dal commercio. Informazioni su come [aggiornare le edizioni Web e Business](sql-database-upgrade-new-service-tiers.md). Leggere la [Domande frequenti sull'interruzione](http://azure.microsoft.com/pricing/details/sql-database/web-business/) se si prevede di continuare a utilizzare le edizioni Web e Business.

## Basic

Il livello Basic è progettato per le applicazioni con un carico di lavoro transazionale ridotto. Un caso di utilizzo tipico è costituito da un'applicazione semplificata, che richiede un database di piccole dimensioni con una singola operazione in un determinato momento.

**Prestazioni dimensioni e funzionalità**


| Livello di servizio | Basic |
| :-- | :-- |
| Unità di velocità effettiva del database (DTU, Database Throughput Unit) | 5 |
| Dimensioni massime del database | 2 GB |
| Ripristino temporizzato (PITR, Point-in-time Restore) | Fino a un millisecondo entro gli ultimi 7 giorni |
| Ripristino di emergenza | Ripristini geografico in qualsiasi area di Azure |
| Obiettivi di prestazioni | Frequenza delle transazioni su base oraria |


## Standard

Il livello Standard rappresenta l'opzione più adatta per iniziare a utilizzare i carichi di lavoro transazionali. Offre prestazioni migliori e migliori funzionalità di continuità aziendale incorporate rispetto al livello Basic. Un caso di utilizzo tipico è rappresentato da un'applicazione con più transazioni simultanee.

**Prestazioni e dimensioni**


| Livello di servizio | Standard S0 | Standard S1 | Standard S2 | Standard S3 |
| :-- | :-- | :-- | :-- | :-- |
| Unità di velocità effettiva del database (DTU, Database Throughput Unit) | 10 | 20 | 50 | 100 |
| Dimensioni massime del database | 250 GB | 250 GB | 250 GB | 250 GB |


**Funzionalità**


| Livello di servizio | Standard (S0, S1, S2, S3) |
| :-- | :-- |
| Ripristino temporizzato (PITR, Point-in-time Restore) | Fino a un millisecondo entro gli ultimi 14 giorni |
| Ripristino di emergenza | Replica geografica standard, 1 database secondario offline |
| Obiettivi di prestazioni | Frequenza delle transazioni al minuto |


## Premium

Il livello Premium è progettato per applicazioni di importanza strategica. Offre il miglior livello di prestazioni e di accesso alle funzionalità di continuità aziendale avanzate tra cui la replica geografica attiva presso un massimo di 4 aree di Azure di propria scelta. Un caso di utilizzo tipico è rappresentato da un'applicazione di importanza strategica con un elevato volume di transazioni e diversi utenti simultanei.

**Prestazioni e dimensioni**


| Livello di servizio | Premium P1 | Premium P2 | Premium P6 (precedentemente denominato P3) |
| :-- | :-- | :-- | :-- |
| Unità di velocità effettiva del database (DTU, Database Throughput Unit) | 125 | 250 | 1000 |
| Dimensioni massime del database | 500 GB | 500 GB | 500 GB |


**Funzionalità**


| Livello di servizio | Premium (P1, P2, P6) |
| :-- | :-- |
| Ripristino temporizzato (PITR, Point-in-time Restore) | Fino a un millisecondo entro gli ultimi 35 giorni |
| Ripristino di emergenza | Replica geografica attiva, fino a 4 database secondari online leggibili |
| Obiettivi di prestazioni | Frequenza delle transazioni al secondo |


Ulteriori informazioni sui prezzi di tali livelli sono disponibili in [Database SQL - Prezzi](http://azure.microsoft.com/pricing/details/sql-database/).

Dopo aver acquisito familiarità con i livelli di database SQL, provare la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/) per scoprire [come creare il primo database SQL](sql-database-get-started.md).
 

<!---HONumber=July15_HO5-->