---
title: Come configurare un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare e gestire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 09e130e8ac0738a3bf9c08147248702d06356a75
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782140"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Come usare un'istanza gestita nel database SQL di Azure

In questo articolo sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare l'istanza gestita.

## <a name="migration"></a>Migrazione

- [Eseguire la migrazione a un'istanza gestita](sql-database-managed-instance-migrate.md): ottenere informazioni sul processo di migrazione consigliato e sugli strumenti per la migrazione in istanza gestita.

- [Eseguire la migrazione del certificato TDE a un'istanza gestita](sql-database-managed-instance-migrate-tde-certificate.md): se il database di SQL Server è protetto tramite crittografia TDE (Transparent Data Encryption), è necessario eseguire la migrazione di certificati che un'istanza gestita può usare per decrittografare il backup da ripristinare in Azure.

## <a name="network-configuration"></a>Configurazione di rete

- [Determinare le dimensioni di una subnet di un'istanza gestita](sql-database-managed-instance-determine-size-vnet-subnet.md): la funzione Istanza gestita viene inserita in subnet dedicate che non possono essere ridimensionate quando si aggiungono le risorse all'interno. Si deve pertanto calcolare quale intervallo di indirizzi IP sarebbe necessario per la subnet a seconda del numero e dei tipi di istanze da distribuire nella subnet.
- [Creare una nuova rete virtuale e una nuova subnet per un'istanza gestita](sql-database-managed-instance-create-vnet-subnet.md): la rete virtuale e la subnet di Azure in cui si intende distribuire le istanze gestite devono essere configurate in base ai [requisiti di rete descritti qui](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Questa guida descrive il modo più semplice per creare la nuova rete virtuale e la nuova subnet configurate correttamente per le istanze gestite.
- [Configurare una rete virtuale e una subnet esistenti per un'istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md): se si intende configurare la rete virtuale e la subnet esistenti per distribuire le istanze gestite all'interno, qui è possibile trovare lo script che controlla i [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements) e che consente di configurare la subnet in base ai requisiti.
- [Configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md): è necessario configurare un DNS personalizzato se si intende accedere a risorse esterne in domini personalizzati dall'istanza gestita tramite server collegati di profili di posta elettronica di database.
- [Sincronizzare la configurazione di rete](sql-database-managed-instance-sync-network-configuration.md): può accadere che, sebbene [l'app in uso sia stata integrata con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md), non sia possibile stabilire una connessione a un'stanza gestita. È possibile provare ad aggiornare la configurazione di rete per il piano di servizio.
- [Trovare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md): Istanza gestita usa endpoint pubblici solo per scopi di gestione. È possibile determinare l'indirizzo IP dell'endpoint di gestione tramite lo script descritto qui.
- [Verificare la protezione del firewall predefinito](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md): per la protezione di Istanza gestita è disponibile un firewall incorporato che consente il traffico solo sulle porte necessarie. È possibile controllare e verificare le regole del firewall incorporato usando lo script descritto in questa guida.
- [Connettere le applicazioni](sql-database-managed-instance-connect-app.md): la funzione Istanza gestita viene inserita nella rete virtuale di Azure privata in uso con indirizzo IP privato. Informazioni su modelli differenti per connettere le applicazioni all'istanza gestita.

## <a name="feature-configuration"></a>Configurazione delle funzionalità

- La [replica transazionale](replication-with-sql-database-managed-instance.md) consente di replicare i dati tra istanze gestite o da SQL Server in locale a un'istanza gestita e viceversa. Altre informazioni sull'uso e sulla configurazione della replica delle transazioni sono disponibili in questa guida.
- [Configurare il rilevamento delle minacce](sql-database-managed-instance-threat-detection.md): il [rilevamento delle minacce](sql-database-threat-detection-overview.md) è una funzionalità integrata del database SQL di Azure che rileva attacchi potenziali diversi, ad esempio un attacco SQL injection oppure un accesso da posizioni sospette. Questa guida descrive come abilitare e configurare il [rilevamento delle minacce](sql-database-threat-detection-overview.md) per un'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere altre informazioni sulle [Guide pratiche per i database singoli](sql-database-howto-single-database.md)