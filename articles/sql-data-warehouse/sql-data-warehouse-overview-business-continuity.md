<properties
   pageTitle="Pianificazione della continuità aziendale in SQL Data Warehouse | Microsoft Azure"
   description="Panoramica della continuità aziendale in SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="sahajs"/>


# Pianificazione della continuità aziendale in SQL Data Warehouse

Questo articolo illustra le nozioni di base della pianificazione della continuità aziendale e del ripristino di emergenza con SQL Data Warehouse. I data warehouse costituiscono il repository centrale di informazioni per le aziende e rivestono un ruolo fondamentale nelle operazioni di routine di analisi e business intelligence a tutti i livelli dell'organizzazione. È pertanto essenziale che il data warehouse sia affidabile e supporti la recuperabilità e il funzionamento continuo. In particolare questo articolo illustrerà il modo in cui SQL Data Warehouse supporta il ripristino da errori dell'utente e il ripristino di emergenza tramite le funzionalità di ripristino del database e il ripristino geografico.

## Concetti

I piani di ripristino di emergenza e continuità aziendale devono essere ottimizzati per le operazioni seguenti:

**Obiettivo del tempo di ripristino:** tempo massimo accettabile prima che il database venga completamente ripristinato dopo un evento di arresto improvviso, ad esempio la perdita massima di disponibilità in caso di errori.

**Obiettivo del punto di ripristino:** finestra temporale massima accettabile di aggiornamenti persi prima che il database venga completamente ripristinato dopo un evento di arresto improvviso, ad esempio la perdita massima di disponibilità in caso di errori.

**Tempo di recupero stimato**: durata stimata per il recupero della completa funzionalità del database dopo una richiesta di ripristino.

## Scenari di continuità aziendale

**Ripristino da errori dell'infrastruttura:** questo scenario si riferisce al ripristino da problemi dell'infrastruttura, ad esempio errori del disco e così via. Un cliente vuole garantire la continuità aziendale con un'infrastruttura con tolleranza di errore e disponibilità elevata.

**Ripristino da errori dell'utente:** questo scenario si riferisce al ripristino da danneggiamento o eliminazione di dati non intenzionale o accidentale. Nel caso in cui un utente modifichi o elimini dati inavvertitamente o accidentalmente, è possibile che un cliente voglia garantire la continuità aziendale ripristinando il database da un punto precedente nel tempo.

**Ripristino di emergenza:** questo scenario si riferisce al ripristino da un evento catastrofico importante. Nello scenario in cui un evento di arresto improvviso, ad esempio una calamità naturale o un'interruzione del servizio a livello locale, rende il database non disponibile, è possibile che un cliente voglia ripristinare il database in un'area diversa per continuare le operazioni aziendali.


## Funzionalità per la continuità aziendale

Si osservi come SQL Data Warehouse migliora l'affidabilità del database e consente la recuperabilità e il funzionamento continuo negli scenari descritti in precedenza.


### Ridondanza dei dati

Poiché SQL Data Warehouse separa il calcolo dall'archiviazione, tutti i dati vengono scritti direttamente in Archiviazione di Azure con ridondanza geografica (RA-GRS). Con l'archiviazione con ridondanza geografica i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Sia nelle aree primarie che in quelle secondarie i dati vengono replicati tre volte tra domini di errore e domini di aggiornamento separati. In questo modo viene garantita la durabilità dei dati anche in caso di una completa interruzione del servizio a livello locale o di una calamità che rende una delle aree non disponibile. Per altre informazioni sull'archiviazione con accesso in lettura e ridondanza geografica, vedere le [opzioni di ridondanza dell'archiviazione di Azure][].

### Ripristino del database

La funzionalità di ripristino del database è stata progettata per ripristinare il database da un punto precedente nel tempo. Il servizio Azure SQL Data Warehouse protegge tutti i database eseguendo snapshot di archiviazione automatica almeno ogni 8 ore e li conserva per 7 giorni in modo da fornire un set discreto di punti di ripristino. Questi backup vengono archiviati in Archiviazione di Azure RA-GRS e pertanto offrono ridondanza geografica per impostazione predefinita. Le funzionalità di backup e ripristino automatici sono disponibili senza spese aggiuntive e consentono di proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero. Per altre informazioni sul ripristino di database, vedere l'articolo relativo al [ripristino da un errore dell'utente][].

### Ripristino geografico

Il ripristino geografico è progettato per ripristinare il database se diventa non disponibile a causa di un evento di arresto improvviso. È possibile contattare il supporto tecnico per ripristinare un database da un backup con ridondanza geografica e creare un nuovo database in un'area di Azure. Poiché il backup è con ridondanza geografica, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. La funzionalità di ripristino geografico viene fornita senza alcun costo aggiuntivo.


## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale di altre edizioni di database SQL, vedere la [panoramica sulla continuità aziendale di database SQL][].

<!--Image references-->

<!--Article references-->
[business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[opzioni di ridondanza dell'archiviazione di Azure]: storage-redundancy/#read-access-geo-redundant-storage-ra-grs.md
[panoramica sulla continuità aziendale di database SQL]: ../sql-database/sql-database-business-continuity.md
[ripristino da un errore dell'utente]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->