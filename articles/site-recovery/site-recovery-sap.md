---
title: Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Site Recovery

La maggior parte delle distribuzioni SAP di medie e grandi dimensioni prevede una soluzione di ripristino di emergenza. L'importanza di soluzioni di ripristino di emergenza efficaci e testabili è aumentata con lo spostamento di un numero sempre maggiore di processi aziendali importanti in applicazioni come SAP. Azure Site Recovery è stata testato e integrato con le applicazioni SAP. Site Recovery supera le capacità della maggior parte delle soluzioni di ripristino di emergenza locali, a un costo totale di proprietà inferiore rispetto alle soluzioni della concorrenza.

Con Site Recovery è possibile:
* **Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale**, eseguendo la replica dei componenti in Azure.
* **Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure**, eseguendo la replica dei componenti in un altro data center di Azure.
* **Semplificare la migrazione nel cloud** usando Site Recovery per eseguire la migrazione della distribuzione SAP ad Azure.
* **Semplificare gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP**, creando un clone di produzione on demand per i test delle applicazioni SAP.

Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con [Azure Site Recovery](site-recovery-overview.md). L'articolo illustra le procedure consigliate per la protezione di una distribuzione SAP NetWeaver a tre livelli in Azure eseguendo la replica in un altro data center di Azure con Site Recovery. Descrive gli scenari e le configurazioni supportati e la procedura per eseguire failover di test (analisi ripristino di emergenza) e failover effettivi.

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare, assicurarsi di conoscere le procedure per eseguire le attività seguenti:

* [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Progettare una rete di ripristino](site-recovery-azure-to-azure-networking-guidance.md)
* [Eseguire un failover di test in Azure](azure-to-azure-walkthrough-test-failover.md)
* [Eseguire un failover in Azure](site-recovery-failover.md)
* [Replicare un controller di dominio](site-recovery-active-directory.md)
* [Replicare SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenari supportati
Con Site Recovery è possibile implementare una soluzione di ripristino di emergenza per gli scenari seguenti:
* Sistemi SAP in esecuzione in un data center di Azure con replica in un altro data center di Azure (ripristino di emergenza da Azure ad Azure). Per altre informazioni, vedere [Azure-to-Azure replication architecture](https://aka.ms/asr-a2a-architecture) (Architettura di replica da Azure ad Azure).
* Sistemi SAP in esecuzione su server VMware (o fisici) locali che eseguono la replica a un sito di ripristino di emergenza in un data Center di Azure (ripristino di emergenza da VMware ad Azure). Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [VMware-to-Azure replication architecture](https://aka.ms/asr-v2a-architecture) (Architettura di replica da VMware ad Azure).
* Sistemi SAP in esecuzione su server Hyper-V locali che eseguono la replica a un sito di ripristino di emergenza in un data Center di Azure (ripristino di emergenza da Hyper-V ad Azure). Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [Hyper-V-to-Azure replication architecture](https://aka.ms/asr-h2a-architecture) (Architettura di replica da Hyper-V ad Azure).

In questo articolo viene usato un scenario di ripristino di emergenza da Azure ad Azure per illustrare le funzionalità di ripristino di emergenza SAP di Site Recovery. Poiché la replica di Site Recovery non varia in base all'applicazione, il processo descritto dovrebbe applicarsi anche ad altri scenari.

### <a name="required-foundation-services"></a>Servizi di base necessari
Nello scenario affrontato in questo articolo vengono distribuiti i servizi di base seguenti:
* Azure ExpressRoute o gateway VPN di Azure
* Almeno un controller di dominio Active Directory e un server DNS in esecuzione in Azure

È consigliabile definire questa infrastruttura prima di distribuire Site Recovery.

## <a name="typical-sap-application-deployment"></a>Distribuzione tipica di un'applicazione SAP
I clienti SAP di grandi dimensioni in genere distribuiscono tra 6 e 20 singole applicazioni SAP. La maggior parte di queste applicazioni si basa su motori SAP NetWeaver ABAP o Java. A supporto di queste applicazioni NetWeaver di base ci sono numerosi motori autonomi SAP non NetWeaver più piccoli e in genere alcune applicazioni non SAP.  

È essenziale creare un inventario di tutte le applicazioni SAP in esecuzione nell'ambiente in uso. Determinare poi la modalità di distribuzione (a due o tre livelli), le versioni, le patch, le dimensioni, la frequenza di varianza e i requisiti di persistenza del disco.

![Diagramma di un tipico criterio di distribuzione SAP](./media/site-recovery-sap/sap-typical-deployment.png)

Proteggere il livello di persistenza del database SAP con strumenti DBMS nativi, ad esempio SQL Server AlwaysOn, Oracle DataGuard o la replica di sistema SAP HANA. Come il livello database di SAP, il livello client non è protetto da Site Recovery. È importante prendere in considerazione fattori che influiscono su questo livello. Alcuni fattori includono ritardo nella propagazione DNS, sicurezza e accesso remoto al data center di ripristino di emergenza.

Site Recovery è la soluzione consigliata per il livello dell'applicazione, anche per SAP SCS e ASCS. Altre applicazioni, ad esempio le applicazioni SAP non NetWeaver e le applicazioni non SAP, fanno parte dell'ambiente di distribuzione SAP globale. Devono essere anch'esse protette con Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali
Per avviare la replica di tutte le macchine virtuali dell'applicazione SAP al data center di ripristino di emergenza di Azure, seguire le indicazioni in [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md).

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale. Per impostare l'indirizzo IP, passare a **Compute and Network settings** > **Network interface card** (Impostazioni Calcolo e rete, Scheda interfaccia di rete).

![Schermata che illustra come impostare un indirizzo IP privato nel riquadro della scheda di interfaccia di rete di Site Recovery](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino
Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Per far sì che le applicazioni funzionino correttamente, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o durante un failover di test. È possibile automatizzare alcune operazioni successive al failover. È ad esempio possibile aggiornare la voce DNS e modificare associazioni e connessioni aggiungendo gli script corrispondenti al piano di ripristino.

### <a name="dns-update"></a>Aggiornamento del DNS
Se il DNS è configurato per l'aggiornamento DNS dinamico, le macchine virtuali aggiornano in genere il DNS con il nuovo indirizzo IP all'avvio. Se si vuole aggiungere un passaggio esplicito per l'aggiornamento del DNS con i nuovi indirizzi IP delle macchine virtuali, aggiungere uno [script per l'aggiornamento dell'indirizzo IP nel DNS](https://aka.ms/asr-dns-update) come azione dopo il failover nei gruppi del piano di ripristino.  

## <a name="example-azure-to-azure-deployment"></a>Esempio di distribuzione da Azure ad Azure
Il diagramma seguente illustra uno scenario di ripristino di emergenza da Azure ad Azure di Site Recovery:

![Diagramma di uno scenario di replica da Azure ad Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* Il data center principale è a Singapore (area Asia sud-orientale di Azure). Il data center di ripristino di emergenza è a Hong Kong (area Asia orientale di Azure). In questo scenario, la disponibilità elevata locale viene resa disponibile con due VM che eseguono SQL Server AlwaysOn in modalità sincrona a Singapore.
* La condivisione file SAP ASCS offre disponibilità elevata per il singolo punto di guasto SAP. La condivisione file ASCS non richiede un disco condiviso del cluster. Non sono necessarie applicazioni come SIOS.
* La protezione tramite ripristino di emergenza per il livello DBMS viene ottenuta con replica asincrona.
* Questo scenario mostra "ripristino di emergenza simmetrico". Questo termine descrive una soluzione di ripristino di emergenza che corrisponde a una replica esatta di produzione. La soluzione di ripristino di emergenza SQL Server ha una disponibilità locale elevata. Il ripristino di emergenza simmetrico non è obbligatorio per il livello di database. Molti clienti sfruttano la flessibilità delle distribuzioni cloud per creare rapidamente un nodo a disponibilità locale elevata dopo un evento di ripristino di emergenza.
* Il diagramma illustra i livelli del server applicazioni e ASCS di SAP NetWeaver replicati da Site Recovery.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover di test**.
4.  Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
5.  Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
6.  Al termine delle convalide, per pulire l'ambiente di failover di test, selezionare **Pulisci failover di test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover**.
4.  Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione di una soluzione di ripristino di emergenza per le distribuzioni SAP NetWeaver con Site Recovery, vedere il white paper scaricabile [SAP NetWeaver - Creazione di una soluzione di ripristino di emergenza con Azure Site Recovery](http://aka.ms/asr-sap). Il white paper include consigli per diverse architetture SAP, elenca le applicazioni e i tipi di VM supportati per SAP in Azure e descrive le opzioni dei piani di test per la soluzione di ripristino di emergenza.
* Sono disponibili altre informazioni sulla [replica di altri carichi di lavoro](site-recovery-workload.md) con Site Recovery.
