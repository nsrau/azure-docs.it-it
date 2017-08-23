---
title: Registri server nel database di Azure per PostgreSQL | Microsoft Docs
description: Genera registri errori e log di query nel database di Azure per PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c384171c806e929a425e4299c81f0fb2321dc91b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Registri server nel database di Azure per PostgreSQL 
Il database di Azure per PostgreSQL genera log di query e registri errori. Tuttavia, l'accesso ai log delle transazioni non è supportato. Questi log possono essere usati per individuare e risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. Per altre informazioni, vedere [Error Reporting and Logging](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) (Segnalazione e registrazione degli errori).

## <a name="access-server-logs"></a>Accesso ai registri server
È possibile elencare e scaricare i registri errori del server PostgreSQL di Azure tramite il portale di Azure, [l'interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md) e le API REST di Azure.

## <a name="log-retention"></a>Conservazione dei log
È possibile impostare il periodo di conservazione dei registri di usando il parametro **log\_retention\_period** (periodo di conservazione dei log) associato al server. L'unità di questo parametro è espressa in giorni (da confermare). Il valore predefinito è 3 giorni. Il valore massimo è 7 giorni. Si noti che il server deve disporre di risorse di archiviazione allocate sufficienti per i file di log conservati.
I file di log ruotano ogni ora o 100 MB di spazio, a seconda della condizione che si verifica per prima.

## <a name="configure-logging-for-azure-postgresql-server"></a>Configurare la registrazione per il server PostgreSQL di Azure
È possibile abilitare la registrazione delle query e degli errori per il server. I registri errori possono contenere informazioni su checkpoint, connessioni e vuoto automatico.

È possibile abilitare la registrazione delle query per l'istanza del database PostgreSQL impostando due parametri del server: log\_statement (istruzione log) e log\_min\_duration\_statement (istruzione durata registrazione min).

Il parametro **log\_statement** controlla quali istruzioni SQL sono registrate. È consigliabile impostare questo parametro su ***all*** (tutto) per registrare tutte le istruzioni; il valore predefinito è None (Nessuno, da confermare).

Il parametro **log\_min\_duration\_statement** definisce il limite in millisecondi di un'istruzione da registrare. Si registrano tutte le istruzioni SQL che richiedono più tempo rispetto al valore impostato nel parametro. Questo parametro è disabilitato e impostato su meno 1 (-1) per impostazione predefinita (da confermare). L'attivazione di questo parametro può essere utile per rilevare query non ottimizzate nelle applicazioni.

Il parametro **log\_min\_messages** permette di controllare quali livelli messaggio scrivere nel registro server. Il valore predefinito è WARNING (avviso). (da confermare)

Per altre informazioni su queste impostazioni, vedere la documentazione [Error Reporting and Logging](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) (segnalazione e registrazione degli errori). Per una configurazione particolare del database di Azure per i parametri del server PostgreSQL, vedere [Server Logs in Azure Database for PostgreSQL](concepts-server-logs.md) (Registri server nel database di Azure per PostgreSQL).

## <a name="next-steps"></a>Passaggi successivi
- Per accedere ai registri tramite l'interfaccia della riga di comando di Azure, vedere [Configure and access server logs using Azure CLI](howto-configure-server-logs-using-cli.md) (Accesso e configurazione dei registri server tramite l'interfaccia della riga di comando di Azure)
- Per altre informazioni sui parametri del server, vedere [Customize server configuration parameters using Azure CLI](howto-configure-server-parameters-using-cli.md) (Personalizzare i parametri di configurazione del server tramite l'interfaccia della riga di comando di Azure).
