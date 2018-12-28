---
title: Come configurare Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare e gestire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439518"
---
# <a name="how-to-use-managed-instance"></a>Come usare Istanza gestita

In questa sezione sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare Istanza gestita di database SQL di Azure.

## <a name="migration"></a>Migrazione

- [Eseguire la migrazione a Istanza gestita](sql-database-managed-instance-migrate.md): ottenere informazioni sul processo di migrazione consigliato e sugli strumenti per la migrazione in Istanza gestita.

- [Eseguire la migrazione del certificato TDE a Istanza gestita](sql-database-managed-instance-migrate-tde-certificate.md): se il database di SQL Server è protetto tramite crittografia TDE (Transparent Data Encryption), è necessario eseguire la migrazione di certificati che Istanza gestita può usare per decrittografare il backup da ripristinare in Azure.

## <a name="network-configuration"></a>Configurazione di rete

- [Determinare le dimensioni della subnet di Istanza gestita](sql-database-managed-instance-determine-size-vnet-subnet.md): la funzione Istanza gestita viene inserita in subnet dedicate che non possono essere ridimensionate quando si aggiungono le risorse all'interno. Si deve pertanto calcolare quale intervallo di indirizzi IP sarebbe necessario per la subnet a seconda del numero e dei tipi di istanze da distribuire nella subnet.
- [Creare una nuova rete virtuale e una nuova subnet per Istanza gestita](sql-database-managed-instance-create-vnet-subnet.md): la rete virtuale e la subnet di Azure in cui si intende distribuire le istanze gestite devono essere configurate in base ai [requisiti di rete descritti qui](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Questa guida descrive il modo più semplice per creare la nuova rete virtuale e la nuova subnet configurate correttamente per le istanze gestite.
- [Configurare una rete virtuale e una subnet esistenti per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md): se si intende configurare la rete virtuale e la subnet esistenti per distribuire le istanze gestite all'interno, qui è possibile trovare lo script che controlla i [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements) e che consente di configurare la subnet in base ai requisiti.
- [Configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md): è necessario configurare un DNS personalizzato se si intende accedere a risorse esterne in domini personalizzati da Istanza gestita tramite server collegati di profili di posta elettronica di database.
- [Sincronizzare la configurazione di rete](sql-database-managed-instance-sync-network-configuration.md): può accadere che, sebbene [l'app in uso sia stata integrata con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md), non sia possibile stabilire una connessione a Istanza gestita. È possibile provare ad aggiornare la configurazione di rete per il piano di servizio.
- [Trovare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md): Istanza gestita usa endpoint pubblici solo per scopi di gestione. È possibile determinare l'indirizzo IP dell'endpoint di gestione tramite lo script descritto qui.
- [Verificare la protezione del firewall incorporato](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md): per la protezione di Istanza gestita è disponibile un firewall incorporato che consente il traffico solo sulle porte necessarie. È possibile controllare e verificare le regole del firewall incorporato usando lo script descritto in questa guida.
- [Connettere le applicazioni](sql-database-managed-instance-connect-app.md): la funzione Istanza gestita viene inserita nella rete virtuale di Azure privata in uso con indirizzo IP privato. Informazioni su modelli differenti per connettere le applicazioni a Istanza gestita.

## <a name="feature-configuration"></a>Configurazione delle funzionalità

- La [replica transazionale](replication-with-sql-database-managed-instance.md) consente di replicare i dati tra istanze gestite o da SQL Server in locale a Istanza gestita e viceversa. Altre informazioni sull'uso e sulla configurazione della replica delle transazioni sono disponibili in questa guida.
- [Configurare il rilevamento delle minacce](sql-database-managed-instance-threat-detection.md): il [rilevamento delle minacce](sql-database-threat-detection-overview.md) è una funzionalità integrata del database SQL di Azure che rileva attacchi potenziali diversi, ad esempio un attacco SQL injection oppure un accesso da posizioni sospette. Questa guida descrive come abilitare e configurare il [rilevamento delle minacce](sql-database-threat-detection-overview.md) per Istanza gestita.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [guide pratiche per Database singolo](sql-database-howto-single-database.md)