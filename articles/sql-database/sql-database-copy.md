---
title: Copiare un database SQL di Azure | Documentazione Microsoft
description: Creare una copia di un database SQL di Azure
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e6152f6539962653a582ba4921af82e4447fd76


---
# <a name="copy-an-azure-sql-database"></a>Copiare un database SQL di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-copy.md)
> * [Portale di Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

È possibile utilizzare i [backup automatici del database SQL](sql-database-automated-backups.md) di Azure per creare una copia del database SQL. La copia del database usa la stessa tecnologia della funzionalità di replica geografica. Tuttavia, a differenza della replica geografica, interrompe il collegamento di replica al completamento della fase di seeding. Pertanto, il database copia è uno snapshot del database di origine al momento della richiesta di copia.  
È possibile creare la copia del database nello stesso server o su un altro server. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine per impostazione predefinita. Quando si usa l'API, è possibile selezionare un livello di prestazioni differente all'interno del livello di servizio stesso (edizione). Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.  

Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.  

Quando la copia del database viene eseguita su un server logico diverso, l'entità di sicurezza sul nuovo server diventa il proprietario del database nel nuovo database. L'uso di [utenti di database indipendente](sql-database-manage-logins.md) per l'accesso ai dati garantisce che per i database primari e secondari ci siano sempre le stesse credenziali utente, in modo che dopo il completamento della copia sia possibile accedervi immediatamente con le stesse credenziali. Se si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), è possibile eliminare completamente l'esigenza di gestione delle credenziali nella copia. Tuttavia, quando si copia il database in un nuovo server, l'accesso basato sulle credenziali non sarà generalmente funzionante perché le credenziali di accesso non esistono nel nuovo server. Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione degli accessi con credenziali durante la copia di un database in un server logico differente. 

Per copiare un database SQL è necessario quanto segue:

* Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
* Un database SQL da copiare. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Passaggi successivi
* Per copiare un database tramite il portale di Azure, vedere [Copiare un database SQL di Azure tramite il portale di Azure](sql-database-copy-portal.md) .
* Vedere [Copiare un database SQL di Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
* Vedere [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md) per copiare un database usando Transact-SQL.
* Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Gestire gli accessi](sql-database-manage-logins.md)
* [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
* [Esportare il database in un BACPAC](sql-database-export.md)
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


