---
title: Architettura della connettività di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo articolo presenta una panoramica delle comunicazioni di Istanza gestita di database SQL di Azure e spiega l'architettura della connettività e il funzionamento dei diversi componenti per indirizzare il traffico a Istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 08/16/2018
ms.openlocfilehash: 312425d3ea02d15a992b9a694f09cb2be73b6221
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161591"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Architettura della connettività di Istanza gestita di database SQL di Azure 

Questo articolo presenta una panoramica delle comunicazioni di Istanza gestita di database SQL di Azure e spiega l'architettura della connettività e il funzionamento dei diversi componenti per indirizzare il traffico a Istanza gestita.  

## <a name="communication-overview"></a>Panoramica delle comunicazioni 

Il diagramma seguente mostra le entità che si connettono a Istanza gestita e le risorse che Istanza gestita deve raggiungere per funzionare correttamente. 

![Connettività delle entità dell'architettura](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

La comunicazione raffigurata nella parte inferiore del diagramma rappresenta gli strumenti e le applicazioni dei clienti che si connettono a Istanza gestita come origine dati.  

Poiché Istanza gestita è una soluzione PaaS (piattaforma distribuita come servizio), Microsoft gestisce questo servizio usando agenti automatizzati (gestione, distribuzione e manutenzione) basati sui flussi di dati di telemetria. Poiché la gestione di Istanza gestita è esclusivamente di responsabilità di Microsoft, i clienti non possono accedere alle macchine del cluster virtuale di Istanza gestita tramite RDP. 

Alcune operazioni di SQL Server avviate dalle applicazioni o dagli utenti finali possono richiedere l'interazione di Istanza gestita con la piattaforma. Un caso è la creazione di un database di Istanza gestita, ovvero una risorsa esposta tramite il portale, PowerShell e l'interfaccia della riga di comando di Azure. 

Istanza gestita dipende da altri servizi di Azure per il corretto funzionamento (ad esempio Archiviazione di Azure per i backup, il bus di servizio di Azure per la telemetria, Azure AD per l'autenticazione, Azure Key Vault per Transparent Data Encryption e così via) e avvia le connessioni a tali servizi di conseguenza. 

Tutte le comunicazioni, indicate in precedenza, vengono crittografate e firmate mediante certificati. Per assicurarsi che le parti che comunicano siano attendibili, Istanza gestita verifica costantemente i certificati contattando l'autorità di certificazione. Se i certificati vengono revocati o Istanza gestita non può verificarli, le connessioni vengono chiuse per proteggere i dati. 

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività 

A livello generale, Istanza gestita è un set di componenti del servizio ospitati in un set dedicato di macchine virtuali isolate che vengono eseguite nella subnet di rete virtuale del cliente e formano un cluster virtuale. 

In un singolo cluster virtuale possono venire ospitate più istanze gestite. Il cluster viene automaticamente espanso o ridotto, se necessario, quando il cliente modifica il numero di istanze nella subnet di cui viene effettuato il provisioning. 

Le applicazioni dei clienti possono connettersi a Istanza gestita, eseguire query e aggiornare i database solo se vengono eseguite all'interno della rete virtuale o in una rete virtuale con peering o in una crete connessa tramite VPN/ExpressRoute tramite endpoint con indirizzo IP privato.  

![Diagramma dell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

I servizi di gestione e distribuzione Microsoft vengono eseguiti all'esterno della rete virtuale, quindi la connessione tra Istanza gestita e i servizi Microsoft passa negli endpoint con indirizzi IP pubblici. Quando Istanza gestita crea una connessione in uscita, sul lato del ricevente sembra provenire da questo indirizzo IP pubblico a causa del processo NAT (Network Address Translation). 

Il traffico di gestione passa attraverso la rete virtuale del cliente. Ciò significa che gli elementi dell'infrastruttura di rete virtuale interessano e potrebbero danneggiare il traffico di gestione e l'istanza potrebbe passare a uno stato di errore e smettere di funzionare. 

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e la disponibilità dei servizi, Microsoft applica criteri di finalità di rete agli elementi dell'infrastruttura di rete virtuale di Azure che potrebbero influire sul funzionamento di Istanza gestita. Si tratta di un meccanismo di piattaforma per comunicare i requisiti di rete in modo trasparente agli utenti finali, con l'obiettivo principale di evitare errori di configurazione di rete e garantire il nomale funzionamento delle operazioni di Istanza gestita. Quando si elimina Istanza gestita vengono rimossi anche i criteri di finalità di rete. 

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale 

Verrà ora esaminata in modo più approfondito l'architettura della connettività di Istanza gestita. Il diagramma seguente illustra il layout concettuale del cluster virtuale. 

![Diagramma dell'architettura della connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a Istanza gestita usando il nome host nel formato <mi_name>.<clusterid>.database.windows.net. Questo nome host viene risolto in un indirizzo IP privato, anche se è registrato nella zona DNS pubblica ed è risolvibile pubblicamente. 

L'indirizzo IP privato appartiene al servizio di bilanciamento del carico interno di Istanza gestita che indirizza il traffico al gateway di Istanza gestita. Poiché potenzialmente potrebbero venire eseguite più istanze gestite nello stesso cluster, il gateway usa il nome host di Istanza gestita per reindirizzare il traffico al servizio del motore SQL corretto. 

I servizi di gestione e distribuzione si connettono a Istanza gestita usando un endpoint pubblico mappato al servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set di porte predefinite usate esclusivamente dai componenti di gestione di Istanza gestita. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite certificato. 

## <a name="next-steps"></a>Passaggi successivi 

- Per una panoramica, vedere  [Informazioni su Istanza gestita](sql-database-managed-instance.md) 
- Per altre informazioni sulla configurazione della rete virtuale, vedere  [Configurare una rete virtuale per Istanza gestita di database SQL di Azure](sql-database-managed-instance-vnet-configuration.md). 
- Per una guida introduttiva, vedere le informazioni su come creare Istanza gestita: 
  - Dal [portale di Azure](sql-database-managed-instance-get-started.md) 
  - Tramite [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - con un [modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - con un [modello di Azure Resource Manager (incluso jumpbox con SSMS)](https://portal.azure.com/) 

