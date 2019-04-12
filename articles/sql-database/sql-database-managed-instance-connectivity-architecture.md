---
title: Architettura della connettività per un'istanza gestita di Database SQL di Azure | Microsoft Docs
description: Informazioni sulle comunicazioni istanza Database SQL di Azure gestiti e anche l'architettura della connettività come i componenti di indirizzano il traffico all'istanza gestita.
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
ms.date: 02/26/2019
ms.openlocfilehash: 82b533f7293e00469a5b92b02e8d58967379a585
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497067"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architettura della connettività per un'istanza gestita di Database SQL di Azure

Questo articolo illustra le comunicazioni in un'istanza gestita di Database SQL di Azure. Vengono inoltre descritte architettura della connettività e modo in cui i componenti di indirizzano il traffico all'istanza gestita.  

L'istanza gestita di Database SQL viene inserito all'interno di rete virtuale di Azure e la subnet dedicata nelle istanze gestite. Questa distribuzione include:

- Un indirizzo IP privato protetto.
- La possibilità di connettere una rete locale a un'istanza gestita.
- La possibilità di connettersi a un'istanza gestita a un server collegato o un altro archivio dati in-premise.
- La possibilità di connettersi a un'istanza gestita per le risorse di Azure.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Il diagramma seguente mostra le entità che si connettono a un'istanza gestita. Indica anche le risorse necessarie per comunicare con l'istanza gestita. Il processo di comunicazione nella parte inferiore del diagramma rappresenta le applicazioni dei clienti e gli strumenti che si connettono all'istanza gestita come origini dati.  

![Entità nell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Un'istanza gestita è una piattaforma come un'offerta di servizio (PaaS). Microsoft Usa automatizzati agents (gestione, distribuzione e manutenzione) per gestire questo servizio basato su flussi di dati di telemetria. Poiché Microsoft è responsabile della gestione, i clienti non possono accedere le macchine virtuali del cluster di istanza gestita tramite Remote Desktop Protocol (RDP).

Alcune operazioni avviate da applicazioni o gli utenti finali potrebbero richiedere di SQL Server gestito le istanze per interagire con la piattaforma. Un caso è la creazione di un database in istanza gestita. Questa risorsa viene esposta tramite il portale di Azure, PowerShell, CLI di Azure e l'API REST.

Le istanze gestite dipendono da servizi di Azure come archiviazione di Azure per i backup, hub eventi di Azure per i dati di telemetria, Azure Active Directory per l'autenticazione, Azure Key Vault per Transparent Data Encryption (TDE) e un paio di servizi della piattaforma Azure che forniscono funzionalità di sicurezza e al supporto. Le istanze gestite stabilisce le connessioni a tali servizi.

Tutte le comunicazioni vengono crittografate e firmati mediante certificati. Per verificare l'attendibilità delle parti, gestite in comunicazione istanze verificare costantemente questi certificati tramite gli elenchi di revoche di certificati. Se i certificati vengono revocati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A livello generale, un'istanza gestita è un set di componenti del servizio. Questi componenti sono ospitati in un set dedicato di macchine virtuali isolate eseguite all'interno di subnet della rete virtuale del cliente. Queste macchine formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite. Se necessario, il cluster viene automaticamente espande o comprime quando il cliente modifica il numero di istanze sottoposte a provisioning nella subnet.

Le applicazioni dei clienti possono connettersi a istanze gestite e possono eseguire una query e aggiornare i database all'interno della rete virtuale, rete virtuale con peering, o una rete connessa tramite VPN o ExpressRoute di Azure. Questa rete deve utilizzare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Servizi di distribuzione e gestione di Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e servizi Microsoft di connettersi tramite gli endpoint che dispongono di indirizzi IP pubblici. Quando un'istanza gestita consente di creare una connessione in uscita, estremità ricevente rende Network Address Translation (NAT) l'aspetto di connessione proviene da questo indirizzo IP pubblico.

Gestione traffico scorre attraverso la rete virtuale del cliente. Ciò significa che gli elementi dell'infrastruttura della rete virtuale possono danneggiare il traffico di gestione, rendendo l'istanza di esito negativo e non saranno più disponibili.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e disponibilità dei servizi, Microsoft applicherà i criteri preventivo rete sugli elementi dell'infrastruttura di rete virtuale di Azure. I criteri possono influire sul funzionamento di istanza gestita. Questo meccanismo platform comunica in modo trasparente i requisiti di rete per gli utenti. L'obiettivo principale del criterio è per evitare errori di configurazione di rete e per assicurare il funzionamento normale istanza gestita. Quando si elimina un'istanza gestita, viene rimosso anche il criterio preventivi di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Diamo un esame approfondito nell'architettura di connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura della connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza gestita usando un nome host che ha il formato `<mi_name>.<dns_zone>.database.windows.net`. Questo nome host si risolve un indirizzo IP privato anche se è registrato in una zona di dominio pubblico Name System (DNS) ed è risolvibile pubblicamente. Il `zone-id` viene generato automaticamente quando si crea il cluster. Se un cluster appena creato ospita un'istanza gestita secondaria, condivide il relativo ID della zona con il cluster primario. Per altre informazioni, vedere [usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato a cui appartiene al servizio di bilanciamento del carico interno dell'istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway dell'istanza gestita. Poiché più istanze gestite possono essere eseguiti all'interno dello stesso cluster, nome host dell'istanza gestita il gateway Usa per reindirizzare il traffico al servizio del motore SQL corretto.

Servizi di distribuzione e gestione di connettersi a un'istanza gestita usando un [endpoint di gestione](#management-endpoint) che esegue il mapping a un riferimento esterno bilanciamento del carico. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set predefinito di porte che utilizzano solo i componenti di gestione dell'istanza gestita. Un firewall incorporato nei nodi è configurare per consentire il traffico solo da intervalli di indirizzi IP di Microsoft. I certificati si escludono a vicenda autenticano tutte le comunicazioni tra i componenti di gestione e il piano di gestione.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita usando un endpoint di gestione. Questo endpoint si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall a livello di rete predefinito. A livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [determinare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando avviare le connessioni all'interno dell'istanza gestita (come con i backup e i log di controllo), il traffico viene visualizzata per l'avvio dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso a servizi pubblici da un'istanza gestita impostando le regole del firewall per consentire solo indirizzo IP dell'istanza gestita. Per altre informazioni, vedere [firewall incorporato dell'istanza gestita di verificare](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Traffice destinati a servizi di Azure che si trovano all'interno di aree dell'istanza gestita è ottimizzata e per tale motivo non fornisca all'indirizzo IP pubblico di istanza possono Gestione endpoint. Per questo motivo se è necessario usare le regole del firewall basato su IP, più comunemente per l'archiviazione del servizio deve essere in un'area diversa dall'istanza gestita.

## <a name="network-requirements"></a>Requisiti di rete

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le seguenti caratteristiche:

- **Subnet dedicata:** Subnet dell'istanza gestita non può contenere qualsiasi altro servizio cloud che è associato, e non può essere una subnet del gateway. La subnet non può contenere qualsiasi risorsa, ma l'istanza gestita e non è possibile aggiungere in un secondo momento le risorse nella subnet.
- **Gruppo di sicurezza di rete**: È necessario definire un gruppo di sicurezza che è associati con la rete virtuale [regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [regole di sicurezza in uscita](#mandatory-outbound-security-rules) prima di qualsiasi altra regola. È possibile usare un NSG per controllare l'accesso all'endpoint di dati dell'istanza gestita filtrando il traffico sulla porta 1433 e le porte 11000-11999 quando l'istanza gestita è configurata per reindirizzare le connessioni.
- **Tabella di route definita (UDR) utente:** Una tabella di route definita dall'utente che ha associato con la rete virtuale deve includere specifiche [voci](#user-defined-routes).
- **Nessun endpoint di servizio:** Nessun endpoint di servizio deve essere associato a subnet dell'istanza gestita. Assicurarsi che l'opzione endpoint di servizio viene disabilitato quando si crea la rete virtuale.
- **Indirizzi IP sufficienti:** Subnet dell'istanza gestita deve avere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per altre informazioni, vedere [determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire le istanze gestite nel [della rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo la configurazione per soddisfare [requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare un [nuova rete e subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Se la subnet di destinazione non dispone di queste caratteristiche, è possibile distribuire una nuova istanza gestita. Quando si crea un'istanza gestita, viene applicato un criterio di finalità di rete nella subnet per impedire modifiche non conformi alla configurazione di rete. Dopo l'ultima istanza viene rimossa dalla subnet, viene rimosso anche il criterio preventivi di rete.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie

| Attività       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |MI SUBNET  |CONSENTI |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |CONSENTI |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |CONSENTI |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie

| Attività       |Porta          |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |MI SUBNET        |AzureCloud |CONSENTI |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |CONSENTI |

> [!IMPORTANT]
> Assicurarsi che ci sia solo una regola in ingresso per le porte 9000, 9003, 1438, 1440, 1452 e una regola in uscita per le porte 80, 443, 12000. Gestito tramite Azure Resource Manager, le distribuzioni avrà esito negativo se le regole in ingresso e di output vengono configurate separatamente per ogni porta di provisioning dell'istanza. Se queste porte sono regole distinte, la distribuzione avrà esito negativo con codice di errore `VnetSubnetConflictWithIntendedPolicy`

\* SUBNET MI fa riferimento all'intervallo di indirizzi IP per la subnet in 10.x.x.x/y il form. È possibile trovare queste informazioni nel portale di Azure, nelle proprietà di subnet.

> [!IMPORTANT]
> Anche se le regole di sicurezza in ingresso necessario consentano il traffico dagli _qualsiasi_ sulle porte di origine 9000, 9003, 1438, 1440 e 1452, queste porte sono protette da un firewall incorporato. Per altre informazioni, vedere [determinare l'indirizzo dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Se si usa la replica transazionale in un'istanza gestita e se si usa qualsiasi database di istanza come server di pubblicazione o un server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta verrà concesso l'accesso alla condivisione file di Azure.

### <a name="user-defined-routes"></a>route definite dall'utente

|Attività|Prefisso indirizzo|Hop successivo|
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
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
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
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
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
||||

Inoltre, è possibile aggiungere voci alla tabella di route per instradare il traffico con gli intervalli IP privati in locale come destinazione tramite il gateway di rete virtuale o appliance di rete virtuale (NVA).

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve essere in grado di risolvere i nomi host in \*. core.windows.net zona. Usando le funzionalità aggiuntive, ad esempio autenticazione di Azure AD potrebbe richiedere la risoluzione di altri nomi di dominio completi. Per altre informazioni, vedere [configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Database SQL di protezione dati avanzata](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o un' [rete virtuale di Azure esistente](sql-database-managed-instance-configure-vnet-subnet.md) dove è possibile distribuire le istanze gestite.
- [Calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si desidera distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:
  - Nel [portale di Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando [un modello Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [un modello di Azure Resource Manager (tramite JumpBox, con SQL Server Management Studio incluso)](https://portal.azure.com/). 
