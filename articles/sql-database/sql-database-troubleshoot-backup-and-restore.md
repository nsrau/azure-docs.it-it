---
title: Risoluzione dei problemi di backup e ripristino con il database SQL di Azure
description: Informazioni su come ripristinare un database cloud in seguito a errori e interruzioni tramite i backup e le repliche nel database SQL di Azure.
services: sql-database
documentationcenter: ''
author: dalechen
manager: felixwu
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche

---
# Ripristinare un database a un momento precedente, ripristinare un database eliminato o eseguire il ripristino in seguito a un'interruzione del data center
Database SQL mantiene le repliche del database, pertanto è possibile eseguire il ripristino in seguito a interruzioni ed errori dell'utente. Le opzioni disponibili dipendono dal livello di servizio del database e dalle opzioni scelte. Per informazioni dettagliate sulle considerazioni di progettazione, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## Per ripristinare un database nello stato a un momento precedente
1. Nel [portale di Azure](https://azure.microsoft.com/) fare clic su **Database SQL**.
2. Selezionare il database dall'elenco e quindi fare clic su **Ripristina**.
3. Digitare un nuovo nome per il database, scegliere la data e l'ora da cui eseguire il ripristino e quindi fare clic su **Crea.**
4. Apportare le modifiche necessarie all'app per fare riferimento al nuovo database. Vedere la sezione sul [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

## Per ripristinare un database accidentalmente eliminato
1. Nel [portale di Azure](https://azure.microsoft.com/) fare clic su **SQL Server**.
2. Selezionare dall'elenco il server che ospitava il database.
3. Nel pannello del server scorrere verso il basso e fare clic su **Database eliminati**.
4. Selezionare il database da ripristinare e quindi fare clic su **Crea**.
5. Apportare le modifiche necessarie all'app per fare riferimento al nuovo database. Vedere la sezione sul [ripristino di un database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore).

## Per eseguire il ripristino in seguito a un'interruzione del data center regionale
Con i database Standard e Premium, se sono stati configurati database secondari con replica geografica, è possibile usarli per eseguire il ripristino. In questo modo è possibile ripristinare un database con una possibilità inferiore che si verifichi una perdita di dati. Per informazioni, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

Azure offre inoltre backup di ogni database in un'area diversa (un backup con ridondanza geografica). È possibile creare un nuovo database da questi backup tramite il Ripristino geografico, ma è probabile che si verifichi una perdita di dati se ci si affida solo a questo metodo.

**Per ripristinare un database mediante il ripristino geografico:**

* Nel [portale di Azure](https://azure.microsoft.com/) fare clic su **Nuovo**, quindi su **Dati e archiviazione**, **Database SQL** e quindi selezionare **Backup** come origine del database. Per informazioni dettagliate, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

<!---HONumber=AcomDC_0831_2016-->