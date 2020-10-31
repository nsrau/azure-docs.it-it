---
title: Architettura della connettività
titleSuffix: Azure SQL Managed Instance
description: Informazioni sull'architettura di comunicazione e connettività di Istanza gestita SQL di Azure, nonché sul modo in cui i componenti indirizzano il traffico a un'istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 5ebe0bcf1e491166c5fc61597904056307f9679c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098009"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Architettura della connettività per Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra la comunicazione in Istanza gestita SQL di Azure. Viene inoltre descritta l'architettura di connettività e il modo in cui i componenti indirizzano il traffico a un'istanza gestita.  

SQL Istanza gestita viene inserito nella rete virtuale di Azure e nella subnet dedicata alle istanze gestite. Questa distribuzione fornisce:

- Un indirizzo IP privato sicuro.
- La possibilità di connettere una rete locale a SQL Istanza gestita.
- La possibilità di connettere SQL Istanza gestita a un server collegato o a un altro archivio dati locale.
- La possibilità di connettere SQL Istanza gestita alle risorse di Azure.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Il diagramma seguente mostra le entità che si connettono a SQL Istanza gestita. Mostra anche le risorse che devono comunicare con un'istanza gestita. Il processo di comunicazione nella parte inferiore del diagramma rappresenta le applicazioni e gli strumenti del cliente che si connettono a SQL Istanza gestita come origini dati.  

![Entità nell'architettura della connettività](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Istanza gestita è un'offerta di piattaforma distribuita come servizio (PaaS). Azure USA agenti automatizzati (gestione, distribuzione e manutenzione) per gestire questo servizio in base ai flussi di dati di telemetria. Poiché Azure è responsabile della gestione, i clienti non possono accedere ai computer cluster virtuali SQL Istanza gestita tramite Remote Desktop Protocol (RDP).

Alcune operazioni avviate dagli utenti finali o dalle applicazioni potrebbero richiedere la Istanza gestita SQL per interagire con la piattaforma. Un caso è la creazione di un database SQL Istanza gestita. ovvero una risorsa esposta tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

SQL Istanza gestita dipende da servizi di Azure, ad esempio archiviazione di Azure per i backup, Hub eventi di Azure per la telemetria, Azure Active Directory (Azure AD) per l'autenticazione, Azure Key Vault per Transparent Data Encryption (Transparent Data Encryption) e un paio di servizi della piattaforma Azure che offrono funzionalità di sicurezza e supporto. SQL Istanza gestita consente di stabilire connessioni a tali servizi.

Tutte le comunicazioni vengono crittografate e firmate mediante certificati. Per verificare l'affidabilità delle parti che comunicano, SQL Istanza gestita verifica costantemente questi certificati tramite gli elenchi di revoche di certificati. Se i certificati vengono revocati, SQL Istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A un livello elevato, SQL Istanza gestita è un set di componenti del servizio. ospitati in un set dedicato di macchine virtuali isolate, che vengono eseguite all'interno della subnet della rete virtuale del cliente. Questi computer formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite e, se necessario, quando il cliente modifica il numero di istanze nella subnet di cui viene effettuato il provisioning, viene automaticamente espanso o ridotto.

Le applicazioni dei clienti possono connettersi a SQL Istanza gestita e possono eseguire query e aggiornare i database all'interno della rete virtuale, della rete virtuale con peering o della rete connessa tramite VPN o Azure ExpressRoute. Questa rete, tuttavia, deve usare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura della connettività](./media/connectivity-architecture-overview/connectivityarch002.png)

I servizi di distribuzione e gestione di Azure vengono eseguiti all'esterno della rete virtuale. SQL Istanza gestita e i servizi di Azure si connettono agli endpoint che hanno indirizzi IP pubblici. Quando SQL Istanza gestita crea una connessione in uscita, nella ricezione NAT (Network Address Translation) finale di ricezione la connessione risulta simile a quella proveniente da questo indirizzo IP pubblico.

Il traffico di gestione passa attraverso la rete virtuale del cliente. Questo significa che gli elementi dell'infrastruttura di rete virtuale potrebbero danneggiare il traffico di gestione e l'istanza potrebbe passare a uno stato di errore e smettere di funzionare.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e la disponibilità dei servizi, Azure applica criteri di finalità di rete negli elementi dell'infrastruttura di rete virtuale di Azure. I criteri possono influire sul funzionamento di SQL Istanza gestita. Questo meccanismo della piattaforma consente di comunicare i requisiti di rete agli utenti in modo trasparente. L'obiettivo principale del criterio è impedire la configurazione errata della rete e garantire le normali operazioni di SQL Istanza gestita. Quando si elimina un'istanza gestita, vengono rimossi anche i criteri relativi alle finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Esaminiamo approfonditamente l'architettura di connettività per SQL Istanza gestita. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura della connettività del cluster virtuale](./media/connectivity-architecture-overview/connectivityarch003.png)

I client si connettono a SQL Istanza gestita usando un nome host con il formato `<mi_name>.<dns_zone>.database.windows.net` . Questo nome host viene risolto in un indirizzo IP privato, sebbene sia registrato in una zona DNS (Public Domain Name System) ed è risolvibile pubblicamente. Quando viene creato il cluster, viene automaticamente generato il valore `zone-id`. Se un nuovo cluster ospita un'istanza gestita secondaria, ne condivide l'ID zona con il cluster principale. Per altre informazioni, vedere [Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato appartiene al servizio di bilanciamento del carico interno per SQL Istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway SQL Istanza gestita. Poiché più istanze gestite possono essere eseguite all'interno dello stesso cluster, il gateway usa il nome host SQL Istanza gestita per reindirizzare il traffico al servizio motore SQL corretto.

I servizi di gestione e distribuzione si connettono a SQL Istanza gestita usando un [endpoint di gestione](#management-endpoint) che esegue il mapping a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set predefinito di porte utilizzate solo dai componenti di gestione di SQL Istanza gestita. Per consentire il traffico solo da intervalli IP specifici di Microsoft, viene inoltre configurato un firewall incorporato nei nodi. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite i certificati.

## <a name="management-endpoint"></a>Endpoint di gestione

Azure gestisce SQL Istanza gestita tramite un endpoint di gestione. Questo endpoint si trova all'interno di un cluster virtuale di un'istanza. L'endpoint di gestione è protetto da un firewall incorporato a livello di rete mentre, a livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [Determinare l'indirizzo IP dell'endpoint di gestione](management-endpoint-find-ip-address.md).

Quando le connessioni vengono avviate all'interno di SQL Istanza gestita (come nel caso dei backup e dei log di controllo), il traffico sembra iniziare dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso ai servizi pubblici da SQL Istanza gestita impostando le regole del firewall in modo da consentire solo l'indirizzo IP per SQL Istanza gestita. Per altre informazioni, vedere [verificare il firewall predefinito di SQL istanza gestita](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Il traffico che passa ai servizi di Azure all'interno dell'area SQL Istanza gestita è ottimizzato e per questo motivo non è stato indirizzato all'indirizzo IP pubblico per l'endpoint di gestione. Per questo motivo, se è necessario usare le regole del firewall basate su IP, in genere per l'archiviazione, il servizio deve trovarsi in un'area diversa da SQL Istanza gestita.

## <a name="service-aided-subnet-configuration"></a>Configurazione della subnet con il supporto del servizio

Per soddisfare i requisiti di sicurezza e gestibilità dei clienti, SQL Istanza gestita esegue la transizione dalla configurazione manuale alla subnet assistita da servizi.

Con la configurazione della subnet assistita da servizi, l'utente è in un traffico TDS (Full Control of data), mentre SQL Istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per soddisfare un contratto di servizio.

La configurazione della subnet assistita da servizi si basa sulla funzionalità di [delega della subnet](../../virtual-network/subnet-delegation-overview.md) della rete virtuale per fornire la gestione automatica della configurazione di rete e abilitare gli endpoint di servizio. 

Gli endpoint di servizio possono essere usati per configurare le regole del firewall per la rete virtuale negli account di archiviazione che conservano i backup e i log di controllo. Anche con gli endpoint di servizio abilitati, i clienti sono invitati a usare un [collegamento privato](../../private-link/private-link-overview.md) che fornisce sicurezza aggiuntiva sugli endpoint di servizio.

> [!IMPORTANT]
> A causa delle specifiche della configurazione del piano di controllo, la configurazione della subnet assistita dal servizio non Abilita gli endpoint di servizio nei cloud nazionali. 

### <a name="network-requirements"></a>Requisiti di rete

Distribuire Istanza gestita SQL in una subnet dedicata all'interno della rete virtuale. Di seguito sono illustrate le caratteristiche della subnet:

- **Subnet dedicata:** La subnet di SQL Istanza gestita non può contenere altri servizi cloud associati e non può essere una subnet del gateway. La subnet non può contenere risorse, ma SQL Istanza gestita e successivamente non sarà possibile aggiungere altri tipi di risorse nella subnet.
- **Delega subnet:** È necessario delegare la subnet di SQL Istanza gestita al `Microsoft.Sql/managedInstances` provider di risorse.
- **Gruppo di sicurezza di rete (NSG):** Una NSG deve essere associata alla subnet SQL Istanza gestita. È possibile usare un NSG per controllare l'accesso all'endpoint dati di SQL Istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando SQL Istanza gestita è configurato per le connessioni di reindirizzamento. Il servizio eseguirà automaticamente il provisioning e manterrà [le regole](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) correnti necessarie per consentire il flusso ininterrotto del traffico di gestione.
- **Tabella route definita dall'utente (UDR):** È necessario associare una tabella UDR alla subnet SQL Istanza gestita. È possibile aggiungere voci alla tabella di route per indirizzare il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale. Il servizio eseguirà automaticamente il provisioning e manterrà le [voci](#user-defined-routes-with-service-aided-subnet-configuration) correnti necessarie per consentire un flusso ininterrotto del traffico di gestione.
- **Indirizzi IP sufficienti:** La subnet SQL Istanza gestita deve avere almeno 32 indirizzi IP. Per ulteriori informazioni, vedere [determinare le dimensioni della subnet per SQL istanza gestita](vnet-subnet-determine-size.md). È possibile distribuire le istanze gestite nella [rete esistente](vnet-existing-add-subnet.md) dopo averla configurata in modo da soddisfare [i requisiti di rete per SQL istanza gestita](#network-requirements). In caso contrario, creare [una nuova rete e una nuova subnet](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Quando viene creata un'istanza gestita, nella subnet vengono applicati criteri relativi alle finalità di rete per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, vengono rimossi anche i criteri relativi alle finalità di rete.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza in ingresso obbligatorie con la configurazione della subnet con il supporto del servizio

| Nome       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpNetSaw       |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Allow |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza in uscita obbligatorie con la configurazione della subnet con il supporto del servizio

| Nome       |Porta          |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Route definite dall'utente con la configurazione della subnet con il supporto del servizio

|Nome|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|Rete virtuale|
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

\* MI SUBNET fa riferimento all'intervallo di indirizzi IP della subnet nel formato x.x.x.x/y. Queste informazioni sono disponibili nel portale di Azure, nella sezione relativa alle proprietà della subnet.

\** Se l'indirizzo di destinazione è per uno dei servizi di Azure, Azure instrada il traffico direttamente al servizio tramite la rete backbone di Azure, anziché indirizzare il traffico a Internet. Il traffico tra i servizi di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui si trova la rete virtuale o in cui viene distribuita un'istanza del servizio di Azure. Per altri dettagli, vedere la [pagina della documentazione di UdR](../../virtual-network/virtual-networks-udr-overview.md).

È inoltre possibile aggiungere voci alla tabella di route per indirizzare il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale.

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve essere in grado di risolvere i record DNS pubblici. L'uso di funzionalità aggiuntive come Autenticazione di Azure AD, inoltre, potrebbe richiedere la risoluzione di nomi di dominio completi aggiuntivi. Per altre informazioni, vedere [Configurare un DNS personalizzato](custom-dns-configure.md).

### <a name="networking-constraints"></a>Vincoli di rete

**TLS 1.2 viene applicato alle connessioni in uscita** : nel gennaio 2020 Microsoft ha applicato in tutti i servizi di Azure il protocollo TLS 1.2 per il traffico interno al servizio. Per il Istanza gestita SQL di Azure, il risultato è che TLS 1,2 viene applicato alle connessioni in uscita utilizzate per la replica e le connessioni al server collegato SQL Server. Se si usano versioni di SQL Server precedenti a 2016 con SQL Istanza gestita, assicurarsi che siano stati applicati [gli aggiornamenti specifici di TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) .

Le funzionalità di rete virtuale seguenti non sono attualmente supportate con SQL Istanza gestita:

- **Peering Microsoft** : l'abilitazione del [peering Microsoft](../../expressroute/expressroute-faqs.md#microsoft-peering) nei circuiti ExpressRoute con peering diretto o transitivo con una rete virtuale in cui risiede SQL istanza gestita influisce sul flusso del traffico tra i componenti di SQL istanza gestita all'interno della rete virtuale e i servizi da cui dipende, causando problemi di disponibilità. Le distribuzioni di SQL Istanza gestita alla rete virtuale con peering Microsoft già abilitato dovrebbero avere esito negativo.
- **Peering di rete virtuale globale** : la connettività del [peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md) tra le aree di Azure non funziona per le istanze gestite di SQL inserite in subnet create prima del 9/22/2020.
- **AzurePlatformDNS** : se si usa il [tag del servizio](../../virtual-network/service-tags-overview.md) AzurePlatformDNS per bloccare la risoluzione DNS della piattaforma, il rendering di SQL istanza gestita non è disponibile. Anche se SQL Istanza gestita supporta il DNS definito dal cliente per la risoluzione DNS all'interno del motore, esiste una dipendenza dal DNS della piattaforma per le operazioni della piattaforma.
- **Gateway NAT** : l'uso della [rete virtuale di Azure NAT](../../virtual-network/nat-overview.md) per controllare la connettività in uscita con un indirizzo IP pubblico specifico renderebbe SQL istanza gestita non disponibile. Il servizio di Istanza gestita SQL è attualmente limitato all'uso del servizio di bilanciamento del carico di base che non fornisce la coesistenza dei flussi in ingresso e in uscita con la rete virtuale NAT.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Deprecato] Requisiti di rete senza configurazione della subnet con il supporto del servizio

Distribuire Istanza gestita SQL in una subnet dedicata all'interno della rete virtuale. Di seguito sono illustrate le caratteristiche della subnet:

- **Subnet dedicata:** La subnet di SQL Istanza gestita non può contenere altri servizi cloud associati e non può essere una subnet del gateway. La subnet non può contenere risorse, ma SQL Istanza gestita e successivamente non sarà possibile aggiungere altri tipi di risorse nella subnet.
- **Gruppo di sicurezza di rete** : Un gruppo di sicurezza di rete associato alla rete virtuale deve definire [regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [regole di sicurezza in uscita](#mandatory-outbound-security-rules) prima di qualsiasi altra regola. È possibile usare un NSG per controllare l'accesso all'endpoint dati di SQL Istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando SQL Istanza gestita è configurato per le connessioni di reindirizzamento.
- **Tabella delle route definite dall'utente:** una tabella UDR associata alla rete virtuale deve includere determinate [voci](#user-defined-routes).
- **Nessun endpoint servizio:** Nessun endpoint di servizio deve essere associato alla subnet SQL Istanza gestita. Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
- **Indirizzi IP sufficienti:** La subnet SQL Istanza gestita deve avere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [determinare le dimensioni della subnet per SQL istanza gestita](vnet-subnet-determine-size.md). È possibile distribuire le istanze gestite nella [rete esistente](vnet-existing-add-subnet.md) dopo averla configurata in modo da soddisfare [i requisiti di rete per SQL istanza gestita](#network-requirements). In caso contrario, creare [una nuova rete e una nuova subnet](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Non è possibile distribuire una nuova istanza gestita se la subnet di destinazione non dispone di queste caratteristiche. Quando viene creata un'istanza gestita, nella subnet vengono applicati criteri relativi alle finalità di rete per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, vengono rimossi anche i criteri relativi alle finalità di rete.

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
> Verificare che sia presente solo una regola in ingresso per le porte 9000, 9003, 1438, 1440 e 1452 e una regola in uscita per le porte 443 e 12000. Il provisioning di SQL Istanza gestita tramite Azure Resource Manager distribuzioni non riuscirà se le regole in ingresso e in uscita vengono configurate separatamente per ogni porta. Se queste porte sono in regole separate, la distribuzione avrà esito negativo con il codice di errore `VnetSubnetConflictWithIntendedPolicy` .

\* MI SUBNET fa riferimento all'intervallo di indirizzi IP della subnet nel formato x.x.x.x/y. Queste informazioni sono disponibili nel portale di Azure, nella sezione relativa alle proprietà della subnet.

> [!IMPORTANT]
> Anche se le regole di sicurezza in ingresso obbligatorie consentono il traffico da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, queste porte sono protette dal firewall predefinito. Per ulteriori informazioni, vedere [Determinare l'indirizzo dell'endpoint di gestione](management-endpoint-find-ip-address.md).

> [!NOTE]
> Se si utilizza la replica transazionale in SQL Istanza gestita e si utilizza un database di istanza come server di pubblicazione o server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta consentirà infatti l'accesso alla condivisione file di Azure.

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

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](virtual-network-subnet-create-arm-template.md) o una [rete virtuale di Azure esistente](vnet-existing-add-subnet.md) in cui è possibile distribuire SQL istanza gestita.
- Consente [di calcolare le dimensioni della subnet](vnet-subnet-determine-size.md) in cui si desidera distribuire SQL istanza gestita.
- Informazioni su come creare un'istanza gestita:
  - Nel [portale di Azure](instance-create-quickstart.md).
  - Con [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Usando un [modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Con un [modello di Azure Resource Manager (incluso JumpBox con SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
