---
title: Architettura della connettività di un'istanza gestita
description: Informazioni sulle comunicazioni di Istanza gestita di database SQL di Azure e sull'architettura della connettività e il funzionamento dei diversi componenti per indirizzare il traffico a Istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9f341c3c2c299ca358b2a42210f04c6399fe2892
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773608"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architettura della connettività di Istanza gestita di database SQL di Azure

Questo articolo illustra la comunicazione in un'istanza gestita di database SQL di Azure. Descrive inoltre l'architettura di connettività e il modo in cui i componenti indirizzano il traffico all'istanza gestita.  

La funzione Istanza gestita di database SQL di Azure viene inserita nella rete virtuale di Azure e nella subnet dedicata alle istanze gestite. Questa distribuzione fornisce:

- Un indirizzo IP privato sicuro.
- La possibilità di connettere una rete locale a un'istanza gestita.
- La possibilità di connettere un'istanza gestita a un server collegato o a un altro archivio dati locale.
- La possibilità di connettere un'istanza gestita a risorse di Azure.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Nel diagramma seguente vengono illustrate le entità che si connettono a un'istanza gestita, oltre alle risorse che devono comunicare con l'istanza gestita. Il processo di comunicazione raffigurato nella parte inferiore del diagramma rappresenta gli strumenti e le applicazioni dei clienti che si connettono all'istanza gestita come origine dati.  

![Entità nell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Un'istanza gestita è un'offerta PaaS (Platform as a Service). Microsoft usa agenti automatizzati (gestione, distribuzione e manutenzione) per gestire questo servizio in base ai flussi dei dati di telemetria. Poiché la gestione è responsabilità di Microsoft, i clienti non possono accedere alle macchine del cluster virtuale dell'istanza gestita tramite RDP (Remote Desktop Protocol).

Alcune operazioni di SQL Server avviate dalle applicazioni o dagli utenti finali possono richiedere l'interazione di istanze gestite con la piattaforma. Un caso è la creazione di un database di istanze gestite, ovvero una risorsa esposta tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

Le istanze gestite dipendono da servizi di Azure quali Archiviazione di Azure per i backup, Hub eventi di Azure per la telemetria, Azure Active Directory per l'autenticazione, Azure Key Vault per Transparent Data Encryption (TDE) e un paio di servizi della piattaforma Azure che offrono funzionalità di sicurezza e supporto. Le istanze gestite si connettono direttamente a questi servizi.

Tutte le comunicazioni vengono crittografate e firmate mediante certificati. Per verificare l'affidabilità delle parti che comunicano, le istanze gestite controllano costantemente questi certificati rispetto agli elenchi di revoche dei certificati. Se i certificati sono revocati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A livello generale, un'istanza gestita è un set di componenti del servizio, ospitati in un set dedicato di macchine virtuali isolate, che vengono eseguite all'interno della subnet della rete virtuale del cliente. Questi computer formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite e, se necessario, quando il cliente modifica il numero di istanze nella subnet di cui viene effettuato il provisioning, viene automaticamente espanso o ridotto.

Le applicazioni dei clienti possono connettersi a istanze gestite ed eseguire query su un database e aggiornarlo in una rete virtuale, in una rete virtuale con peering o in una rete connessa tramite VPN o Azure ExpressRoute. Questa rete, tuttavia, deve usare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

I servizi di distribuzione e gestione Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e i servizi Microsoft si connettono infatti sugli endpoint che hanno indirizzi IP pubblici. Quando un'istanza gestita crea una connessione in uscita, sul lato del ricevente sembra provenire da questo indirizzo IP pubblico a causa del processo NAT (Network Address Translation).

Il traffico di gestione passa attraverso la rete virtuale del cliente. Questo significa che gli elementi dell'infrastruttura di rete virtuale potrebbero danneggiare il traffico di gestione e l'istanza potrebbe passare a uno stato di errore e smettere di funzionare.

> [!IMPORTANT]
> Per migliorare l'esperienza cliente e la disponibilità dei servizi, Microsoft applica criteri di finalità di rete agli elementi dell'infrastruttura di rete virtuale di Azure che potrebbero influire sul funzionamento delle istanze gestite. Questo meccanismo della piattaforma consente di comunicare i requisiti di rete agli utenti in modo trasparente. Il principale obiettivo dei criteri è quello di impedire un'errata configurazione della rete e garantire il normale funzionamento delle istanze gestite. Quando si elimina un'istanza gestita, vengono rimossi anche i criteri relativi alle finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Verrà ora esaminata in modo più approfondito l'architettura della connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura della connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza gestita usando un nome host con formato `<mi_name>.<dns_zone>.database.windows.net`. Questo nome host viene risolto in un indirizzo IP privato, anche se è registrato in una zona DNS (Domain Name System) pubblica ed è risolvibile pubblicamente. Quando viene creato il cluster, viene automaticamente generato il valore `zone-id`. Se un nuovo cluster ospita un'istanza gestita secondaria, ne condivide l'ID zona con il cluster principale. Per altre informazioni, vedere [Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato appartiene al servizio di bilanciamento del carico interno dell'istanza gestita, che indirizza il traffico al gateway dell'istanza gestita. Poiché possono essere eseguite più istanze gestite nello stesso cluster, il gateway usa il nome host dell'istanza gestita per reindirizzare il traffico al servizio del motore SQL corretto.

I servizi di gestione e distribuzione si connettono a un'istanza gestita usando un [endpoint di gestione](#management-endpoint) mappato a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set di porte predefinite usate esclusivamente dai componenti di gestione dell'istanza gestita. Per consentire il traffico solo da intervalli IP specifici di Microsoft, viene inoltre configurato un firewall incorporato nei nodi. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite i certificati.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita tramite un endpoint di gestione, che si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall incorporato a livello di rete mentre, a livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [Determinare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando viene avviata una connessione all'interno dell'istanza gestita, ad esempio per accedere ai backup o ai log di controllo, il traffico sembra partire dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso a servizi pubblici da un'istanza gestita impostando le regole del firewall per consentire solo l'indirizzo IP dell'istanza gestita. Per altre informazioni, vedere [Verifica del firewall predefinito in un'istanza gestita](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Il traffico indirizzato ai servizi di Azure che si trovano nell'area dell'istanza gestita è ottimizzato e, pertanto, non ne viene eseguito il NAT sull'indirizzo IP pubblico dell'endpoint di gestione delle istanze gestite. Per questo motivo, se è necessario usare le regole del firewall IP, in genere a scopo di archiviazione, il servizio deve trovarsi in un'area diversa rispetto all'istanza gestita.

## <a name="service-aided-subnet-configuration"></a>Configurazione della subnet con il supporto del servizio

Per soddisfare i requisiti di sicurezza e gestibilità dei clienti, Istanza gestita passerà dalla configurazione manuale alla configurazione con subnet assistita dal servizio.

Con la configurazione della subnet assistita dal servizio, l'utente ha il controllo completo del traffico dati (TDS), mentre Istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per soddisfare il contratto di servizio.

La configurazione della subnet assistita dal servizio si basa sulla funzionalità di [delega della subnet](../virtual-network/subnet-delegation-overview.md) rete virtuale per fornire la gestione automatica della configurazione di rete e abilitare gli endpoint di servizio. Gli endpoint di servizio possono essere usati per configurare le regole del firewall della rete virtuale negli account di archiviazione che conservano i backup e i log di controllo.

### <a name="network-requirements"></a>Requisiti di rete 

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. Di seguito sono illustrate le caratteristiche della subnet:

- **Subnet dedicata:** la subnet dell'istanza gestita non deve contenere altri servizi cloud associati e non deve essere una subnet del gateway. La subnet non può contenere alcuna risorsa al di fuori dell'istanza gestita e in seguito non è possibile aggiungere altri tipi di risorse nella subnet.
- **Delega di subnet**: La subnet dell'istanza gestita deve essere delegata al provider di risorse `Microsoft.Sql/managedInstances`.
- **Gruppo di sicurezza di rete**: è necessario associare un gruppo di sicurezza di rete alla subnet dell'istanza gestita. È possibile usare un gruppo di sicurezza di rete per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per le connessioni di reindirizzamento. Il servizio eseguirà automaticamente il provisioning e manterrà le [regole](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) correnti necessarie per consentire un flusso ininterrotto del traffico di gestione.
- **Tabella delle route definite dall'utente:** è necessario associare una tabella UDR alla subnet dell'istanza gestita. È possibile aggiungere voci alla tabella di route per indirizzare il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale. Il servizio eseguirà automaticamente il provisioning e manterrà le [voci](#user-defined-routes-with-service-aided-subnet-configuration) correnti necessarie per consentire un flusso ininterrotto del traffico di gestione.
- **Indirizzi IP sufficienti:** la subnet dell'istanza gestita deve contenere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire le istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata per soddisfare i [requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare [una nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

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

È inoltre possibile aggiungere voci alla tabella di route per indirizzare il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale.

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve poter risolvere record DNS pubblici. L'uso di funzionalità aggiuntive come Autenticazione di Azure AD, inoltre, potrebbe richiedere la risoluzione di nomi di dominio completi aggiuntivi. Per altre informazioni, vedere [Configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Vincoli di rete

**TLS 1.2 viene applicato alle connessioni in uscita**: nel gennaio 2020 Microsoft ha applicato in tutti i servizi di Azure il protocollo TLS 1.2 per il traffico interno al servizio. Per le istanze gestite di database SQL di Azure, è stato quindi applicato il protocollo TLS 1.2 sia per le connessioni in uscita usate per la replica sia per le connessioni del server collegato a SQL Server. Se con Istanza gestita si usano versioni di SQL Server precedenti a 2016, assicurarsi che siano stati applicati gli [aggiornamenti specifici di TLS 1.2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Con Istanza gestita non sono attualmente supportate le funzionalità della rete virtuale seguenti:
- **Peering Microsoft**: L'abilitazione della funzione di [peering Microsoft](../expressroute/expressroute-faqs.md#microsoft-peering) in circuiti ExpressRoute con peering diretto o transitivo con la rete virtuale in cui si trova Istanza gestita influisce sul flusso del traffico tra i componenti di Istanza gestita all'interno della rete virtuale e i servizi da cui dipende, generando problemi di disponibilità. Le distribuzioni di Istanza gestita in una rete virtuale con il peering Microsoft già abilitato hanno in genere esito negativo.
- **Peering di rete virtuale globale**: la connettività del [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) tra aree di Azure non può essere usata con Istanza gestita a causa di [vincoli di bilanciamento del carico documentati](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: L'uso del [tag di servizio](../virtual-network/service-tags-overview.md) AzurePlatformDNS per bloccare la risoluzione DNS della piattaforma renderebbe non disponibile Istanza gestita. Sebbene Istanza gestita supporti un server DNS definito dal cliente per la risoluzione DNS all'interno del motore, esiste una dipendenza dal DNS della piattaforma per le operazioni della piattaforma.
- **Gateway NAT**: l'uso di [NAT di rete virtuale](../virtual-network/nat-overview.md) per controllare la connettività in uscita con uno specifico indirizzo IP pubblico renderebbe non disponibile Istanza gestita. Il servizio Istanza gestita è attualmente limitato all'uso del servizio di bilanciamento del carico di base, che non prevede la coesistenza di flussi in ingresso o in uscita con la rete virtuale NAT.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Deprecato] Requisiti di rete senza configurazione della subnet con il supporto del servizio

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. Di seguito sono illustrate le caratteristiche della subnet:

- **Subnet dedicata:** la subnet dell'istanza gestita non deve contenere altri servizi cloud associati e non deve essere una subnet del gateway. La subnet non può contenere alcuna risorsa al di fuori dell'istanza gestita e in seguito non è possibile aggiungere altri tipi di risorse nella subnet.
- **Gruppo di sicurezza di rete**: Un gruppo di sicurezza di rete associato alla rete virtuale deve definire [regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [regole di sicurezza in uscita](#mandatory-outbound-security-rules) prima di qualsiasi altra regola. È possibile usare un gruppo di sicurezza di rete per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per le connessioni di reindirizzamento.
- **Tabella delle route definite dall'utente:** una tabella UDR associata alla rete virtuale deve includere determinate [voci](#user-defined-routes).
- **Nessun endpoint di servizio**: alla subnet dell'istanza gestita non deve essere associato alcun endpoint di servizio. Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
- **Indirizzi IP sufficienti:** la subnet dell'istanza gestita deve contenere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire le istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata per soddisfare i [requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare [una nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

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
> Verificare che sia presente solo una regola in ingresso per le porte 9000, 9003, 1438, 1440, 1452 e una regola in uscita per le porte 443, 12000. Il provisioning di Istanza gestita tramite distribuzioni di Azure Resource Manager non riuscirà se per ogni porta sono state configurate separatamente regole in ingresso e in uscita. Se a queste porte sono associate regole separate, la distribuzione avrà esito negativo con codice di errore `VnetSubnetConflictWithIntendedPolicy`

\* MI SUBNET fa riferimento all'intervallo di indirizzi IP della subnet nel formato x.x.x.x/y. Queste informazioni sono disponibili nel portale di Azure, nella sezione relativa alle proprietà della subnet.

> [!IMPORTANT]
> Anche se le regole di sicurezza in ingresso obbligatorie consentono il traffico da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, queste porte sono protette dal firewall predefinito. Per ulteriori informazioni, vedere [Determinare l'indirizzo dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se si usa la replica transazionale in un'istanza gestita di e un database di istanza come server di pubblicazione o di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta consentirà infatti l'accesso alla condivisione file di Azure.

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

- Per una panoramica, vedere  [Sicurezza dei dati avanzata di database SQL](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o una [rete virtuale di Azure esistente](sql-database-managed-instance-configure-vnet-subnet.md) in cui sia possibile distribuire istanze gestite.
- [Calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si intende distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:
  - Nel [portale di Azure](sql-database-managed-instance-get-started.md).
  - Con [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando un [modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Con un [modello di Azure Resource Manager (incluso JumpBox con SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
