---
title: Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure | Microsoft Docs
description: Informazioni sulla panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni di database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure
Esistono diversi prerequisiti obbligatori per assicurare che il Servizio Migrazione del database di Azure venga eseguito senza problemi quando si eseguono migrazioni di database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

I prerequisiti associati all'utilizzo del Servizio Migrazione del database di Azure sono elencati nelle sezioni seguenti.

## <a name="prerequisites-common-across-migration-scenarios"></a>Prerequisiti comuni tra gli scenari di migrazione
In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:
- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Prerequisiti per la migrazione di un database SQL Server a un database SQL di Azure 
Oltre ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione, ci sono anche altri prerequisiti che si applicano specificatamente a uno scenario o all'altro.

Quando si usa il Servizio Migrazione del database di Azure per eseguire le migrazioni di un database SQL Server a un database SQL di Azure, oltre ai prerequisiti comuni a tutti gli scenari di migrazione, è necessario risolvere i seguenti prerequisiti aggiuntivi:
- Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Creare un'istanza dell'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal).
- Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) 3.3 o versione successiva.
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine.
- Creare una [regola del firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) a livello di server per il server di database SQL di Azure per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
- Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.

   > [!NOTE]
   > Per l'elenco completo dei prerequisiti richiesti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni da SQL Server al database SQL di Azure, vedere l'esercitazione [Eseguire la migrazione di SQL Server nel database SQL di Azure](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e la Disponibilità a livello di area durante l'anteprima pubblica, vedere l'articolo [Informazioni sull'anteprima del Servizio Migrazione del database di Azure](dms-overview.md). 