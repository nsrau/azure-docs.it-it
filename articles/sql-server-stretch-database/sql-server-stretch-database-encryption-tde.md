---
title: Abilitare Transparent Data Encryption per Estensione database - Azure | Documentazione Microsoft
description: Abilitare Transparent Data Encryption (TDE) per Estensione database di SQL Server su Azure
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a


---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Abilitare Transparent Data Encryption (TDE) per Estensione database su Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

La funzionalità Transparent Data Encryption (TDE) consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". La chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server Azure. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Abilitazione della crittografia
Per abilitare la funzionalità TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione **Transparent data encryption**![][1]
4. Selezionare **Attiva** e quindi selezionare **Salva**
   ![][2]

## <a name="disabling-encryption"></a>Disabilitazione della crittografia
Per disabilitare TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione **Transparent data encryption**
4. Selezionare **Disattiva** e quindi selezionare **Salva**

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->



<!--HONumber=Jan17_HO4-->


