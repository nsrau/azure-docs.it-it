---
title: Componenti e limiti del sistema informativo di Azure
description: Questo articolo offre una descrizione generale dell'architettura e della gestione di Microsoft Azure.
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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101962"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componenti e limiti del sistema informativo di Azure
Questo articolo offre una descrizione generale dell'architettura e della gestione di Microsoft Azure. L'ambiente di sistema di Azure è costituito dalle reti seguenti:

- Rete di produzione di Microsoft Azure (rete di Azure)
- Rete aziendale Microsoft (rete Corpnet)

Team IT separati sono responsabili del funzionamento e della manutenzione della rete di Azure e delle reti CorpNet.

## <a name="azure-architecture"></a>Architettura di Azure
Microsoft Azure è una piattaforma e un'infrastruttura di cloud computing per la compilazione, distribuzione e la gestione delle applicazioni e dei servizi attraverso una rete di data center gestiti da Microsoft. In base al numero di risorse specificato dai clienti, Azure crea le macchine virtuali in base alle esigenze delle risorse. Queste macchine virtuali operano in un Hypervisor di Azure progettato per l'uso nel cloud e non accessibile al pubblico.

In ogni nodo di server fisico di Azure, è presente un Hypervisor che viene eseguito direttamente sull'hardware. L'Hypervisor divide un nodo in un numero variabile di macchine virtuali (VM) guest. Ogni nodo ha anche un'apposita VM "radice" che esegue il sistema operativo host. Windows Firewall è abilitato in ogni macchina virtuale. Le sole le porte aperte e utilizzabili, internamente o esternamente, sono le porte definite in modo esplicito nel file di definizione del servizio configurato dal cliente. Tutto il traffico e l'accesso al disco e alla rete viene mediato dagli Hypervisor e dal sistema operativo radice.

A livello di host le macchine virtuali di Azure eseguono una versione personalizzata e con protezione avanzata di Windows Server più recente. Microsoft Azure usa una versione ridotta di Windows Server che include solo i componenti necessari per ospitare le macchine virtuali. Ciò serve sia per migliorare le prestazioni sia per ridurre la superficie di attacco. I limiti delle macchine sono applicati dall'Hypervisor, che non dipende dalla sicurezza del sistema operativo.

**Gestione di Azure dei controller di infrastruttura (FC)**: in Azure le macchine virtuali in esecuzione sui server fisici (pannelli/nodi) sono raggruppate in circa 1000 "cluster". Le macchine virtuali vengono gestite in modo indipendente da un componente software di piattaforma con scalabilità orizzontale e ridondante denominato controller di infrastruttura (FC).

Ogni controller di infrastruttura gestisce il ciclo di vita delle applicazioni in esecuzione nel relativo cluster ed esegue il provisioning e monitora l'integrità dell'hardware sotto il suo controllo. Esegue entrambe le operazioni in modo autonomo, ad esempio trasferendo le istanze di macchine virtuali nei server integri quando determina che un server presenta dei malfunzionamenti. Il controller di infrastruttura esegue anche operazioni di gestione delle applicazioni, ad esempio distribuzione, aggiornamento e scalabilità orizzontale delle applicazioni.

Il data center è suddiviso in cluster. I cluster isolano gli errori a livello di controller di infrastruttura e impediscono che determinate classi di errori interessino i server del cluster in cui si verificano. I controller di infrastruttura che servono un determinato cluster di Azure sono raggruppati in un cluster di controller di infrastruttura.

**Inventario dell'hardware**: un inventario dei dispositivi hardware e di rete di Azure viene preparato durante il processo di configurazione del bootstrap e documentato nel file di configurazione datacenter.xml. Qualsiasi componente di nuovo hardware e di rete che accede all'ambiente di produzione di Azure deve seguire il processo di configurazione del bootstrap. Il controller di infrastruttura è responsabile della gestione dell'intero inventario intero elencato nel file di configurazione datacenter.xml.

**Sistema operativo gestito dal controller di infrastruttura**: il team del sistema operativo fornisce le immagini del sistema operativo sotto forma di dischi rigidi virtuali (VHD) che vengono distribuiti su tutte le VM Host e Guest nell'ambiente di produzione di Azure. Il team del sistema operativo costruisce queste "immagini di base" attraverso un processo di creazione offline automatizzato. L'immagine di base è una versione del sistema operativo in cui il kernel e gli altri componenti di base sono stati modificati e ottimizzati per supportare l'ambiente di Azure.

Esistono tre tipi di immagini del sistema operativo gestito dall'infrastruttura:

- Sistema operativo host: un sistema operativo personalizzato che funziona sulle VM Host
- Sistema operativo nativo: un sistema operativo che funziona sui tenant (ad esempio, Archiviazione di Azure) che non dispone di Hypervisor
- Sistema operativo guest: un sistema che funziona sulle VM Guest

I sistemi operativi host e nativo gestiti dai controller di infrastruttura sono progettati per l'uso nel cloud e non sono accessibili pubblicamente.

**Sistema operativo host e nativo**: il sistema operativo host e il sistema operativo nativo sono immagini del sistema operativo con protezione avanzata che ospitano gli agenti di infrastruttura (FA) e funzionano su un nodo di calcolo (in esecuzione come prima macchina virtuale nel nodo) e sui nodi di archiviazione. Il vantaggio dell'uso delle immagini di base ottimizzate del sistema operativo host o nativo è che viene ridotta l'area della superficie esposta dall'API o dai componenti non usati che presentano rischi notevoli di sicurezza e che aumentano il footprint del sistema operativo. Questi sistemi operativi con footprint ridotto includono solo i componenti necessari in Azure. Ciò migliora le prestazioni e riduce la superficie di attacco.

**Sistema operativo guest**: i componenti interni di Azure in esecuzione sulle macchine virtuali Guest del sistema operativo non hanno la possibilità di eseguire il protocollo RDP (Remote Desktop Protocol), a differenza dei clienti esterni. Tutte le modifiche alle impostazioni di configurazione di base devono essere sottoposte al processo di gestione delle modifiche e della versione.

## <a name="azure-datacenters"></a>Datacenter di Azure
Il team dell'infrastruttura Microsoft Cloud e delle operazioni (MCIO) gestisce tutte le infrastrutture fisiche e gli impianti dei data center di Microsoft per tutti i Servizi online Microsoft. Il team MCIO è responsabile principalmente della gestione dei controlli fisici e ambientali nei data center, nonché la della gestione e del supporto dei dispositivi di rete perimetrale esterni (router perimetrali e router di data center). Il team MCIO è inoltre responsabile della configurazione minima dell'hardware dei server sui rack nel data center. I clienti non hanno alcuna interazione diretta con Azure.

## <a name="service-management--service-teams"></a>Team di gestione dei servizi e team dei servizi
Il supporto del servizio di Azure è gestito da vari di gruppi di progettazione noti come 'Team dei servizi'. Ogni team dei servizi è responsabile di un'area di supporto di Azure. Ogni team dei servizi deve avere un tecnico disponibile 24x7 per analizzare e risolvere gli errori nel servizio. I team dei servizi, per impostazione predefinita, non hanno accesso fisico all'hardware che opera in Azure.

I team dei servizi sono:

- Piattaforma applicativa
- Azure Active Directory
- Calcolo di Azure
- Azure Net
- Servizi di progettazione cloud
- ISSD: Sicurezza
- Autenticazione a più fattori
- Database SQL
- Archiviazione

## <a name="types-of-users"></a>Tipi di utenti
Tutti gli utenti interni Azure hanno lo stato di dipendente categorizzato con un livello di riservatezza che definisce l'accesso ai dati dei clienti (accesso o nessun accesso). I dipendenti (o collaboratori) di Microsoft vengono considerati utenti interni. Tutti gli altri utenti sono considerati utenti esterni. La tabella seguente descrive i privilegi di utente in Azure (autorizzazione dopo l'autenticazione):

| Ruolo | Interno o esterno | Livello di riservatezza | Privilegi concessi e funzioni svolte | Tipo di accesso
| --- | --- | --- | --- | --- |
| Tecnico del data center di Azure | Interno | Nessun accesso ai dati dei clienti | Gestione della sicurezza fisica dei locali. Conduzione del personale all'interno e all'esterno del data center e monitorare tutti i punti di ingresso. Servizi di accompagnamento all'interno e all'esterno del data center di personale non autorizzato che svolge servizi generali (pasti, pulizie) o lavoro IT all'interno del data center. Esecuzione di operazioni di routine di monitoraggio e manutenzione dell'hardware di rete. Esecuzione della gestione degli eventi imprevisti e risoluzione dei problemi usando un'ampia gamma di strumenti. Operazioni di routine di monitoraggio e manutenzione dell'hardware fisico nei data center; Accesso all'ambiente su richiesta dei proprietari degli edifici. È in grado di eseguire indagini forensi, registrare rapporti sugli incidenti e richiedere corsi di formazione sulla sicurezza obbligatori e requisiti dei criteri. Proprietà operativa e manutenzione di strumenti di sicurezza critici come scanner e raccolta di log. | Accesso permanente all'ambiente |
| Valutazione degli eventi imprevisti di Microsoft Azure (tecnici Rapid Response) | Interno | Accesso ai dati dei clienti | Gestione delle comunicazioni tra i team delle operazioni sull'infrastruttura, di assistenza e tecnico di Azure. ; Valutazione degli eventi imprevisti della piattaforma, problemi di distribuzione e richieste di servizio. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT |
| Tecnici della distribuzione di Microsoft Azure | Interno | Accesso ai dati dei clienti | Esecuzione della distribuzione/degli aggiornamenti di componenti della piattaforma, software e modifiche di configurazione pianificate per il supporto di Microsoft Azure. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT |
| Supporto per l'interruzione del servizio clienti di Microsoft Azure (Tenant) | Interno | Accesso ai dati dei clienti | Esecuzione del debug e della diagnostica di errori e delle interruzioni di piattaforma per i singoli tenant di calcolo e gli account di Microsoft Azure. Analisi degli errori e esecuzione di correzioni critiche alle piattaforma/cliente, apporto di miglioramenti tecnici al supporto. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT |
| Tecnici del sito live di Microsoft Azure (tecnici di monitoraggio) e eventi imprevisti | Interno | Accesso ai dati dei clienti | Responsabili della diagnosi e della prevenzione dei rischi dell'integrità della piattaforma usando strumenti di diagnostica. Fornitura di correzioni per i driver di volume, soluzione di problemi derivanti da interruzioni e assistenza durante le azioni di ripristino a seguito di un'interruzione. | Accesso JIT all'ambiente - con accesso permanente limitato per sistemi non di clienti JIT |
|Client di Microsoft Azure | Esterno | N/D | N/D | N/D |

Azure utilizza identificatori univoci per autenticare utenti e clienti dell'organizzazione (o processi che agiscono per conto di utenti dell'organizzazione) su tutti gli asset/dispositivi che fanno parte dell'ambiente di Azure.

**Autenticazione interna di Microsoft Azure**: le comunicazioni tra i componenti interni di Azure sono protetti con crittografia TLS. Nella maggior parte dei casi, i certificati X.509 sono autofirmati. Vengono fatte delle eccezioni per i certificati con le connessioni a cui è possibile accedere dall'esterno della rete di Azure e per i controller di infrastruttura. I controller di infrastruttura hanno i certificati emessi da un'autorità di certificazione Microsoft (CA) supportata da una CA radice attendibile. In questo modo è possibile eseguire facilmente il rollover delle chiavi pubbliche dei controller di infrastruttura. Inoltre, le chiavi pubbliche dei controller di infrastruttura vengono usate dagli strumenti di sviluppo Microsoft in modo che quando gli sviluppatori inviano nuove immagini di applicazioni, vengono crittografate con una chiave pubblica del controller di infrastruttura per proteggere eventuali segreti incorporati.

**Autenticazione dei dispositivi hardware di Microsoft Azure**: il controller di infrastruttura gestisce un set di credenziali (chiavi e/o password) usate per l'autenticazione con i diversi dispositivi hardware sotto il suo controllo. Il sistema utilizzato per il trasporto, la persistenza e uso di queste credenziali sono progettati per impedire l'accesso del personale/servizi di backup, amministratori e sviluppatori di Azure alle informazioni sensibili, riservate o private.

La crittografia basata sulla chiave pubblica dell'identità master del controller di infrastruttura viene usata nel programma di installazione e nelle riconfigurazioni del controller di infrastruttura per trasferire le credenziali usate per accedere ai dispositivi hardware di rete. Le credenziali vengono recuperate e decrittografate dal controller di infrastruttura quando è necessario.

**Dispositivi di rete**: gli account del servizio di rete vengono configurati dal team della rete di Azure per consentire a un client di Microsoft Azure di eseguire l'autenticazione per i dispositivi di rete (router, commutatori e bilanciamento del carico).

## <a name="secure-service-administration"></a>Protezione dell'amministrazione dei servizi
Il personale operativo di Microsoft Azure è tenuto a usare workstation di amministrazione sicure (SAW; i clienti possono implementare controlli simili usando workstation dotate di accesso con privilegi o PAW). L'approccio SAW è un'estensione dell'ormai consolidata buona prassi di usare per il personale amministrativo due account diversi per i ruoli di utente e amministratore, assegnando un account amministrativo personale diverso dal normale account utente. Tenendo separati questi account sensibili, l'approccio SAW garantisce l'affidabilità della workstation.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni che Microsoft esegue per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
