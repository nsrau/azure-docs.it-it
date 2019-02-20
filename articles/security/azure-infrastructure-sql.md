---
title: Funzionalità di sicurezza del database SQL di Microsoft Azure
description: Questo articolo offre una descrizione generale di come il database SQL di Azure protegge i dati dei clienti in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103949"
---
# <a name="azure-sql-database-security-features"></a>Funzionalità di sicurezza del database SQL di Microsoft Azure    
Il database SQL di Azure fornisce un servizio di database relazionale in Azure. Per proteggere i dati dei clienti e fornire le funzionalità avanzate di sicurezza che i clienti richiedono da un servizio di database relazionale, il database SQL include set specifici di funzionalità di sicurezza. Queste funzionalità si basano sui controlli ereditati da Azure.

## <a name="security-capabilities"></a>Funzionalità di sicurezza

### <a name="usage-of-the-tds-protocol"></a>Utilizzo del protocollo TDS
Il database SQL di Azure supporta solo il protocollo TDS, in base al quale il database deve essere accessibile solo tramite la porta TCP/1433 predefinita.

### <a name="azure-sql-database-firewall"></a>Firewall del database SQL di Azure
Per proteggere i dati dei clienti, il database SQL di Azure include una funzionalità firewall che, per impostazione predefinita, impedisce qualsiasi accesso al server del database SQL, come illustrato di seguito.

![Firewall del database SQL di Azure][1]

Il firewall del gateway consente di limitare gli indirizzi consentendo ai clienti un controllo granulare per specificare intervalli di indirizzi IP accettabili. Il firewall concede l'accesso in base all'indirizzo IP di origine di ogni richiesta.

I clienti possono eseguire la configurazione del firewall tramite un portale di gestione o a livello di codice usando l'API REST di gestione del database SQL di Azure. Il firewall del gateway del database SQL di Azure impedisce per impostazione predefinita qualsiasi accesso TDS del cliente alle istanze del database SQL di Azure. Il clienti devono configurare l'accesso mediante l'elenco di controllo di accesso (ACL) per consentire connessioni al database SQL di Azure per numeri di porta, protocolli e indirizzi Internet di origine e destinazione.

### <a name="dosguard"></a>DoSGuard
Gli attacchi Denial of Service (DoS) vengono ridotti da un servizio gateway del database SQL denominato DoSGuard. DoSGuard tiene traccia in modo attivo degli accessi non riusciti dagli indirizzi IP. Se sono presenti più accessi non riusciti da uno specifico indirizzo IP in un intervallo di tempo, all'indirizzo viene impedito di accedere a qualsiasi risorsa nel servizio per un periodo predefinito.

Inoltre, il gateway del database SQL di Azure esegue:

- Negoziazioni sicure di funzionalità di canale per implementare connessioni TDS FIPS 140-2 crittografate convalidate durante la connessione ai server di database.
- Ispezione di pacchetti TDS con stato durante l'accettazione di connessioni dai client. Il gateway convalida le informazioni di connessione e passa i pacchetti TDS al server fisico appropriato in base al nome di database specificato nella stringa di connessione.

Il principio generale per la sicurezza di rete dell'offerta di database SQL di Azure è quello di consentire solo le connessioni e le comunicazioni necessarie a garantire il funzionamento del servizio. Tutte le altre porte, le connessioni e i protocolli vengono bloccati per impostazione predefinita. VLAN e ACL vengono usati per limitare le comunicazioni di rete per numeri di porta, protocolli, reti di origine e destinazione.

I meccanismi approvati per implementare ACL basati su rete includono gli ACL su router e i servizi di bilanciamento del carico. Questi meccanismi vengono gestiti dalle regole firewall del gateway della rete di Azure, del firewall delle VM guest e del database SQL di Azure, configurate dal cliente.

## <a name="data-segregation-and-customer-isolation"></a>Separazione dei dati e isolamento dei clienti
La rete di produzione di Azure è strutturata in modo che i componenti di sistema accessibili pubblicamente siano separati dalle risorse interne. Esistono limiti fisici e logici tra i server Web che forniscono accesso al portale pubblico di Azure e l'infrastruttura virtuale di Azure sottostante, in cui si trovano le istanze delle applicazioni e i dati dei clienti.

Tutte le informazioni accessibili pubblicamente vengono gestite all'interno della rete di produzione di Azure. La rete di produzione è soggetta a meccanismi di autenticazione a due fattori e protezione dei limiti, usa il set di funzioni di sicurezza e del firewall descritto nella sezione precedente e impiega funzioni di isolamento dei dati come indicato nelle sezioni seguenti.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemi non autorizzati e isolamento dei controller di infrastruttura
Poiché il controller di infrastruttura è l'agente di orchestrazione centrale dell'infrastruttura di Azure, vengono applicati controlli significativi per mitigare le minacce, specialmente dagli agenti dell'infrastruttura nelle applicazioni dei clienti. Il controller di infrastruttura non riconosce alcun hardware con informazioni sul dispositivo (ad esempio, indirizzo MAC) non precaricate al suo interno. I server DHCP nel controller di infrastruttura hanno elenchi configurati di indirizzi MAC dei nodi desiderati per l'avvio. Anche se sono connessi sistemi non autorizzati, non sono incorporati nell'inventario dell'infrastruttura e pertanto non sono connessi o autorizzati a comunicare con alcun sistema nell'infrastruttura di Microsoft Azure. In questo modo si riduce il rischio di sistemi non autorizzati che comunicano con il controller di infrastruttura e ottengono l'accesso alla VLAN e ad Azure.

### <a name="vlan-isolation"></a>Isolamento VLAN
La rete di produzione di Azure è separata in modo logico in tre VLAN primarie:

- VLAN principale: crea l'interconnessione tra i nodi non attendibili del cliente.
- VLAN del controller di infrastruttura: contiene controller di infrastruttura attendibili e sistemi di supporto.
- VLAN dei dispositivi: contiene dispositivi di rete attendibili e altri dispositivi dell'infrastruttura.

### <a name="packet-filtering"></a>Filtri dei pacchetti
I firewall software e IPFilter implementati nel sistema operativo radice e guest dei nodi applicano restrizioni di connettività e impediscono il traffico non autorizzato tra le VM.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, sistema operativo radice e VM guest
L'isolamento del sistema operativo radice dalle VM guest e delle VM guest l'una dall'altra è gestito dall'hypervisor e dal sistema operativo radice.

### <a name="types-of-rules-on-firewalls"></a>Tipi di regole nei firewall
Una regola viene definita come mostrato di seguito.

{IP Security Response Center (Src), porta Src, IP destinazione, porta destinazione, protocollo destinazione, entrata/uscita, con stato/senza stato, timeout flusso con stato}.

I pacchetti SYN (Synchronous idle character) sono consentiti in entrata e in uscita solo se autorizzati da una regola. Per TCP, Azure usa regole senza stato il cui principio è che sono consentiti solo i pacchetti non SYN in entrata e in uscita dalla VM. Il presupposto di sicurezza è che qualsiasi stack host evita di ignorare un pacchetto non SYN se non ne ha mai visto uno in precedenza. Il protocollo TCP stesso è con stato e, in combinazione con la regola basata su SYN senza stato, adotta il comportamento generale di un'implementazione con stato.

Per UDP (User Datagram Protocol), Azure usa una regola con stato. Ogni volta che un pacchetto UDP corrisponde a una regola, viene creato un flusso inverso nella direzione opposta. Questo flusso ha un timeout integrato.

I clienti sono tenuti a configurare i propri firewall oltre a quanto fornito da Azure. Qui i clienti sono in grado di definire le regole per il traffico in ingresso e in uscita.

### <a name="production-configuration-management"></a>Gestione della configurazione di produzione
Configurazioni standard sicure vengono gestite dai rispettivi team delle operazioni in Azure e nel database SQL di Azure. Tutte le modifiche di configurazione apportate ai sistemi di produzione sono documentate e monitorate tramite un sistema centrale. Le modifiche hardware e software vengono monitorate tramite il sistema centrale. Le modifiche di rete relative agli ACL vengono monitorate tramite il relativo servizio di gestione.

Tutte le modifiche di configurazione in Azure vengono sviluppate e testate nell'ambiente di gestione temporanea e successivamente distribuite nell'ambiente di produzione. Le build del software vengono revisionate nell'ambito dei test. I controlli sulla sicurezza e sulla privacy vengono esaminati nell'ambito dei criteri di immissione degli elenchi di controllo. Le modifiche vengono distribuite a intervalli pianificati dal rispettivo team di distribuzione. Le versioni vengono revisionate e approvate dal personale del rispettivo team di distribuzione prima di essere distribuite in produzione.

Le modifiche vengono monitorate per garantire un esito positivo. In uno scenario con errori, viene ripristinato lo stato precedente della modifica o viene distribuito un hotfix per gestire il problema con l'approvazione del personale designato. Source Depot, Git, TFS, Master Data Services (MDS), strumenti di esecuzione, Azure Security Monitoring, il controller di infrastruttura e la piattaforma WinFabric vengono usati per gestire, applicare e verificare a livello centrale le impostazioni di configurazione nell’ambiente virtuale di Azure.

In modo analogo, le modifiche apportate ad hardware e rete includono passaggi di convalida per valutarne la conformità ai requisiti di compilazione. Le versioni vengono esaminate e autorizzate da un comitato consultivo per le modifiche (CAB) dei rispettivi gruppi nello stack.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
