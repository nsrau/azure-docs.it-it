---
title: 'Versioni supportate: server flessibile per database di Azure per MySQL'
description: Informazioni sulle versioni del server MySQL supportate nel database di Azure per il server flessibile MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f29a34fd8916110355c0122fee9db29599a01231
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937110"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Versioni supportate per database di Azure per MySQL-server flessibile


> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.


Il server flessibile database di Azure per MySQL è alimentato da [MySQL Community Edition](https://www.mysql.com/products/community/), usando il motore InnoDB.

MySQL usa lo schema di denominazione X.Y.Z. X è la versione principale, Y è la versione secondaria Z è la versione di correzione di bug. Per altre informazioni sullo schema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL.

Al momento il Database di Azure per MySQL supporta le versioni indicate di seguito:

## <a name="mysql-version-57"></a>MySQL versione 5.7

Versione correzione bug: 5.7.29

Il servizio esegue l'applicazione automatica di patch dell'hardware, del sistema operativo e del motore di database sottostanti. L'applicazione di patch include aggiornamenti di sicurezza e software. Per il motore MySQL, sono inclusi anche gli aggiornamenti della versione secondaria come parte della versione di manutenzione pianificata. Gli utenti possono configurare la pianificazione dell'applicazione di patch in modo che sia gestita dal sistema o definire la pianificazione personalizzata. Durante la pianificazione della manutenzione, viene applicata la patch e potrebbe essere necessario riavviare il server come parte di questo processo per completare l'aggiornamento. Con la pianificazione personalizzata gli utenti possono prevedere il ciclo di applicazione delle patch e scegliere una finestra di manutenzione con un impatto minimo sull'attività. Il servizio segue in genere la pianificazione di rilascio mensile come parte del processo continuo di integrazione e rilascio.

Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione, vedere le [Note sulla versione](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) di MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il servizio gestisce automaticamente l'applicazione di patch per gli aggiornamenti delle versioni di correzione dei bug. Ad esempio, 5.7.29 a 5.7.30.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Creare un'app PHP in Windows con MySQL](../../app-service/app-service-web-tutorial-php-mysql.md)<br/>
>[Crea app PHP in Linux con MySQL](../../app-service/containers/tutorial-php-mysql-app.md)<br/>
>[Crea app Spring basate su Java con MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>
