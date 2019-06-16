---
title: Eseguire la replica dei dati in Database di Azure per MySQL
description: Questo articolo descrive come eseguire la replica dei dati in ingresso in Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996819"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Eseguire la replica dei dati in Database di Azure per MySQL

Replica dei dati in ingresso consente di sincronizzare i dati da un server MySQL esterno nel servizio Database di Azure per MySQL. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud. La replica dei dati in ingresso si basa sulla replica nativa di MySQL in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL). 

## <a name="when-to-use-data-in-replication"></a>Quando usare la replica dei dati in ingresso
Gli scenari principali da considerare quando si usa la funzione di replica dei dati in ingresso sono i seguenti:

- **Sincronizzazione dei dati ibrida:** con la replica dei dati in ingresso è possibile mantenere sincronizzati i dati tra i server locali e Database di Azure per MySQL. Questa sincronizzazione è utile per la creazione di applicazioni ibride. Il metodo è particolarmente interessante quando si ha già un server di database locale, ma si vogliono spostare i dati in un'area più vicina agli utenti finali.
- **Sincronizzazione multi-cloud:** per soluzioni cloud complesse, usare la replica dei dati in ingresso per sincronizzare i dati tra Database di Azure per MySQL e diversi provider cloud, inclusi servizi di database e macchine virtuali ospitati nei cloud.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="data-not-replicated"></a>Dati non replicati
Il [*database di sistema mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) sul server master non viene replicato. Le modifiche agli account e alle autorizzazioni sul server master non vengono replicate. Se si crea un account sul server master e questo deve accedere al server di replica, creare manualmente lo stesso account sul lato del server di replica. Per informazioni sulle tabelle contenute nel database di sistema, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Requisiti
- Nel server master deve essere installata almeno la versione 5.6 di MySQL. 
- Le versioni del server master e del server di replica devono essere identiche. Ad esempio, in entrambi deve essere installato MySQL versione 5.6 o MySQL versione 5.7.
- Ogni tabella deve avere una chiave primaria.
- Il server master deve usare il motore InnoDB di MySQL.
- L'utente deve disporre delle autorizzazioni necessarie per configurare la registrazione binaria e creare nuovi utenti sul server master.

### <a name="other"></a>Altri
- La replica dei dati in ingresso è supportata solo nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria.
- Gli identificatori di transazione globale (GTID) non sono supportati.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare la replica dei dati in ingresso](howto-data-in-replication.md)
- Informazioni sulla [replica in Azure con repliche in lettura](concepts-read-replicas.md)
