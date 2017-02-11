---
title: Eseguire il backup di database con estensione abilitata | Documentazione Microsoft
description: Informazioni su come eseguire il backup di database\-con estensione abilitata.
services: sql-server-stretch-database
documentationcenter: 
author: Antvgski
manager: johnmac
editor: douglasl
ms.assetid: a196f858-ef8f-47b5-b9db-bb7db98d48bd
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68aeee33dbbba67e2a24300db62bf6bcdc627ea5


---
# <a name="backup-stretch-enabled-databases"></a>Eseguire il backup di database con estensione abilitata
I backup del database consentono di recuperare da molti tipi di errori e guasti.  

* È necessario eseguire il backup dei database SQL Server con estensione abilitata.  
* Microsoft Azure esegue automaticamente il backup dei dati remoti che Estensione database ha migrato da SQL Server in Azure.  

> [!NOTE]
> Il backup è solo una parte di una soluzione completa di continuità aziendale e a disponibilità elevata. Per altre informazioni sulla disponibilità elevata, vedere [Soluzioni a disponibilità elevata (SQL Server)](https://msdn.microsoft.com/library/ms190202.aspx).
> 
> 

## <a name="back-up-your-sql-server-data"></a>Eseguire il backup dei dati di SQL Server
Per eseguire il backup di database SQL Server con estensione abilitata, è possibile continuare a usare i metodi di backup di SQL Server attualmente in uso. Per altre informazioni, vedere [Backup e ripristino di database SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

I backup di un database con estensione abilitata contengono solo dati locali e dati idonei alla migrazione in corrispondenza del punto nel tempo in cui viene eseguito il backup \(I dati idonei non sono stati ancora migrati, ma saranno migrati in Azure in base alle impostazioni di migrazione delle tabelle.\) Questo backup è definito **superficiale** e non include i dati già migrati in Azure.  

## <a name="back-up-your-remote-azure-data"></a>Eseguire il backup di dati di Azure remoti
Microsoft Azure esegue automaticamente il backup dei dati remoti che Estensione database ha migrato da SQL Server in Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure riduce il rischio di perdita dei dati con il backup automatico
Il servizio Estensione database di SQL Server in Azure protegge i database remoti con snapshot di archiviazione automatici almeno ogni 8 ore. Mantiene ogni snapshot per 7 giorni per fornire un intervallo di possibili punti di ripristino.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure riduce il rischio di perdita dei dati con la ridondanza geografica
I backup dei database di Azure vengono archiviati in archiviazioni di Azure (RA\-GRS) con ridondanza geografica e pertanto offrono la ridondanza geografica per impostazione predefinita. Con l'archiviazione con ridondanza geografica i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Sia nelle aree primarie che in quelle secondarie i dati vengono replicati tre volte tra domini di errore e domini di aggiornamento separati. In questo modo viene garantita la durata dei dati anche in caso di una completa interruzione del servizio a livello locale o di una calamità che rende una delle aree di Azure non disponibile.

### <a name="a-namestretchrpoastretch-database-reduces-the-risk-of-data-loss-for-your-azure-data-by-retaining-migrated-rows-temporarily"></a><a name="stretchRPO"></a>Estensione database riduce il rischio di perdita dei dati di Azure conservando temporaneamente le righe migrate
Dopo che Estensione database ha eseguito la migrazione delle righe idonee da SQL Server in Azure, conserva le righe nella tabella di gestione temporanea per un minimo di 8 ore. Se si ripristina un backup del database di Azure, Estensione database usa le righe salvate nella tabella di gestione temporanea per riconciliare i database SQL Server e di Azure.

Dopo aver ripristinato un backup di dati di Azure, è necessario eseguire la stored procedure [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) per riconnettere il database SQL Server con estensione abilitata al database di Azure remoto. Quando si esegue **sys.sp_rda_reauthorize_db**, Estensione database riconosce automaticamente i database SQL Server e i database di Azure.

Per aumentare il numero di ore per cui i dati migrati sono conservati temporaneamente da Estensione database nella tabella di gestione, eseguire la stored procedure [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) e specificare un numero di ore maggiore di 8. Per decidere la quantità di dati da conservare, considerare i fattori seguenti:

* La frequenza dei backup automatici di Azure (almeno ogni 8 ore).
* Il tempo necessario dopo un problema per riconoscere il problema e decidere di ripristinare un backup.
* La durata dell'operazione di ripristino di Azure.

> [!NOTE]
> Aumentando la quantità di dati che Estensione database deve conservare temporaneamente nella tabella di gestione temporanea aumenta la quantità di spazio necessaria in SQL Server.
> 
> 

Per controllare il numero di ore per cui i dati sono conservati attualmente da Estensione database nella tabella di gestione temporanea, eseguire la stored procedure [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Vedere anche
[Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Backup e ripristino di database SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


