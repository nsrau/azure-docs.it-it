---
title: Componenti e limiti del sistema informativo di Azure
description: Questo articolo offre una descrizione generale dell'architettura e della gestione di Microsoft Azure.
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
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587206"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componenti e limiti del sistema informativo di Azure
Questo articolo offre una descrizione generale dell'architettura e della gestione di Azure. L'ambiente di sistema di Azure è costituito dalle reti seguenti:

- Rete di produzione di Microsoft Azure (rete di Azure)
- Rete aziendale Microsoft (corpnet)

Team IT separati sono responsabili del funzionamento e della manutenzione di queste reti.

## <a name="azure-architecture"></a>Architettura di Azure
Azure è una piattaforma e un'infrastruttura di cloud computing per la compilazione, distribuzione e la gestione delle applicazioni e dei servizi attraverso una rete di data center. Microsoft gestisce questi data center. In base al numero di risorse specificato dai clienti, Azure crea le macchine virtuali (VM) in base alle esigenze delle risorse. Queste macchine virtuali operano in un hypervisor di Azure progettato per l'uso nel cloud e non accessibile al pubblico.

In ogni nodo di server fisico di Azure, è presente un hypervisor che viene eseguito direttamente sull'hardware. L'hypervisor divide un nodo in un numero variabile di macchine virtuali guest. Ogni nodo ha anche una macchina virtuale "radice" che esegue il sistema operativo host. Windows Firewall è abilitato in ogni macchina virtuale. È possibile definire le porte che possono essere indirizzate configurando il file di definizione del servizio. Queste porte sono le uniche aperte e indirizzabili, internamente o esternamente. Tutto il traffico e l'accesso al disco e alla rete viene mediato dagli hypervisor e dal sistema operativo radice.

A livello di host le macchine virtuali di Azure eseguono una versione personalizzata e con protezione avanzata di Windows Server più recente. Azure usa una versione di Windows Server che include solo i componenti necessari per ospitare le macchine virtuali. Ciò migliora le prestazioni e riduce la superficie di attacco. I limiti delle macchine sono applicati dall'hypervisor, che non dipende dalla sicurezza del sistema operativo.

### <a name="azure-management-by-fabric-controllers"></a>Gestione di Azure dai controller di infrastruttura

In Azure le macchine virtuali in esecuzione sui server fisici (pannelli/nodi) sono raggruppate in circa 1000 cluster. Le macchine virtuali vengono gestite in modo indipendente da un componente software di piattaforma con scalabilità orizzontale e ridondante denominato controller di infrastruttura (FC).

Ogni controller di infrastruttura gestisce il ciclo di vita delle applicazioni in esecuzione nel relativo cluster ed esegue il provisioning e monitora l'integrità dell'hardware sotto il suo controllo. Esegue le operazioni in modo autonomo, ad esempio trasferendo le istanze di macchine virtuali nei server integri quando determina che un server presenta dei malfunzionamenti. Il controller di infrastruttura esegue anche operazioni di gestione delle applicazioni, ad esempio distribuzione, aggiornamento e scalabilità orizzontale delle applicazioni.

Il data center è suddiviso in cluster. I cluster isolano gli errori a livello di controller di infrastruttura e impediscono che determinate classi di errori interessino i server del cluster in cui si verificano. I controller di infrastruttura che servono un determinato cluster di Azure sono raggruppati in un cluster di controller di infrastruttura.

### <a name="hardware-inventory"></a>Inventario dell’hardware

Il controller di infrastruttura crea un inventario dell'hardware Azure e dei dispositivi di rete durante il processo di configurazione del bootstrap. Qualsiasi componente di nuovo hardware e di rete che accede all'ambiente di produzione di Azure deve seguire il processo di configurazione del bootstrap. Il controller di infrastruttura è responsabile della gestione dell'intero inventario intero elencato nel file di configurazione datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Immagine del sistema operativo gestito dal controller di infrastruttura

Il team del sistema operativo fornisce immagini sotto forma di dischi rigidi virtuali che vengono distribuiti su tutte le macchine virtuali Host e Guest nell'ambiente di produzione di Azure. Il team costruisce queste "immagini di base" attraverso un processo di creazione offline automatizzato. L'immagine di base è una versione del sistema operativo in cui il kernel e gli altri componenti di base sono stati modificati e ottimizzati per supportare l'ambiente di Azure.

Esistono tre tipi di immagini del sistema operativo gestito dall'infrastruttura:

- Host: un sistema operativo personalizzato che funziona sulle macchine virtuali Host.
- Nativo: un sistema operativo nativo in esecuzione sul tenant (ad esempio, Archiviazione di Azure). Questo sistema operativo non è un hypervisor.
- Guest: un sistema operativo guest in esecuzione su macchine virtuali guest.

I sistemi operativi host e nativo gestiti dai controller di infrastruttura sono progettati per l'uso nel cloud e non sono accessibili pubblicamente.

#### <a name="host-and-native-operating-systems"></a>Sistema operativo host e nativo

il sistema operativo host e il sistema operativo nativo sono immagini del sistema operativo con protezione avanzata che ospitano gli agenti di infrastruttura e funzionano su un nodo di calcolo (in esecuzione come prima macchina virtuale nel nodo) e sui nodi di archiviazione. Il vantaggio dell'uso delle immagini di base ottimizzate del sistema operativo host o nativo è che viene ridotta l'area della superficie esposta dall'API o dai componenti non usati che presentano rischi notevoli di sicurezza e che aumentano il footprint del sistema operativo. I sistemi operativi con footprint ridotto includono solo i componenti necessari in Azure.

#### <a name="guest-operating-system"></a>Sistema operativo guest

i componenti interni di Azure in esecuzione sulle macchine virtuali Guest del sistema operativo non hanno la possibilità di eseguire il protocollo RDP (Remote Desktop Protocol). Tutte le modifiche alle impostazioni di configurazione di base devono essere sottoposte al processo di gestione delle modifiche e della versione.

## <a name="azure-datacenters"></a>Datacenter di Azure
Il team dell'infrastruttura Microsoft Cloud e delle operazioni (MCIO) gestisce tutte le infrastrutture fisiche e gli impianti dei data center di Microsoft per tutti i Servizi online Microsoft. Il team MCIO è responsabile principalmente della gestione dei controlli fisici e ambientali nei data center, nonché la della gestione e del supporto dei dispositivi di rete perimetrale esterni (ad es. router perimetrali e router di data center). Il team MCIO è inoltre responsabile della configurazione minima dell'hardware dei server sui rack nel data center. I clienti non hanno alcuna interazione diretta con Azure.

## <a name="service-management-and-service-teams"></a>Team di gestione dei servizi e team dei servizi
Il supporto dell’assistenza di Azure è gestito da vari di gruppi di progettazione noti come team dei servizi. Ogni team dei servizi è responsabile di un'area di supporto di Azure. Ogni team dei servizi deve avere un tecnico disponibile 24x7 per analizzare e risolvere gli errori nel servizio. I team dei servizi, per impostazione predefinita, non hanno accesso fisico all'hardware che opera in Azure.

I team dei servizi sono:

- Piattaforma applicativa
- Azure Active Directory
- Calcolo di Azure
- Azure Net
- Servizi di progettazione cloud
- ISSD: Security
- Autenticazione a più fattori
- Database SQL
- Archiviazione

## <a name="types-of-users"></a>Tipi di utenti
I dipendenti (o collaboratori) di Microsoft vengono considerati utenti interni. Tutti gli altri utenti sono considerati utenti esterni. Tutti gli utenti interni Azure hanno lo stato di dipendente categorizzato con un livello di riservatezza che definisce l'accesso ai dati dei clienti (accesso o nessun accesso). La tabella seguente descrive i privilegi di utente in Azure (autorizzazione dopo l'autenticazione):

| Ruolo | Interno o esterno | Livello di riservatezza | Privilegi concessi e funzioni svolte | Tipo di accesso
| --- | --- | --- | --- | --- |
| Tecnico del data center di Azure | Interno | Nessun accesso ai dati dei clienti | Gestire la sicurezza fisica dei locali. Effettuare pattugliamenti dentro e fuori i data center e monitorare tutti i punti di ingresso. Accompagnare all'interno e all'esterno del data center il personale non autorizzato che fornisce servizi generali (ad esempio ristorazione o pulizia) o lavori informatici all'interno del data center. Operazioni di routine di monitoraggio e manutenzione dell'hardware di rete. Eseguire la gestione degli incidenti e gli interventi in garanzia utilizzando una varietà di strumenti. Effettuare il monitoraggio e la manutenzione di routine dell'hardware fisico nei data center. Accedere all'ambiente su richiesta dei proprietari. Capacità di eseguire indagini forensi, registrare i rapporti di operazioni non consentite e richiedere una formazione obbligatoria in materia di sicurezza e requisiti dei criteri. Proprietà operativa e manutenzione degli strumenti di sicurezza critici, come gli scanner e la raccolta dei registri. | Accesso permanente all'ambiente. |
| Valutazione degli eventi imprevisti di Azure (tecnici Rapid Response) | Interno | Accesso ai dati dei clienti | Gestire le comunicazioni tra i team MCIO, di supporto e di ingegneria. Valutare gli eventi imprevisti della piattaforma, i problemi di distribuzione e le richieste di servizio. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT. |
| Tecnici della distribuzione di Azure | Interno | Accesso ai dati dei clienti | Distribuzione e aggiornamenti di componenti della piattaforma, software e modifiche di configurazione pianificate per il supporto di Azure. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT. |
| Supporto per l'interruzione del servizio clienti di Azure (tenant) | Interno | Accesso ai dati dei clienti | Esecuzione di debug e diagnosi di guasti ed errori della piattaforma per i singoli tenant del computer e gli account Azure. Analisi non riuscita. Offre correzioni critiche alla piattaforma o al cliente e miglioramenti tecnici attraverso l'assistenza. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT. |
| Tecnici del sito live di Azure (tecnici di monitoraggio) e eventi imprevisti | Interno | Accesso ai dati dei clienti | Diagnosticare e attenuazione dello stato di integrità della piattaforma utilizzando strumenti diagnostici. Offre correzioni per i driver di volume, riparazioni derivanti da interruzioni e assistenza per le azioni di ripristino delle interruzioni. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT. |
|Clienti Azure | Esterno | N/D | N/D | N/D |

Azure utilizza identificatori univoci per autenticare utenti e clienti dell'organizzazione (o processi che agiscono per conto di utenti dell'organizzazione) su tutti gli asset e i dispositivi che fanno parte dell'ambiente di Azure.

### <a name="azure-internal-authentication"></a>Autenticazione interna di Azure

le comunicazioni tra i componenti interni di Azure sono protetti con crittografia TLS. Nella maggior parte dei casi, i certificati X.509 sono autofirmati. Costituiscono un’eccezione i certificati con le connessioni a cui è possibile accedere dall'esterno della rete di Azure, così come i certificati per i controller di infrastruttura. I controller di infrastruttura hanno i certificati emessi da un'autorità di certificazione Microsoft (CA) supportata da una CA radice attendibile. In questo modo è possibile eseguire facilmente il rollover delle chiavi pubbliche dei controller di infrastruttura. Inoltre, gli strumenti di sviluppo Microsoft usano le chiavi pubbliche dei controller di infrastruttura. Quando gli sviluppatori inviano nuove immagini di applicazioni, le immagini vengono crittografate con una chiave pubblica del controller di infrastruttura per proteggere eventuali segreti incorporati.

### <a name="azure-hardware-device-authentication"></a>Autenticazione dei dispositivi hardware di Azure

Il controller di infrastruttura gestisce un set di credenziali (chiavi e/o password) usate per l'autenticazione con i diversi dispositivi hardware sotto il suo controllo. Microsoft usa un sistema per impedire l'accesso a queste credenziali. Nello specifico il trasporto, la persistenza e l’uso di queste credenziali sono progettati per impedire l'accesso del personale e servizi di backup, amministratori e sviluppatori di Azure alle informazioni sensibili, riservate o private.

Microsoft utilizza la crittografia basata sulla chiave pubblica di identità principale del controller di infrastruttura. Questo avviene nei tempi di configurazione e riconfigurazione del controller di infrastruttura per trasferire le credenziali utilizzate per accedere ai dispositivi hardware di rete. Se il controller di infrastruttura deve usare le credenziali, questo le recupera e ne esegue la decrittografia.

### <a name="network-devices"></a>Dispositivi di rete

Gli account del servizio di rete vengono configurati dal team della rete di Azure per consentire a un client di Azure di eseguire l'autenticazione per i dispositivi di rete (router, commutatori e bilanciamento del carico).

## <a name="secure-service-administration"></a>Protezione dell'amministrazione dei servizi
Il personale operativo Azure è tenuto a utilizzare workstation protette per l'amministrazione (SAW). I clienti possono implementare controlli simili utilizzando workstation con accesso con privilegi. Con le workstation SAW, il personale amministrativo utilizza un account amministrativo assegnato a livello personale diverso dal normale account utente. Tenendo separati questi account sensibili, l'approccio SAW garantisce l'affidabilità della workstation.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](azure-protection-of-customer-data.md)
