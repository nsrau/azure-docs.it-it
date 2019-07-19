---
title: Architettura di connettività per un'istanza gestita nel database SQL di Azure | Microsoft Docs
description: Informazioni sulla comunicazione dell'istanza gestita di database SQL di Azure e sull'architettura di connettività, nonché sul modo in cui i componenti indirizzano il traffico all'istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 960320e280a613a537f1918d93e4584a13a0b374
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309967"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architettura di connettività per un'istanza gestita nel database SQL di Azure

Questo articolo illustra la comunicazione in un'istanza gestita di database SQL di Azure. Viene inoltre descritta l'architettura di connettività e il modo in cui i componenti indirizzano il traffico all'istanza gestita.  

L'istanza gestita di database SQL è posizionata all'interno della rete virtuale di Azure e della subnet dedicata alle istanze gestite. Questa distribuzione fornisce:

- Un indirizzo IP privato protetto.
- Possibilità di connettere una rete locale a un'istanza gestita.
- Possibilità di connettere un'istanza gestita a un server collegato o a un altro archivio dati locale.
- Possibilità di connettere un'istanza gestita a risorse di Azure.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Nel diagramma seguente vengono illustrate le entità che si connettono a un'istanza gestita. Vengono inoltre illustrate le risorse che devono comunicare con l'istanza gestita. Il processo di comunicazione nella parte inferiore del diagramma rappresenta le applicazioni e gli strumenti del cliente che si connettono all'istanza gestita come origini dati.  

![Entità nell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Un'istanza gestita è un'offerta di piattaforma distribuita come servizio (PaaS). Microsoft usa agenti automatizzati (gestione, distribuzione e manutenzione) per gestire questo servizio in base ai flussi di dati di telemetria. Poiché Microsoft è responsabile della gestione, i clienti non possono accedere ai computer del cluster virtuale dell'istanza gestita tramite Remote Desktop Protocol (RDP).

Alcune SQL Server operazioni avviate dagli utenti finali o dalle applicazioni potrebbero richiedere che le istanze gestite possano interagire con la piattaforma. Un caso è la creazione di un database di istanza gestita. Questa risorsa viene esposta tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

Le istanze gestite dipendono da servizi di Azure, ad esempio archiviazione di Azure per i backup, Hub eventi di Azure per la telemetria, Azure Active Directory per l'autenticazione, Azure Key Vault per Transparent Data Encryption (Transparent Data Encryption) e un paio di servizi della piattaforma Azure che forniscono funzionalità di sicurezza e supporto. Le istanze gestite consentono di stabilire connessioni a tali servizi.

Tutte le comunicazioni vengono crittografate e firmate tramite certificati. Per verificare l'affidabilità delle parti che comunicano, le istanze gestite verificano costantemente questi certificati tramite gli elenchi di revoche di certificati. Se i certificati vengono revocati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A un livello elevato, un'istanza gestita è un set di componenti del servizio. Questi componenti sono ospitati in un set dedicato di macchine virtuali isolate che vengono eseguite all'interno della subnet della rete virtuale del cliente. Questi computer formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite. Se necessario, il cluster si espande automaticamente o si contrae quando il cliente modifica il numero di istanze di cui è stato effettuato il provisioning nella subnet.

Le applicazioni dei clienti possono connettersi a istanze gestite e possono eseguire query e aggiornare i database all'interno della rete virtuale, della rete virtuale con peering o della rete connessa tramite VPN o Azure ExpressRoute. Questa rete deve usare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

I servizi di distribuzione e gestione Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e i servizi Microsoft si connettono sugli endpoint che hanno indirizzi IP pubblici. Quando un'istanza gestita crea una connessione in uscita, alla ricezione della connessione NAT (Network Address Translation) finale la connessione risulta simile a quella proveniente da questo indirizzo IP pubblico.

Il traffico di gestione fluisce attraverso la rete virtuale del cliente. Questo significa che gli elementi dell'infrastruttura della rete virtuale possono danneggiare il traffico di gestione rendendo l'istanza non riuscita e diventare non disponibile.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e la disponibilità dei servizi, Microsoft applica criteri per finalità di rete negli elementi dell'infrastruttura di rete virtuale di Azure. I criteri possono influire sul funzionamento dell'istanza gestita. Questo meccanismo della piattaforma comunica in modo trasparente i requisiti di rete agli utenti. L'obiettivo principale del criterio è impedire la configurazione errata della rete e garantire le normali operazioni dell'istanza gestita. Quando si elimina un'istanza gestita, vengono rimossi anche i criteri per finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Si esaminerà in dettaglio l'architettura di connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura di connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza gestita utilizzando un nome host con `<mi_name>.<dns_zone>.database.windows.net`il formato. Questo nome host viene risolto in un indirizzo IP privato anche se è registrato in una zona DNS (Public Domain Name System) ed è risolvibile pubblicamente. `zone-id` Viene generato automaticamente quando si crea il cluster. Se un cluster appena creato ospita un'istanza gestita secondaria, condivide il relativo ID di zona con il cluster primario. Per altre informazioni, vedere [usare i gruppi di failover automatico per abilitare il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato appartiene al servizio di bilanciamento del carico interno dell'istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway dell'istanza gestita. Poiché più istanze gestite possono essere eseguite all'interno dello stesso cluster, il gateway usa il nome host dell'istanza gestita per reindirizzare il traffico al servizio motore SQL corretto.

I servizi di gestione e distribuzione si connettono a un'istanza gestita tramite un [endpoint di gestione](#management-endpoint) che esegue il mapping a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set predefinito di porte utilizzate solo dai componenti di gestione dell'istanza gestita. Un firewall integrato nei nodi viene configurato per consentire il traffico solo da intervalli IP Microsoft. I certificati autenticano reciprocamente tutte le comunicazioni tra i componenti di gestione e il piano di gestione.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita tramite un endpoint di gestione. Questo endpoint si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall incorporato a livello di rete. A livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [determinare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando le connessioni vengono avviate all'interno dell'istanza gestita, ad esempio i backup e i log di controllo, il traffico sembra iniziare dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso ai servizi pubblici da un'istanza gestita impostando le regole del firewall in modo da consentire solo l'indirizzo IP dell'istanza gestita. Per ulteriori informazioni, vedere [verificare il firewall incorporato dell'istanza gestita](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Il traffico che passa ai servizi di Azure che si trovano all'interno dell'area dell'istanza gestita è ottimizzato e per questo motivo non è stato gestito con l'indirizzo IP pubblico dell'endpoint di gestione delle istanze gestite. Per questo motivo, se è necessario usare le regole del firewall basate su IP, in genere per l'archiviazione, il servizio deve trovarsi in un'area diversa rispetto all'istanza gestita.

## <a name="network-requirements"></a>Requisiti di rete

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le caratteristiche seguenti:

- **Subnet dedicata:** La subnet dell'istanza gestita non può contenere nessun altro servizio cloud associato e non può essere una subnet del gateway. La subnet non può contenere alcuna risorsa ma l'istanza gestita e successivamente non sarà possibile aggiungere altri tipi di risorse nella subnet.
- **Gruppo di sicurezza di rete**: Un NSG associato alla rete virtuale deve definire [le regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [le regole di sicurezza](#mandatory-outbound-security-rules) in uscita prima di qualsiasi altra regola. È possibile usare un NSG per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per le connessioni di reindirizzamento.
- **Tabella route definita dall'utente (UDR):** Una tabella UDR associata alla rete virtuale deve includere [voci](#user-defined-routes)specifiche.
- **Nessun endpoint servizio:** Nessun endpoint di servizio deve essere associato alla subnet dell'istanza gestita. Assicurarsi che l'opzione endpoint di servizio sia disabilitata quando si crea la rete virtuale.
- **Indirizzi IP sufficienti:** La subnet dell'istanza gestita deve contenere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata in modo da soddisfare [i requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare una [nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Non è possibile distribuire una nuova istanza gestita se la subnet di destinazione non dispone di queste caratteristiche. Quando si crea un'istanza gestita, nella subnet viene applicato un criterio di finalità della rete per evitare modifiche non conformi alla configurazione della rete. Una volta rimossa l'ultima istanza dalla subnet, viene rimosso anche il criterio per finalità di rete.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie

| Name       |Port                        |Protocol|Source           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Any              |MI SUBNET  |Allow |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie

| Name       |Port          |Protocol|Source           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> Verificare che sia presente solo una regola in ingresso per le porte 9000, 9003, 1438, 1440, 1452 e una regola in uscita per le porte 80, 443, 12000. Istanza gestita il provisioning tramite Azure Resource Manager distribuzioni non riuscirà se le regole in ingresso e in uscita vengono configurate separatamente per ogni porta. Se queste porte sono in regole separate, la distribuzione avrà esito negativo con codice di errore`VnetSubnetConflictWithIntendedPolicy`

\*La SUBNET MI fa riferimento all'intervallo di indirizzi IP per la subnet nel formato 10. x.x. x/y. È possibile trovare queste informazioni nella portale di Azure, in proprietà subnet.

> [!IMPORTANT]
> Sebbene le regole di sicurezza in ingresso obbligatorie consentano il traffico da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, queste porte sono protette da un firewall incorporato. Per ulteriori informazioni, vedere [determinare l'indirizzo dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Se si utilizza la replica transazionale in un'istanza gestita di e si utilizza un database di istanza come server di pubblicazione o server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta consentirà l'accesso alla condivisione file di Azure.

### <a name="user-defined-routes"></a>route definite dall'utente

|Name|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|Rete virtuale|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-NextHop-Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-NextHop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-NextHop-Internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-NextHop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-NextHop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-22-NextHop-Internet|103.25.156.0/22|Internet|
|Mi-103-36-96-22-NextHop-Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-NextHop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-NextHop-Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-NextHop-Internet|150.242.48.0/22|Internet|
|mi-157-54-15-NextHop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-NextHop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|Mi-192-32-16-NextHop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-NextHop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-NextHop-Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|Mi-192-100-103-24-NextHop-Internet|192.100.103.0/24|Internet|
|Mi-192-197-157-24-NextHop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-NextHop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-NextHop-Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-NextHop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-NextHop-Internet|199.103.122.0/24|Internet|
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
||||

Inoltre, è possibile aggiungere voci alla tabella di route per instradare il traffico con intervalli di indirizzi IP privati locali come destinazione tramite il gateway di rete virtuale o l'appliance di rete virtuale (NVA).

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve essere in grado di risolvere i record DNS pubblici. L'uso di funzionalità aggiuntive come Autenticazione di Azure AD potrebbe richiedere la risoluzione di nomi di dominio completi aggiuntivi. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [sicurezza dei dati avanzata del database SQL](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o una [rete virtuale di Azure esistente](sql-database-managed-instance-configure-vnet-subnet.md) in cui è possibile distribuire istanze gestite.
- Consente [di calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si desidera distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:
  - Nel [portale di Azure](sql-database-managed-instance-get-started.md).
  - Tramite [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Utilizzando [un modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [un modello di Azure Resource Manager (usando JumpBox, con SSMS incluso)](https://portal.azure.com/). 
