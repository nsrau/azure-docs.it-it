---
title: Domande comuni sull'appliance Azure Migrate
description: Risposte alle domande comuni sull'appliance Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425356"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni sull'appliance Azure Migrate. Per altre domande, vedere gli articoli seguenti:

- [Domande generali](resources-faq.md) su Azure migrate.
- [Domande](common-questions-discovery-assessment.md) sull'individuazione, la valutazione e la visualizzazione delle dipendenze.
- [Domande](common-questions-server-migration.md) sulla migrazione del server.
- Ottieni risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum). 


## <a name="what-is-the-azure-migrate-appliance"></a>Che cos'è la Azure Migrate Appliance?

Il Azure Migrate Appliance è un'appliance semplice usata dallo strumento di valutazione Azure Migrate: Server per individuare e valutare i server locali. Il dispositivo viene usato anche dallo strumento di migrazione Azure Migrate: Server, per la migrazione senza agenti di macchine virtuali VMware locali. 

- Il dispositivo viene distribuito in locale come macchina virtuale o computer fisico.
- L'appliance individua i computer locali e invia continuamente i dati sulle prestazioni e i metadati del computer a Azure Migrate.
- L'individuazione dell'appliance è senza agente. Nessun elemento viene installato nei computer individuati.

[Altre](migrate-appliance.md) informazioni sull'appliance.

## <a name="how-does-the-appliance-connect-to-azure"></a>In che modo l'appliance si connette ad Azure?

Il dispositivo può connettersi tramite Internet o usare Azure ExpressRoute con peering pubblico/Microsoft.

## <a name="does-appliance-analysis-impact-performance"></a>L'analisi del dispositivo ha un effetto sulle prestazioni?

L'appliance Azure Migrate profilare i computer locali in modo continuo per misurare i dati sulle prestazioni. Questa profilatura non ha quasi nessun effetto sulle prestazioni delle macchine profilate.

### <a name="can-i-harden-the-appliance-vm"></a>È possibile rafforzare la macchina virtuale dell'appliance?

Quando si crea la macchina virtuale dell'appliance usando il modello scaricato, è possibile aggiungere componenti, ad esempio un antivirus, al modello, purché si lascino le regole di comunicazione e del firewall necessarie per la Azure Migrate Appliance sul posto.


## <a name="what-network-connectivity-is-needed"></a>Quale connettività di rete è necessaria?

Esaminare le informazioni sulla connettività di rete:
- Valutazione VMware con il dispositivo Azure Migrate: requisiti di accesso a [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-vmware.md#port-access) .
- Migrazione senza agenti VMware tramite il dispositivo Azure Migrate: requisiti di accesso a [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-vmware-migration.md#agentless-ports) .
- Valutazione di Hyper-V con il dispositivo Azure Migrate: requisiti di accesso a [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-hyper-v.md#port-access) .


## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Esaminare i dati raccolti:

- [Metadati](migrate-appliance.md#collected-metadata-vmware)e [dati sulle prestazioni delle](migrate-appliance.md#collected-performance-data-vmware) macchine virtuali VMware.
- [Metadati](migrate-appliance.md#collected-metadata-hyper-v)e [dati sulle prestazioni delle](migrate-appliance.md#collected-performance-data-hyper-v) macchine virtuali Hyper-V.


## <a name="how-is-data-stored"></a>Come vengono archiviati i dati?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nella località di Azure in cui è stato creato il progetto Azure Migrate. 

- I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando si elimina il progetto Azure Migrate.
- Se si usa la [visualizzazione delle dipendenze](concepts-dependency-visualization.md), i dati raccolti vengono archiviati nel Stati Uniti, in un'area di lavoro log Analytics creata nella sottoscrizione di Azure. Tali dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Quanti dati vengono caricati nella profilatura continua?

Il volume dei dati inviati a Azure Migrate dipende da diversi parametri. Per dare un'idea, un progetto Azure Migrate con 10 computer (ognuno con un disco e una scheda di interfaccia di rete) Invia circa 50 MB al giorno. Questo valore è approssimativo e cambia in base al numero di punti dati per le schede di rete e i dischi. L'aumento dei dati inviati è non lineare se si verifica un aumento del numero di macchine virtuali, nic o dischi.

## <a name="is-data-encrypted-at-restin-transit"></a>I dati sono crittografati a riposo/in transito?

Sì, per entrambi.

- I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS.
- I metadati vengono archiviati in un database di [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e nell' [archiviazione BLOB di Azure](../storage/common/storage-service-encryption.md)in una sottoscrizione Microsoft. I metadati vengono crittografati a riposo.
- I dati per l'analisi delle dipendenze vengono crittografati anche in transito (Secure HTTPS). Viene archiviato in un'area di lavoro Log Analytics nella sottoscrizione. Viene anche crittografato a riposo.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>In che modo l'appliance si connette a server vCenter?

1. L'appliance si connette a server vCenter (porta 443), usando le credenziali specificate durante la configurazione dell'appliance.
2. L'appliance usa VMware PowerCLI per eseguire query server vCenter per raccogliere i metadati sulle macchine virtuali gestite da server vCenter.
3. Il dispositivo raccoglie i dati di configurazione sulle VM (core, memoria, dischi, schede di rete) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese.
4. I metadati raccolti vengono inviati a Azure Migrate: server Assessment (via Internet tramite HTTPS) per la valutazione.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>È possibile connettere l'appliance a più server vCenter?

No. Esiste un mapping uno-a-uno tra un appliance e server vCenter. Per individuare le macchine virtuali in più istanze di server vCenter, è necessario distribuire più appliance.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quante VM o server è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 VM VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 250 server fisici con un'unica appliance. Se si dispone di più computer nell'ambiente locale, vedere la pagina relativa alla scalabilità di [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) e valutazione [fisica](scale-physical-assessment.md) .

## <a name="can-i-delete-an-appliance"></a>È possibile eliminare un appliance?

Attualmente, l'eliminazione di un appliance dal progetto non è supportata.

- L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il Azure Migrate progetto associato all'appliance.
- Tuttavia, l'eliminazione del gruppo di risorse eliminerà anche altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri componenti di Azure associati al progetto nel gruppo di risorse.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>È possibile usare l'appliance con una sottoscrizione o un progetto diverso?

Dopo aver usato l'appliance per avviare l'individuazione, non è possibile riconfigurarla con una sottoscrizione di Azure diversa oppure usarla in un progetto di Azure Migrate diverso. Non è inoltre possibile individuare le macchine virtuali in un server vCenter diverso. Configurare un nuovo Appliance per queste attività.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>È possibile configurare l'appliance in una macchina virtuale di Azure?
No. Questa operazione non è attualmente supportata. 

## <a name="can-i-discover-on-an-esxi-host"></a>È possibile individuare in un host ESXi?
No. Per individuare le macchine virtuali VMware, è necessario un server vCenter.

## <a name="how-do-i-update-the-appliance"></a>Ricerca per categorie aggiornare l'appliance?

Per impostazione predefinita, l'appliance e gli agenti installati vengono aggiornati automaticamente. Il dispositivo verifica la disponibilità di aggiornamenti ogni 24 ore. Se il processo di aggiornamento non riesce, si verifica un nuovo tentativo. Aggiornamenti automatici aggiorna solo gli agenti Appliance e appliance. Il sistema operativo non è stato aggiornato. Utilizzare Microsoft Update per rendere aggiornato il sistema operativo.

## <a name="can-i-check-agent-health"></a>È possibile controllare l'integrità dell'agente?

Nel portale passare alla pagina **integrità agente** nello strumento Server Assessment o migrazione server. Qui è possibile controllare lo stato della connessione tra gli agenti di individuazione e valutazione nell'appliance e Azure.

## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
