---
title: Architettura della connettività per un'istanza gestita di Database SQL di Azure | Microsoft Docs
description: Informazioni sulle comunicazioni istanza Database SQL di Azure gestiti e anche l'architettura della connettività come i componenti di indirizzano il traffico all'istanza gestita.
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
ms.date: 02/26/2019
ms.openlocfilehash: 6ef020ff1054416e2b9af5af824b9aa27f0b1e64
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247240"
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

Le istanze gestite dipendono da servizi di Azure, ad esempio archiviazione di Azure per i backup, il Bus di servizio di Azure per i dati di telemetria, Azure Active Directory per l'autenticazione e Azure Key Vault per Transparent Data Encryption (TDE). Le istanze gestite di stabilire connessioni a tali servizi.

Tutte le comunicazioni utilizzano i certificati per la crittografia e firma. Per verificare l'attendibilità delle parti, gestite in comunicazione istanze verificare costantemente questi certificati contattando un'autorità di certificazione. Se i certificati vengono revocati o non possono essere verificati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A livello generale, un'istanza gestita è un set di componenti del servizio. Questi componenti sono ospitati in un set dedicato di macchine virtuali isolate eseguite all'interno di subnet della rete virtuale del cliente. Queste macchine formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite. Se necessario, il cluster viene automaticamente espande o comprime quando il cliente modifica il numero di istanze sottoposte a provisioning nella subnet.

Le applicazioni dei clienti possono connettersi a istanze gestite e possono eseguire query e aggiornamento database solo se vengono eseguite all'interno della rete virtuale, virtuale con peering rete o connesso tramite VPN o ExpressRoute di Azure. Questa rete deve utilizzare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura della connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Servizi di distribuzione e gestione di Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e servizi Microsoft di connettersi tramite gli endpoint che dispongono di indirizzi IP pubblici. Quando un'istanza gestita consente di creare una connessione in uscita, estremità ricevente rende Network Address Translation (NAT) l'aspetto di connessione proviene da questo indirizzo IP pubblico.

Gestione traffico scorre attraverso la rete virtuale del cliente. Ciò significa che gli elementi dell'infrastruttura della rete virtuale possono danneggiare il traffico di gestione, rendendo l'istanza di esito negativo e non saranno più disponibili.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e disponibilità dei servizi, Microsoft applicherà i criteri preventivo rete sugli elementi dell'infrastruttura di rete virtuale di Azure. I criteri possono influire sul funzionamento di istanza gestita. Questo meccanismo platform comunica in modo trasparente i requisiti di rete per gli utenti. L'obiettivo principale del criterio è per evitare errori di configurazione di rete e per assicurare il funzionamento normale istanza gestita. Quando si elimina un'istanza gestita, viene rimosso anche il criterio preventivi di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Diamo un esame approfondito nell'architettura di connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura della connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza gestita usando un nome host che ha il formato `<mi_name>.<dns_zone>.database.windows.net`. Questo nome host si risolve un indirizzo IP privato anche se è registrato in una zona di dominio pubblico Name System (DNS) ed è risolvibile pubblicamente. Il `zone-id` viene generato automaticamente quando si crea il cluster. Se un cluster appena creato ospita un'istanza gestita secondaria, condivide il relativo ID della zona con il cluster primario. Per altre informazioni, vedere [usare i gruppi con failover automatico per consentire il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato a cui appartiene al servizio di bilanciamento del carico interno dell'istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway dell'istanza gestita. Poiché più istanze gestite possono essere eseguiti all'interno dello stesso cluster, nome host dell'istanza gestita il gateway Usa per reindirizzare il traffico al servizio del motore SQL corretto.

Servizi di distribuzione e gestione di connettersi a un'istanza gestita usando un [endpoint di gestione](#management-endpoint) che esegue il mapping a un riferimento esterno bilanciamento del carico. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set predefinito di porte che utilizzano solo i componenti di gestione dell'istanza gestita. Un firewall incorporato nei nodi è configurare per consentire il traffico solo da intervalli di indirizzi IP di Microsoft. I certificati si escludono a vicenda autenticano tutte le comunicazioni tra i componenti di gestione e il piano di gestione.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita usando un endpoint di gestione. Questo endpoint si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall a livello di rete predefinito. A livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [determinare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando avviare le connessioni all'interno dell'istanza gestita (come con i backup e i log di controllo), il traffico viene visualizzata per l'avvio dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso a servizi pubblici da un'istanza gestita impostando le regole del firewall per consentire solo indirizzo IP dell'istanza gestita. Per altre informazioni, vedere [firewall incorporato dell'istanza gestita di verificare](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> A differenza del firewall per le connessioni che iniziano all'interno dell'istanza gestita, i servizi di Azure che si trovano all'interno di aree dell'istanza gestita dispone di un firewall che è ottimizzato per il traffico che passa tra questi servizi.

## <a name="network-requirements"></a>Requisiti di rete

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le seguenti caratteristiche:

- **Subnet dedicata:** Subnet dell'istanza gestita non può contenere qualsiasi altro servizio cloud che è associato, e non può essere una subnet del gateway. La subnet non può contenere qualsiasi risorsa, ma l'istanza gestita e non è possibile aggiungere in un secondo momento le risorse nella subnet.
- **Gruppo di sicurezza di rete**: È necessario definire un gruppo di sicurezza che è associati con la rete virtuale [regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [regole di sicurezza in uscita](#mandatory-outbound-security-rules) prima di qualsiasi altra regola. È possibile usare un NSG per controllare l'accesso all'endpoint di dati dell'istanza gestita filtrando il traffico sulla porta 1433.
- **Tabella di route definita (UDR) utente:** Una tabella di route definita dall'utente che ha associato con la rete virtuale deve includere specifiche [voci](#user-defined-routes).
- **Nessun endpoint di servizio:** Nessun endpoint di servizio deve essere associato a subnet dell'istanza gestita. Assicurarsi che l'opzione endpoint di servizio viene disabilitato quando si crea la rete virtuale.
- **Indirizzi IP sufficienti:** Subnet dell'istanza gestita deve avere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per altre informazioni, vedere [determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire le istanze gestite nel [della rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo la configurazione per soddisfare [requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare un [nuova rete e subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Se la subnet di destinazione non dispone di queste caratteristiche, è possibile distribuire una nuova istanza gestita. Quando si crea un'istanza gestita, viene applicato un criterio di finalità di rete nella subnet per impedire modifiche non conformi alla configurazione di rete. Dopo l'ultima istanza viene rimossa dalla subnet, viene rimosso anche il criterio preventivi di rete.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie

| NOME       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |Qualsiasi        |CONSENTI |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |Qualsiasi        |CONSENTI |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|Qualsiasi        |CONSENTI |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie

| NOME       |Porta          |Protocollo|Source (Sorgente)           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |Qualsiasi              |Internet   |CONSENTI |
|mi_subnet   |Qualsiasi           |Qualsiasi     |Qualsiasi              |MI SUBNET *  |CONSENTI |

\* SUBNET MI fa riferimento all'intervallo di indirizzi IP per la subnet in 10.x.x.x/y il form. È possibile trovare queste informazioni nel portale di Azure, nelle proprietà di subnet.

> [!IMPORTANT]
> Anche se le regole di sicurezza in ingresso necessario consentano il traffico dagli _qualsiasi_ sulle porte di origine 9000, 9003, 1438, 1440 e 1452, queste porte sono protette da un firewall incorporato. Per altre informazioni, vedere [determinare l'indirizzo dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se si usa la replica transazionale in un'istanza gestita e se si usa qualsiasi database di istanza come server di pubblicazione o un server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta verrà concesso l'accesso alla condivisione file di Azure.

### <a name="user-defined-routes"></a>route definite dall'utente

|NOME|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Rete virtuale|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Inoltre, è possibile aggiungere voci alla tabella di route per instradare il traffico con gli intervalli IP privati in locale come destinazione tramite il gateway di rete virtuale o appliance di rete virtuale (NVA).

Se la rete virtuale include un DNS personalizzato, aggiungere una voce per l'indirizzo IP del resolver ricorsivo di Azure (ad esempio 168.63.129.16). Per altre informazioni, vedere [configurare un DNS personalizzato](sql-database-managed-instance-custom-dns.md). Il server DNS personalizzato deve essere in grado di risolvere i nomi host in questi domini e i sottodomini: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, e *microsoftonline-errore*.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Database SQL di protezione dati avanzata](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o un' [rete virtuale di Azure esistente](sql-database-managed-instance-configure-vnet-subnet.md) dove è possibile distribuire le istanze gestite.
- [Calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si desidera distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:
  - Nel [portale di Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).
  - Usando [un modello Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [un modello di Azure Resource Manager (tramite JumpBox, con SQL Server Management Studio incluso)](https://portal.azure.com/).
