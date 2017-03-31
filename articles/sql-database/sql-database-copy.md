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
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e64abfd5581c02b609707f7fa712962c024b293b
ms.lasthandoff: 03/23/2017


---
# <a name="copy-an-azure-sql-database"></a>Copiare un database SQL di Azure

È possibile creare la copia del database nello stesso server o su un altro server. La copia del database è uno snapshot del database di origine al momento della richiesta di copia. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine per impostazione predefinita. Quando si usa l'API, è possibile selezionare un livello di prestazioni differente all'interno del livello di servizio stesso (edizione). Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. A questo punto, è possibile aggiornare o effettuare il downgrade della copia a qualsiasi edizione. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.  

Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.  

Quando la copia del database viene eseguita su un server logico diverso, l'entità di sicurezza sul nuovo server diventa il proprietario del database nel nuovo database. L'uso di [utenti di database indipendente](sql-database-manage-logins.md) per l'accesso ai dati garantisce che per i database primari e secondari ci siano sempre le stesse credenziali utente, in modo che dopo il completamento della copia sia possibile accedervi immediatamente con le stesse credenziali. Se si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), è possibile eliminare completamente l'esigenza di gestione delle credenziali nella copia. Tuttavia, quando si copia il database in un nuovo server, è possibile che l'accesso basato sulle credenziali non funzioni perché le credenziali di accesso non esistono nel nuovo server. Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione degli accessi con credenziali durante la copia di un database in un server logico differente. 

È possibile copiare un database SQL usando il [portale di Azure](sql-database-copy-portal.md), [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) o [T-SQL](sql-database-copy-transact-sql.md). 

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso.
* Per informazioni sugli account di accesso, vedere [Gestire gli account di accesso](sql-database-manage-logins.md)
* Per esportare un database, vedere [Esportare il database in un BACPAC](sql-database-export.md)

