---
title: Architettura di connettività per un'istanza gestita
description: Informazioni sull'architettura di comunicazione e connettività dell'istanza gestita dal database SQL di Azure, nonché sul modo in cui i componenti indirizzano il traffico all'istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: f30ccd498b79c36c8892ae38a3e26d169249621a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481100"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architettura di connettività per un'istanza gestita nel database SQL di AzureConnectivity architecture for a managed instance in Azure SQL Database

Questo articolo illustra la comunicazione in un'istanza gestita del database SQL di Azure.This article explains communication in an Azure SQL Database managed instance. Viene inoltre descritta l'architettura di connettività e il modo in cui i componenti indirizzano il traffico all'istanza gestita.  

L'istanza gestita del database SQL viene inserita nella rete virtuale di Azure e nella subnet dedicata alle istanze gestite. Questa distribuzione fornisce:

- Un indirizzo IP privato sicuro.
- La possibilità di connettere una rete locale a un'istanza gestita.
- Possibilità di connettere un'istanza gestita a un server collegato o a un altro archivio dati locale.
- La possibilità di connettere un'istanza gestita alle risorse di Azure.The ability to connect a managed instance to Azure resources.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Il diagramma seguente mostra le entità che si connettono a un'istanza gestita. Mostra inoltre le risorse che devono comunicare con l'istanza gestita. Il processo di comunicazione nella parte inferiore del diagramma rappresenta le applicazioni e gli strumenti dei clienti che si connettono all'istanza gestita come origini dati.  

![Entità nell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Un'istanza gestita è un'offerta di piattaforma come servizio (PaaS). Microsoft utilizza agenti automatizzati (gestione, distribuzione e manutenzione) per gestire questo servizio in base ai flussi di dati di telemetria. Poiché Microsoft è responsabile della gestione, i clienti non possono accedere alle macchine cluster virtuali dell'istanza gestita tramite Remote Desktop Protocol (RDP).

Alcune operazioni di SQL Server avviate da utenti finali o applicazioni potrebbero richiedere istanze gestite per interagire con la piattaforma. Un caso è la creazione di un database dell'istanza gestita. Questa risorsa viene esposta tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

Le istanze gestite dipendono da servizi di Azure come Archiviazione di Azure per i backup, Hub eventi di Azure per la telemetria, Azure Active Directory per l'autenticazione, Archiviazione chiave di Azure per Transparent Data Encryption (TDE) e un paio di servizi della piattaforma Azure che forniscono funzionalità di sicurezza e supportabilità. Le istanze gestite effettuano connessioni a questi servizi.

Tutte le comunicazioni sono crittografate e firmate utilizzando certificati. Per verificare l'affidabilità delle parti in comunicazione, le istanze gestite verificano costantemente questi certificati tramite gli elenchi di revoche di certificati. Se i certificati vengono revocati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A livello generale, un'istanza gestita è un set di componenti del servizio. Questi componenti sono ospitati in un set dedicato di macchine virtuali isolate eseguite all'interno della subnet della rete virtuale del cliente. Queste macchine formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite. Se necessario, il cluster espande o si contrae automaticamente quando il cliente modifica il numero di istanze di cui è stato eseguito il provisioning nella subnet.

Le applicazioni dei clienti possono connettersi alle istanze gestite e possono eseguire query e aggiornare i database all'interno della rete virtuale, della rete virtuale con peering o della rete connessa tramite VPN o Azure ExpressRoute.Customer applications can connect to managed instances and can query and update databases inside the virtual network, peered virtual network, or network connected by VPN or Azure ExpressRoute. Questa rete deve utilizzare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

I servizi di gestione e distribuzione Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e i servizi Microsoft si connettono tramite gli endpoint con indirizzi IP pubblici. Quando un'istanza gestita crea una connessione in uscita, alla ricezione di NAT (Network Address Translation) finale fa sembrare che la connessione provenga da questo indirizzo IP pubblico.

Il traffico di gestione passa attraverso la rete virtuale del cliente. Ciò significa che gli elementi dell'infrastruttura della rete virtuale possono danneggiare il traffico di gestione rendendo l'istanza guasto e diventando non disponibile.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e la disponibilità dei servizi, Microsoft applica un criterio di finalità di rete sugli elementi dell'infrastruttura di rete virtuale di Azure.To improve customer experience and service availability, Microsoft applies a network intent policy on Azure virtual network infrastructure elements. I criteri possono influire sul funzionamento dell'istanza gestita. Questo meccanismo di piattaforma comunica in modo trasparente i requisiti di rete agli utenti. L'obiettivo principale del criterio è prevenire errori di configurazione della rete e garantire le normali operazioni dell'istanza gestita. Quando si elimina un'istanza gestita, vengono rimossi anche i criteri di finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Diamo un'analisi più approfondita dell'architettura di connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura di connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza `<mi_name>.<dns_zone>.database.windows.net`gestita utilizzando un nome host con il formato . Questo nome host viene risolto in un indirizzo IP privato anche se è registrato in una zona DNS (Domain Name System) pubblica ed è risolvibile pubblicamente. L'oggetto `zone-id` viene generato automaticamente quando si crea il cluster. Se un cluster appena creato ospita un'istanza gestita secondaria, condivide il relativo ID di zona con il cluster primario. Per ulteriori informazioni, vedere Utilizzare gruppi di failover automatico per abilitare il [failover trasparente e coordinato di più database.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)

Questo indirizzo IP privato appartiene al servizio di bilanciamento del carico interno dell'istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway dell'istanza gestita. Poiché più istanze gestite possono essere eseguite all'interno dello stesso cluster, il gateway usa il nome host dell'istanza gestita per reindirizzare il traffico al servizio motore SQL corretto.

Servizi di gestione e distribuzione si connettono a un'istanza gestita usando un endpoint di [gestione](#management-endpoint) mappato a un servizio di bilanciamento del carico esterno. Il traffico viene instradato ai nodi solo se viene ricevuto in un set predefinito di porte utilizzate solo dai componenti di gestione dell'istanza gestita. Un firewall incorporato nei nodi è configurato per consentire il traffico solo dagli intervalli IP Microsoft. I certificati autenticano reciprocamente tutte le comunicazioni tra i componenti di gestione e il piano di gestione.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita utilizzando un endpoint di gestione. Questo endpoint si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall incorporato a livello di rete. A livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [Determinare l'indirizzo IP dell'endpoint di gestione.](sql-database-managed-instance-find-management-endpoint-ip-address.md)

Quando le connessioni iniziano all'interno dell'istanza gestita (come con i backup e i log di controllo), il traffico sembra iniziare dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso ai servizi pubblici da un'istanza gestita impostando regole del firewall per consentire solo l'indirizzo IP dell'istanza gestita. Per ulteriori informazioni, consultate [Verificare il firewall incorporato dell'istanza gestita.](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)

> [!NOTE]
> Il traffico destinato ai servizi di Azure che si trovano all'interno dell'area dell'istanza gestita è ottimizzato e per questo motivo non è associato all'indirizzo IP pubblico dell'endpoint di gestione dell'istanza gestita. Per questo motivo, se è necessario utilizzare regole del firewall basate su IP, in genere per l'archiviazione, il servizio deve trovarsi in un'area diversa dall'istanza gestita.

## <a name="service-aided-subnet-configuration"></a>Configurazione della subnet assistita dal servizioService-aided subnet configuration

Per soddisfare i requisiti di sicurezza e gestibilità dei clienti, l'istanza gestita sta passando dalla configurazione manuale a quella assistita.

Con l'utente della configurazione di subnet assistita dal servizio è in pieno controllo del traffico di dati (TDS) mentre l'istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per soddisfare il contratto di servizio.

La configurazione della subnet assistita dal servizio si basa sulla funzionalità di delega delle [subnet](../virtual-network/subnet-delegation-overview.md) della rete virtuale per fornire la gestione automatica della configurazione di rete e abilitare gli endpoint del servizio. Gli endpoint di servizio possono essere usati per configurare le regole del firewall della rete virtuale negli account di archiviazione che mantengono backup/log di controllo.

### <a name="network-requirements"></a>Requisiti di rete 

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le seguenti caratteristiche:

- **Subnet dedicata:** La subnet dell'istanza gestita non può contenere altri servizi cloud associati e non può essere una subnet gateway. La subnet non può contenere alcuna risorsa tranne l'istanza gestita e non è possibile aggiungere altri tipi di risorse nella subnet.
- **Delega subnet:** La subnet dell'istanza gestita deve `Microsoft.Sql/managedInstances` essere delegata al provider di risorse.
- **Gruppo di sicurezza di rete:Network security group (NSG):** Un gruppo di sicurezza di rete deve essere associato alla subnet dell'istanza gestita. È possibile usare un gruppo di sicurezza di rete per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per il reindirizzamento delle connessioni. Il servizio eseguirà automaticamente il provisioning e manterrà [le regole](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) attuali necessarie per consentire un flusso ininterrotto del traffico di gestione.
- **Tabella del percorso definito dall'utente (UDR):** Una tabella UDR deve essere associata alla subnet dell'istanza gestita. È possibile aggiungere voci alla tabella di route per instradare il traffico con intervalli IP privati locali come destinazione tramite il gateway di rete virtuale o l'appliance di rete virtuale (NVA). Il servizio eseguirà automaticamente il provisioning e manterrà [le voci correnti](#user-defined-routes-with-service-aided-subnet-configuration) necessarie per consentire un flusso ininterrotto del traffico di gestione.
- **Indirizzi IP sufficienti:** La subnet dell'istanza gestita deve avere almeno 16 indirizzi IP. Il minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite.](sql-database-managed-instance-determine-size-vnet-subnet.md) È possibile distribuire istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata per soddisfare i requisiti di rete per [le istanze gestite.](#network-requirements) In caso contrario, creare una [nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Quando si crea un'istanza gestita, nella subnet viene applicato un criterio di finalità di rete per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, vengono rimossi anche i criteri di finalità di rete.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza in ingresso obbligatorie con configurazione di subnet con assistenza ai servizi 

| Nome       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement (Gestionesql Server)    |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Allow |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza in uscita obbligatorie con configurazione di subnet assistita dal servizio 

| Nome       |Porta          |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Route definite dall'utente con configurazione di subnet assistita dal servizioUser defined routes with service-aided subnet configuration 

|Nome|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|da subnet a vnetlocale|MI SUBNET|Rete virtuale|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*MI SUBNET si riferisce all'intervallo di indirizzi IP per la subnet nel formato x.x.x.x/y. È possibile trovare queste informazioni nel portale di Azure nelle proprietà della subnet.

Inoltre, è possibile aggiungere voci alla tabella di route per instradare il traffico con intervalli IP privati locali come destinazione tramite il gateway di rete virtuale o l'appliance di rete virtuale (NVA).

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve essere in grado di risolvere i record DNS pubblici. L'uso di funzionalità aggiuntive come l'autenticazione di Azure AD potrebbe richiedere la risoluzione di FQDN aggiuntivi. Per ulteriori informazioni, vedere [Configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Vincoli di rete

**TLS 1.2 viene applicato alle connessioni in uscita:** a gennaio 2020 Microsoft ha applicato TLS 1.2 per il traffico all'intra-servizio in tutti i servizi di Azure.TLS 1.2 is enforced on outbound connections : In January 2020 Microsoft enforced TLS 1.2 for intra-service traffic in all Azure services. For Azure SQL Database managed instance, this resulted in TLS 1.2 being enforced on outbound connections used for replication and linked server connections to SQL Server. Se si utilizzano versioni precedenti di SQL Server e 2016 con l'istanza gestita, assicurarsi che siano stati applicati [aggiornamenti specifici TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Le funzionalità di rete virtuale seguenti non sono attualmente supportate con l'istanza gestita:Following virtual network features are currently not supported with Managed Instance:
- **Peering Microsoft:** abilitazione del [peering Microsoft](../expressroute/expressroute-faqs.md#microsoft-peering) su circuiti di route rapida sottoposti a peering direttamente o in transitivo con la rete virtuale in cui risiede l'istanza gestita influiscono sul flusso di traffico tra i componenti dell'istanza gestita all'interno della rete virtuale e sui servizi, dipende dalla causa dei problemi di disponibilità. Si prevede che le distribuzioni di istanze gestite nella rete virtuale con peering Microsoft già abilitato avranno esito negativo.
- **Peering di rete virtuale globale:** la connettività di [peering](../virtual-network/virtual-network-peering-overview.md) della rete virtuale tra aree di Azure non funziona per l'istanza gestita a causa di vincoli di [bilanciamento del carico documentati.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- **AzurePlatformDNS:** l'utilizzo del tag del [servizio](../virtual-network/service-tags-overview.md) AzurePlatformDNS per bloccare la risoluzione DNS della piattaforma renderebbe l'istanza gestita non disponibile. Sebbene l'istanza gestita supporti il DNS definito dal cliente per la risoluzione DNS all'interno del motore, esiste una dipendenza dal DNS della piattaforma per le operazioni della piattaforma.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Obsoleto] Requisiti di rete senza configurazione di subnet assistita dal servizioNetwork requirements without service-aided subnet configuration

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le seguenti caratteristiche:

- **Subnet dedicata:** La subnet dell'istanza gestita non può contenere altri servizi cloud associati e non può essere una subnet gateway. La subnet non può contenere alcuna risorsa tranne l'istanza gestita e non è possibile aggiungere altri tipi di risorse nella subnet.
- **Gruppo di sicurezza di rete:Network security group (NSG):** Un gruppo di sicurezza di rete associato alla rete virtuale deve definire le regole di [sicurezza in ingresso](#mandatory-inbound-security-rules) e le regole di sicurezza in [uscita](#mandatory-outbound-security-rules) prima di qualsiasi altra regola. È possibile usare un gruppo di sicurezza di rete per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per il reindirizzamento delle connessioni.
- **Tabella del percorso definito dall'utente (UDR):** Una tabella UDR associata alla rete virtuale deve includere [voci](#user-defined-routes)specifiche.
- **Nessun endpoint di servizio:No service endpoints:** Nessun endpoint di servizio deve essere associato alla subnet dell'istanza gestita. Assicurarsi che l'opzione degli endpoint del servizio sia disabilitata quando si crea la rete virtuale.
- **Indirizzi IP sufficienti:** La subnet dell'istanza gestita deve avere almeno 16 indirizzi IP. Il minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite.](sql-database-managed-instance-determine-size-vnet-subnet.md) È possibile distribuire istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata per soddisfare i requisiti di rete per [le istanze gestite.](#network-requirements) In caso contrario, creare una [nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Non è possibile distribuire una nuova istanza gestita se la subnet di destinazione non dispone di queste caratteristiche. Quando si crea un'istanza gestita, nella subnet viene applicato un criterio di finalità di rete per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, vengono rimossi anche i criteri di finalità di rete.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie

| Nome       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |MI SUBNET  |Allow |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie

| Nome       |Porta          |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> Assicurarsi che sia presente una sola regola in ingresso per le porte 9000, 9003, 1438, 1440, 1452 e una regola in uscita per le porte 443, 12000. Il provisioning dell'istanza gestita tramite le distribuzioni di Azure Resource Manager avrà esito negativo se le regole in ingresso e in uscita vengono configurate separatamente per ogni porta. Se queste porte si trovano in regole separate, la distribuzione avrà esito negativo con codice di errore`VnetSubnetConflictWithIntendedPolicy`

\*MI SUBNET si riferisce all'intervallo di indirizzi IP per la subnet nel formato x.x.x.x/y. È possibile trovare queste informazioni nel portale di Azure nelle proprietà della subnet.

> [!IMPORTANT]
> Sebbene le regole di sicurezza in ingresso richieste consentano il traffico proveniente da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, queste porte sono protette da un firewall incorporato. Per ulteriori informazioni, vedere [Determinare l'indirizzo dell'endpoint](sql-database-managed-instance-find-management-endpoint-ip-address.md)di gestione.

> [!NOTE]
> Se si utilizza la replica transazionale in un'istanza gestita e si utilizza un database dell'istanza come server di pubblicazione o server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta consentirà l'accesso alla condivisione file di Azure.This port will allow access to the Azure file share.

### <a name="user-defined-routes"></a>route definite dall'utente

|Nome|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|Rete virtuale|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Sicurezza avanzata dei dati del database SQL](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale](sql-database-managed-instance-create-vnet-subnet.md) di Azure o una rete virtuale di Azure [esistente](sql-database-managed-instance-configure-vnet-subnet.md) in cui è possibile distribuire istanze gestite.
- [Calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si desidera distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:Learn how to create a managed instance:
  - Dal [portale di Azure](sql-database-managed-instance-get-started.md).
  - Tramite [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Utilizzando [un modello](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)di Azure Resource Manager .
  - Utilizzando un modello di [Azure Resource Manager (tramite JumpBox, con SSMS incluso)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
