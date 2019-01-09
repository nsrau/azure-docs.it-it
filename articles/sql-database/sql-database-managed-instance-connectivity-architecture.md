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
ms.date: 12/10/2018
ms.openlocfilehash: e69f6869911555730fe723b340e224c0d5a1e4bb
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536050"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Architettura della connettività di Istanza gestita di database SQL di Azure

Questo articolo presenta una panoramica delle comunicazioni di Istanza gestita di database SQL di Azure e spiega l'architettura della connettività e il funzionamento dei diversi componenti per indirizzare il traffico a Istanza gestita.  

La funzione Istanza gestita di database SQL di Azure viene inserita nella rete virtuale di Azure e nella subnet dedicata per le istanze gestite. Questa distribuzione consente di: 
- Proteggere l'indirizzo IP privato.
- Stabilire una connessione a Istanza gestita direttamente da una rete locale.
- Connettere Istanza gestita a un server collegato o a un altro archivio dati locale.
- Connettere Istanza gestita a risorse di Azure.

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
> Per migliorare l'esperienza cliente e la disponibilità dei servizi, Microsoft applica criteri di finalità di rete agli elementi dell'infrastruttura di rete virtuale di Azure che potrebbero influire sul funzionamento di Istanza gestita. Si tratta di un meccanismo di piattaforma per comunicare i requisiti di rete in modo trasparente agli utenti finali, con l'obiettivo principale di evitare errori di configurazione di rete e garantire il nomale funzionamento delle operazioni di Istanza gestita. Quando si elimina Istanza gestita vengono rimossi anche i criteri di finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Verrà ora esaminata in modo più approfondito l'architettura della connettività di Istanza gestita. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Diagramma dell'architettura della connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a Istanza gestita usando il nome host con formato `<mi_name>.<dns_zone>.database.windows.net`. Questo nome host viene risolto in un indirizzo IP privato, anche se è registrato nella zona DNS pubblica ed è risolvibile pubblicamente. Il valore `zone-id` viene generato automaticamente quando viene creato il cluster. Se un nuovo cluster ospita un'istanza gestita secondaria, ne condivide la zona con il cluster principale. Per altre informazioni, vedere [Auto-failover groups](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets) (Gruppi di failover automatico).

L'indirizzo IP privato appartiene al servizio di bilanciamento del carico interno di Istanza gestita che indirizza il traffico al gateway di Istanza gestita. Poiché potenzialmente potrebbero venire eseguite più istanze gestite nello stesso cluster, il gateway usa il nome host di Istanza gestita per reindirizzare il traffico al servizio del motore SQL corretto.

I servizi di gestione e distribuzione si connettono a Istanza gestita usando un [endpoint di gestione](#management-endpoint) mappato al servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set di porte predefinite usate esclusivamente dai componenti di gestione di Istanza gestita. Il firewall incorporato nei nodi è configurato per consentire il traffico solo da intervalli IP specifici di Microsoft. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite certificato.

## <a name="management-endpoint"></a>Endpoint di gestione

Il cluster virtuale di Istanza gestita di database SQL di Azure contiene un endpoint di gestione usato da Microsoft per la gestione di Istanza gestita. L'endpoint di gestione è protetto con un firewall predefinito a livello di rete e con la verifica reciproca dei certificati a livello di applicazione. È possibile [trovare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando le connessioni vengono avviate dall'interno di Istanza gestita (backup, log di controllo), sembra che il traffico abbia origine dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso a servizi pubblici da Istanza gestita impostando le regole del firewall per consentire solo l'indirizzo IP di Istanza gestita. Sono disponibili altre informazioni sul metodo che consente di [verificare il firewall incorporato di Istanza gestita](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ciò non si applica all'impostazione delle regole del firewall per i servizi di Azure presenti nella stessa area di Istanza gestita poiché la piattaforma Azure è un'ottimizzazione per il traffico tra i servizi che vengono collocati.

## <a name="network-requirements"></a>Requisiti di rete

È possibile distribuire Istanza gestita in una subnet dedicata (subnet di Istanza gestita) nella rete virtuale conforme ai requisiti seguenti.
- **Subnet dedicata**: la subnet di Istanza gestita non deve contenere altri servizi cloud associati e non deve essere una subnet del gateway. Non sarà possibile creare un'istanza gestita in una subnet che contiene altre risorse oltre all'istanza gestita o aggiungere altre risorse nella subnet in un secondo momento.
- **Gruppo di sicurezza di rete compatibile (NSG)**: un gruppo di sicurezza di rete associato a una subnet di Istanza gestita può contenere le regole indicate nelle tabelle seguenti (Regole di sicurezza in ingresso obbligatorie e Regole di sicurezza in uscita obbligatorie) prima di qualsiasi altra regola. È possibile usare un gruppo di sicurezza di rete per controllare completamente l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433. 
- **Tabella di route definita dall'utente**: la subnet di Istanza gestita deve contenere una tabella di route utenti con **0.0.0.0/0 hop successivo su Internet** come tabella di route definita dall'utente obbligatoria assegnata. È inoltre possibile aggiungere una route definita dall'utente che indirizza il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale. 
- **DNS personalizzato facoltativo**: se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco l'indirizzo IP del resolver ricorsivo di Azure (ad esempio 168.63.129.16). Per altre informazioni, vedere [Configuring Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione del DNS personalizzato). Il server DNS personalizzato deve essere in grado di risolvere i nomi host nei domini seguenti e nei relativi sottodomini: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* e *microsoftonline-p.com*. 
- **Nessun endpoint di servizio**: alla subnet di Istanza gestita non deve essere associato alcun endpoint di servizio. Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
- **Indirizzi IP sufficienti**: la subnet di Istanza gestita deve contenere almeno 16 indirizzi IP (il numero minimo consigliato è 32 indirizzi IP). Per altre informazioni, vedere [Determine the size of subnet for Managed Instances](sql-database-managed-instance-determine-size-vnet-subnet.md) (Determinare le dimensioni della subnet per le istanze gestite). È possibile distribuire le istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata per soddisfare i [requisiti di rete per Istanza gestita](#network-requirements) o creare una [rete e una subnet nuove](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Se la subnet di destinazione non è compatibile con tutti i requisiti precedenti, non sarà possibile distribuire una nuova istanza gestita. Quando viene creata un'istanza gestita, un *criterio relativo alle finalità di rete* viene applicato nella subnet per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, viene rimosso anche il *criterio relativo alle finalità di rete*.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie 

| NOME       |Porta                        |Protocollo|Sorgente           |Destination|Azione|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |Qualsiasi        |CONSENTI |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |Qualsiasi        |CONSENTI |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|Qualsiasi        |CONSENTI |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie 

| NOME       |Porta          |Protocollo|Sorgente           |Destination|Azione|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |Qualsiasi              |Qualsiasi        |CONSENTI |
|mi_subnet   |Qualsiasi           |Qualsiasi     |Qualsiasi              |MI SUBNET  |CONSENTI |

  > [!Note]
  > Anche se le regole di sicurezza in ingresso obbligatorie consentono il traffico da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, tali porte sono protette dal firewall incorporato. Questo [articolo](sql-database-managed-instance-find-management-endpoint-ip-address.md) illustra come è possibile individuare l'indirizzo IP degli endpoint di gestione e verificare le regole del firewall. 
  
  > [!Note]
  > Se si usa la replica transazionale in Istanza gestita e qualsiasi database nell'Istanza gestita viene utilizzato come server di pubblicazione o di distribuzione, anche la porta 445 (TCP in uscita) deve essere aperta nelle regole di sicurezza della subnet per accedere alla condivisione file di Azure.
  
## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere  [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Informazioni su come [configurare una nuova rete virtuale](sql-database-managed-instance-create-vnet-subnet.md) o [configurare una rete virtuale esistente](sql-database-managed-instance-configure-vnet-subnet.md) quando si distribuiscono istanze gestite.
- [Calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si distribuiscono le istanze gestite. 
- Per una guida introduttiva, vedere le informazioni su come creare Istanza gestita:
  - Dal [portale di Azure](sql-database-managed-instance-get-started.md)
  - Tramite [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - con un [modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - con un [modello di Azure Resource Manager (incluso jumpbox con SSMS)](https://portal.azure.com/)
