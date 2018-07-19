---
title: Rete di produzione di Azure
description: Questo articolo offre una descrizione generale della rete di produzione di Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101548"
---
# <a name="azure-production-network"></a>Rete di produzione di Azure
Gli utenti della rete di produzione di Azure includono i clienti esterni che accedono alle proprie applicazioni di Microsoft Azure, nonché il personale di supporto interno di Microsoft Azure che gestisce la rete di produzione. In questo articolo vengono descritti i metodi di accesso di sicurezza e i meccanismi di protezione per stabilire connessioni alla rete di produzione di Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Routing Internet e tolleranza di errore
Un'infrastruttura Domain Name Service (WADNS) di Microsoft Azure interna ed esterna con ridondanza globale insieme a vari cluster di server DNS primari e secondari offrono tolleranza di errore, mentre altri controlli di sicurezza di rete Microsoft Azure, tra cui NetScaler, impediscono attacchi Distributed Denial of Service (DDoS) e proteggono l'integrità dei servizi DNS di Microsoft Azure.

I server WADNS si trovano in più strutture di data center. L'implementazione WADNS incorpora una gerarchia di server DNS secondari/primari per la risoluzione pubblica dei nomi di dominio dei clienti di Azure. I nomi di dominio vengono in genere risolti in un indirizzo CloudApp.net, che esegue il wrapping dell'indirizzo IP virtuale per il servizio del cliente. Esclusiva di Azure, l'indirizzo IP virtuale corrispondente a un indirizzo IP dedicato interno della traslazione del tenant viene eseguito tramite i servizi di bilanciamento del carico Microsoft responsabili dell'indirizzo IP virtuale.

Azure è ospitato in data center geograficamente distribuiti negli Stati Uniti ed è basato su piattaforme di routing all'avanguardia che implementano standard di architettura avanzati e scalabili. Alcune funzionalità rilevanti includono:

- Ingegneria del traffico basata su Multiprotocol Label Switching (MPLS) che offre un impiego efficiente dei collegamenti e una riduzione automatica del servizio in caso di interruzione
- Le reti vengono implementate con architetture con ridondanza N+1 (need plus one) o superiori.
- Esternamente, i data center vengono serviti da circuiti di rete dedicati a larghezza di banda elevata che connettono in modo ridondante le proprietà con oltre 1.200 provider di servizi Internet a livello globale in più punti di peering che offrono oltre 2.000 gigabyte al secondo (Gbps) di capacità perimetrale.

Poiché Microsoft dispone di circuiti di rete proprietari tra i data center, grazie a questi attributi l'offerta di Azure è in grado di raggiungere una disponibilità di rete superiore al 99,9% senza richiedere i tradizionali provider di servizi Internet di terze parti.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Connessione alla rete di produzione e ai firewall associati
I criteri di flusso del traffico Internet della rete di Azure indirizzano il traffico alla rete di produzione di Azure che si trova nel data center regionale più vicino negli Stati Uniti. Poiché i data center di produzione di Azure gestiscono hardware e architetture di rete coerenti, la descrizione del flusso di traffico di seguito si applica in modo coerente a tutti i data center.

Una volta instradato il traffico Internet per Azure al data center più vicino, verrà stabilita una connessione per i router di accesso. Questi router di accesso consentono di isolare il traffico tra i nodi di Azure e le istanze di VM create dai clienti. I dispositivi dell'infrastruttura di rete all'accesso e al perimetro rappresentano le delimitazioni in cui si applicano filtri in ingresso e/o in uscita. Questi router vengono configurati mediante un ACL a più livelli per filtrare il traffico di rete indesiderato e applicare limiti di velocità del traffico, se necessario. Il traffico consentito dall'ACL viene instradato ai servizi di bilanciamento del carico. I router di distribuzione sono progettati per consentire solo gli indirizzi IP approvati da Microsoft, fornire l'anti-spoofing e stabilire connessioni TCP mediante ACL.

I dispositivi esterni di bilanciamento del carico si trovano dietro i router di accesso per eseguire il processo Network Address Translation (NAT) da indirizzi IP instradabili tramite Internet a indirizzi IP interni di Azure. Instradano anche i pacchetti a porte e indirizzi IP interni di produzione e agiscono come meccanismo di protezione per limitare l'esposizione dello spazio degli indirizzi della rete di produzione interna.

Per impostazione predefinita, Microsoft applica Hypertext Transfer Protocol Secure (HTTPS) per tutto il traffico trasmesso ai Web browser del cliente, inclusi l'accesso e tutto il traffico conseguente. L'uso di TLS v1.2 abilita un tunnel sicuro per l'attraversamento del traffico. L'ACL sui router di accesso e core garantisce coerenza tra l'origine del traffico e il risultato previsto.

Una differenza importante in questa architettura rispetto all'architettura di sicurezza tradizionale è che non sono presenti firewall hardware dedicati, dispositivi specializzati di rilevamento/prevenzione delle intrusioni o altri appliance di sicurezza normalmente previsti prima che vengano stabilite connessioni all'ambiente di produzione di Azure. I clienti si aspettano in genere di trovare questi dispositivi firewall hardware nella rete di Azure, mentre in Azure non vengono impiegati. In modo quasi esclusivo, queste funzionalità di sicurezza sono integrate nel software che esegue l'ambiente di Azure per fornire meccanismi avanzati di sicurezza a più livelli che includono funzionalità firewall. Inoltre, l'ambito del limite e della proliferazione associata di dispositivi di sicurezza critici è più facile da gestire e inventariare, come illustrato nella figura precedente, poiché è gestito dal software che esegue Azure.

## <a name="core-security-and-firewall-features"></a>Funzionalità di sicurezza e firewall di base
Azure implementa avanzate funzionalità di sicurezza e firewall software a vari livelli per applicare le funzionalità di sicurezza in genere previste in un ambiente tradizionale per proteggere il limite di autorizzazioni di sicurezza di base.

### <a name="azure-security-features"></a>Funzionalità di sicurezza di Azure
Azure implementa firewall software basati su host all'interno della rete di produzione. Alcune funzionalità di sicurezza e firewall di base si trovano all'interno dell'ambiente Azure. Queste funzionalità di sicurezza riflettono una strategia di difesa approfondita nell'ambiente Azure. I dati dei clienti in Microsoft Azure sono protetti dai firewall seguenti:

**Firewall hypervisor (filtro di pacchetti)**: implementato nell'hypervisor e configurato da un agente controller di infrastruttura. Questo firewall protegge il tenant in esecuzione all'interno della VM da accessi non autorizzati. Per impostazione predefinita, quando viene creata una VM, tutto il traffico viene bloccato e l'agente controller di infrastruttura aggiunge regole/eccezioni nel filtro per consentire il traffico autorizzato.

In questo caso sono previste due categorie di regole:

- Regole di configurazione computer o di infrastruttura: per impostazione predefinita, vengono bloccate tutte le comunicazioni. Sono presenti eccezioni che consentono a una VM di inviare e ricevere comunicazioni Dynamic Host Configuration Protocol (DHCP), informazioni DNS, inviare traffico a Internet "pubblico", in uscita verso altre VM all'interno del cluster controller di infrastruttura e del server di attivazione del sistema operativo. Poiché l'elenco di destinazioni in uscita consentite delle VM non include subnet di router di Microsoft Azure e altre proprietà Microsoft, le regole fungono da livello di difesa.
- File di configurazione dei ruoli: definisce gli ACL in ingresso in base al modello di servizio dei tenant. Ad esempio, se un tenant ha un frontend Web sulla porta 80 in una determinata VM, la porta 80 viene aperta a tutti gli indirizzi IP. Se la VM ha un ruolo di lavoro end in esecuzione, il ruolo di lavoro viene aperto solo per la VM nello stesso tenant.

**Firewall host nativo**: l'infrastruttura e la risorsa di archiviazione di Microsoft Azure vengono eseguite su un sistema operativo nativo senza hypervisor e quindi Windows Firewall viene configurato con i due precedenti set di regole.

**Firewall host**: il firewall host protegge la partizione host che esegue l'hypervisor. Le regole vengono programmate per consentire solo al controller di infrastruttura e ai jumpbox di comunicare con la partizione host su una porta specifica. Le altre eccezioni consentono la risposta DHCP e le risposte DNS. Azure usa un file di configurazione computer che include il modello delle regole del firewall per la partizione host. È anche disponibile un'eccezione del firewall host che consente alle VM di comunicare con i componenti host, il server cablato e il server di metadati tramite un protocollo o porte specifiche.

**Firewall guest**: componente Windows Firewall del sistema operativo guest, configurabile dal cliente nelle macchine virtuali e nelle risorse di archiviazione del cliente.

Altre funzionalità di sicurezza integrate nelle funzionalità di Azure:

- Ai componenti dell'infrastruttura vengono assegnati indirizzi IP ricavati da indirizzi IP dedicati. Un utente malintenzionato in Internet non può inoltrare il traffico a questi indirizzi, poiché non raggiungerebbero Microsoft. I router gateway Internet filtrano i pacchetti indirizzati esclusivamente agli indirizzi interni, in modo che non accedano alla rete di produzione. Gli unici componenti che accettano il traffico indirizzato agli indirizzi IP virtuali sono i servizi di bilanciamento del carico.
- I firewall implementati in tutti i nodi interni pongono tre considerazioni sull'architettura di sicurezza principale per qualsiasi scenario specificato:

   - Sono posizionati dietro il servizio di bilanciamento del carico e accettano i pacchetti da qualsiasi posizione. Devono essere esposti esternamente e corrispondere alle porte aperte in un firewall perimetrale tradizionale.
   - Accettano solo pacchetti da un set limitato di indirizzi, nell'ambito della strategia di difesa approfondita da attacchi Denial of Service. Queste connessioni vengono autenticate a livello di crittografia.
   - I firewall sono accessibile solo da alcuni nodi interni, nel qual caso accettano pacchetti solo da un elenco enumerato di indirizzi IP di origine (tutti indirizzi IP dedicati all'interno della rete di Azure). Ad esempio, un attacco alla rete aziendale potrebbe indirizzare richieste a questi indirizzi, che verrebbero però bloccati, a meno che l'indirizzo di origine del pacchetto non sia presente nell'elenco enumerato all'interno della rete di Azure.
   - Il router di accesso sul perimetro blocca i pacchetti in uscita inoltrati a un indirizzo all'interno della rete di Azure per via delle route statiche configurate.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
