---
title: Azure Migrate appliance FAQ
description: Risposte alle domande comuni sull'appliance Azure Migrate.Get answers to common questions about the Azure Migrate appliance.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336979"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance di Azure Migrate: domande comuniAzure Migrate appliance: Common questions

Questo articolo risponde alle domande comuni sull'appliance Di Azure Migrate.This article answers common questions about the Azure Migrate appliance. Se hai altre domande, controlla queste risorse:

- [Domande generali](resources-faq.md) su Azure Migrate
- Domande su [scoperta, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Domande sulla [migrazione dei server](common-questions-server-migration.md)
- Ottenere risposte alle domande nel forum di [Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Che cos'è l'appliance Azure Migrate?

L'appliance Azure Migrate è un'appliance leggera usata dallo strumento Azure Migrate: Server Assessment per individuare e valutare i server locali. Lo strumento Di migrazione di Azure: migrazione server usa anche l'appliance per la migrazione senza agenti di macchine virtuali VMware locali.

Ecco altre informazioni sull'appliance di Azure Migrate:Here's more information about the Azure Migrate appliance:

- L'appliance viene distribuita in locale come macchina virtuale o macchina fisica.
- L'appliance individua i computer locali e invia continuamente i metadati e i dati sulle prestazioni del computer ad Azure Migrate.The appliance discovers on-premises machines and continually continuamente sends machine metadata and performance data to Azure Migrate.
- L'individuazione dell'appliance è senza agente. Nulla è installato sulle macchine individuate.

[Ulteriori informazioni](migrate-appliance.md) sull'apparecchio.

## <a name="how-does-the-appliance-connect-to-azure"></a>In che modo l'appliance si connette ad Azure?

L'appliance può connettersi tramite Internet o usando Azure ExpressRoute con il peering pubblico/Microsoft.The appliance can connect over the internet or by using Azure ExpressRoute with public/Microsoft peering.

## <a name="does-appliance-analysis-affect-performance"></a>L'analisi dell'apparecchio influisce sulle prestazioni?

I profili dell'appliance di Azure in locale vengono continuamente eseguiti per misurare i dati sulle prestazioni. Questa profilatura non ha quasi alcun impatto sulle prestazioni delle macchine profilate.

## <a name="can-i-harden-the-appliance-vm"></a>È possibile indurire la macchina virtuale dell'appliance?

Quando si usa il modello scaricato per creare la macchina virtuale dell'appliance, è possibile aggiungere componenti (ad esempio antivirus) al modello se si lasciano in posizione le regole di comunicazione e firewall necessarie per l'appliance di Azure Migrate.When you use the downloaded template to create the appliance VM, you can add components (antivirus, for example) to the template if you leave in place the communication and firewall rules that are required for the Azure Migrate appliance.

## <a name="what-network-connectivity-is-required"></a>Quale connettività di rete è necessaria?

Vedere gli articoli seguenti per informazioni sui requisiti di connettività di rete per l'appliance di Azure Migrate:See the following articles for information about network connectivity requirements for the Azure Migrate appliance:

- **Valutazione VMware**: [Accesso tramite URL](migrate-appliance.md#url-access) e [accesso alle porte](migrate-support-matrix-vmware.md#port-access)
- **Migrazione senza agente VMware**: [accesso all'URL](migrate-appliance.md#url-access) e [accesso alle porte](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Valutazione Hyper-V**: [accesso tramite URL](migrate-appliance.md#url-access) e [accesso alla porta](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Quali dati raccoglie l'apparecchio?

Vedere gli articoli seguenti per informazioni sui dati raccolti dall'appliance di Azure Migrate nelle macchine virtuali:See the following articles for information about data that the Azure Migrate appliance collects on VMs:

- **VMware VM**: [Esaminare i](migrate-appliance.md#collected-data---vmware) dati raccolti. [
- **VM Hyper-V**: [esaminare i](migrate-appliance.md#collected-data---hyper-v) dati raccolti.

## <a name="how-is-data-stored"></a>Come vengono archiviati i dati?

I dati raccolti dall'appliance Azure Migrate vengono archiviati nel percorso di Azure in cui è stato creato il progetto Azure Migrate.Data that's collected by the Azure Migrate appliance is stored in the Azure location where you created the Azure Migrate project.

Ecco altre informazioni su come vengono archiviati i dati:Here's more information about how data is stored:

- I dati raccolti vengono archiviati in modo sicuro in CosmosDB in una sottoscrizione Microsoft. I dati vengono eliminati quando si elimina il progetto di Azure Migrate.The data is deleted when you delete the Azure Migrate project. L'archiviazione viene gestita da Azure Migrate.Storage is handled by Azure Migrate. Non è possibile scegliere in modo specifico un account di archiviazione per i dati raccolti.
- Se si usa la visualizzazione delle dipendenze, i dati raccolti vengono archiviati negli Stati Uniti in un'area di lavoro di Azure Log Analytics creata nella sottoscrizione di Azure.If you use [dependency visualization](concepts-dependency-visualization.md), the data that's collected is stored in the United States in an Azure Log Analytics workspace created in your Azure subscription. I dati vengono eliminati quando si elimina l'area di lavoro di Log Analytics nella sottoscrizione.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quanti dati vengono caricati durante la profilazione continua?

Il volume di dati inviati a Azure Migrate dipende da più parametri. Ad esempio, un progetto di Azure Migrate con 10 computer (ciascuno con un disco e una scheda di interfaccia di rete) invia circa 50 MB di dati al giorno. Questo valore è approssimativo; il valore effettivo varia a seconda del numero di punti dati per i dischi e le schede di interfaccia di rete. Se il numero di computer, dischi o schede di interfaccia di rete aumenta, l'aumento dei dati inviati non è lineare.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>I dati vengono crittografati inattivi e in transito?

Sì, per entrambi:

- I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS.
- I metadati vengono archiviati in un database [di Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e [nell'archiviazione BLOB](../storage/common/storage-service-encryption.md) di Azure in una sottoscrizione Microsoft.Metadata is stored in an Azure Cosmos database and in Azure Blob storage in a Microsoft subscription. I metadati vengono crittografati inattivi per l'archiviazione.
- Anche i dati per l'analisi delle dipendenze vengono crittografati in transito (tramite HTTPS sicuro). Viene archiviato in un'area di lavoro di Log Analytics nella sottoscrizione. I dati vengono crittografati inattivi per l'analisi delle dipendenze.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Come si connette l'appliance a vCenter Server?

Questi passaggi descrivono come l'appliance si connette a VMware vCenter Server:

1. L'appliance si connette a vCenter Server (porta 443) utilizzando le credenziali fornite al momento della configurazione dell'appliance.
2. L'appliance utilizza VMware PowerCLI per eseguire query su vCenter Server per raccogliere i metadati relativi alle macchine virtuali gestite da vCenter Server.
3. L'appliance raccoglie i dati di configurazione relativi alle macchine virtuali (core, memoria, dischi, schede di interfaccia di rete) e alla cronologia delle prestazioni di ogni macchina virtuale nell'ultimo mese.
4. I metadati raccolti vengono inviati allo strumento Azure Migrate: Server Assessment (tramite Internet tramite HTTPS) per la valutazione.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>L'appliance di Azure Migrate può connettersi a più server vCenter?

No. Esiste un mapping uno-a-uno tra [un'appliance di Azure Migrate](migrate-appliance.md) e vCenter Server. Per individuare le macchine virtuali in più istanze di vCenter Server, è necessario distribuire più appliance. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Un progetto di Azure Migrate può avere più appliance?
A un progetto possono essere collegati più appliance. Tuttavia, un accessorio può essere associato a un solo progetto. 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quante macchine virtuali o server è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 macchine virtuali VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 250 server fisici con una singola appliance. Se nell'ambiente locale sono presenti più computer, vedere [Scalabilità di una valutazione Hyper-V](scale-hyper-v-assessment.md), ridimensionamento di [una valutazione VMware](scale-vmware-assessment.md)e ridimensionamento di [una valutazione del server fisico.](scale-physical-assessment.md)

## <a name="can-i-delete-an-appliance"></a>Posso eliminare un apparecchio?

Attualmente, l'eliminazione di un accessorio dal progetto non è supportata.

L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il progetto Azure Migrate associato all'appliance.

Tuttavia, l'eliminazione del gruppo di risorse comporta anche l'eliminazione di altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri componenti di Azure nel gruppo di risorse associati al progetto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso utilizzare l'apparecchio con un abbonamento o un progetto diverso?

Dopo aver usato l'appliance per avviare l'individuazione, non è possibile riconfigurare l'appliance per l'uso con una sottoscrizione di Azure diversa e non è possibile usarla in un progetto Azure Migrate diverso. Non è inoltre possibile individuare le macchine virtuali in un'istanza diversa di vCenter Server.You also can't discover VMs on a different instance of vCenter Server. Configurare una nuova appliance per queste attività.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>È possibile configurare l'appliance in una macchina virtuale di Azure?

No. Attualmente, questa opzione non è supportata. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso scoprire su un host ESXi?

No. Per individuare le macchine virtuali VMware, è necessario disporre di vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Come si aggiorna l'apparecchio?

Per impostazione predefinita, l'appliance e i relativi agenti installati vengono aggiornati automaticamente. L'appliance verifica la disponibilità di aggiornamenti ogni 24 ore. Gli aggiornamenti che hanno esito negativo vengono ritentati. 

Solo l'appliance e gli agenti dell'appliance vengono aggiornati da questi aggiornamenti automatici. Il sistema operativo non viene aggiornato dagli aggiornamenti automatici di Azure Migrate.The operating system is not updated by Azure Migrate automatic updates. Utilizzare gli aggiornamenti di Windows per mantenere aggiornato il sistema operativo.

## <a name="can-i-check-agent-health"></a>Posso controllare lo stato di salute dell'agente?

Sì. Nel portale passare alla pagina **Integrità agente** per Azure Migrate: Server Assessment o Azure Migrate: strumento di migrazione del server. È possibile controllare lo stato della connessione tra Azure e gli agenti di individuazione e valutazione nell'appliance.

## <a name="next-steps"></a>Passaggi successivi

Leggere la [panoramica di Azure Migrate](migrate-services-overview.md).
