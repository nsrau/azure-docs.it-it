---
title: Disponibilità elevata e ripristino di emergenza di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Implementare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 078ce7e2acd93ecab6b37407f460672d4acb1853
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707332"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure 

>[!IMPORTANT]
>Questa documentazione non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che il lettore abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli argomenti di backup, ripristino, disponibilità elevata e ripristino di emergenza.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questa documentazione sono stati semplificati per una migliore comprensione generale e non devono essere usati come passaggi dettagliati per manuali operativi finali. Se si vuole creare manuali operativi per le configurazioni, è necessario testare e provare i processi e documentare quelli correlati alle configurazioni specifiche. 


La disponibilità elevata e il ripristino di emergenza sono aspetti fondamentali nell'esecuzione di un'istanza cruciale di SAP HANA nei server Azure (istanze Large). Per progettare e implementare le corrette strategie di disponibilità elevata e ripristino di emergenza, è fondamentale la collaborazione con SAP, il proprio integratore di sistemi o Microsoft. È anche importante prendere in considerazione l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) specifici dell'ambiente.

Microsoft supporta alcune funzionalità di disponibilità elevata con le istanze Large di SAP HANA. Queste funzionalità includono:

- **Replica di archiviazione**: Capacità del sistema di archiviazione di replicare tutti i dati in un altro modulo per istanze Large di HANA in un'altra area di Azure. SAP HANA funziona in modo indipendente da questo metodo. Questa funzionalità è il meccanismo di ripristino di emergenza predefinito offerto per HANA in istanze Large.
- **Replica di sistema HANA**: Il [replica di tutti i dati in SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) in un sistema SAP HANA separato. L'obiettivo tempo di ripristino è ridotto al minimo tramite la replica dei dati a intervalli regolari. SAP HANA supporta le modalità asincrona, sincrona in memoria e sincrona, La modalità sincrona viene usata solo per i sistemi SAP HANA che si trovano all'interno dello stesso data center o a distanze inferiori a 100 km. Nell'attuale progettazione degli indicatori di HANA in istanze Large la replica di sistema HANA può essere usata solo per la disponibilità elevata all'interno di una sola area. La replica di sistema HANA richiede un componente proxy inverso o di routing di terze parti per le configurazioni di ripristino di emergenza in un'altra area di Azure. 
- **Failover automatico dell'host**: Una soluzione locale di ripristino dagli errori per SAP HANA che è un'alternativa alla replica di sistema HANA. Se il nodo master diventa non disponibile, vengono configurati uno o più nodi SAP HANA di standby in modalità di scalabilità orizzontale e SAP HANA esegue automaticamente il failover in un nodo di standby.

SAP HANA in Azure (istanze Large) è disponibile in due aree di Azure all'interno di quattro diverse aree geopolitiche (Stati Uniti, Australia, Europa e Giappone). Due aree all'interno di un'area geopolitica che ospitano gli indicatori di HANA in istanze Large sono connesse a circuiti di rete dedicati separati. Questi vengono usati per la replica di snapshot di archiviazione in modo da fornire metodi di ripristino di emergenza. La replica non viene definita per impostazione predefinita, ma viene configurata per i clienti che ordinano la funzionalità di ripristino di emergenza. La replica di archiviazione dipende dall'utilizzo degli snapshot di archiviazione per le istanze Large di HANA. Non è possibile scegliere un'area di Azure come area di ripristino di emergenza in un'area geopolitica diversa. 

La tabella seguente mostra i metodi di disponibilità elevata e ripristino di emergenza e le combinazioni associate di cui è attualmente disponibile il supporto:

| Scenario supportato nelle istanze Large di HANA | Opzione di disponibilità elevata | Opzione di ripristino di emergenza | Commenti |
| --- | --- | --- | --- |
| Nodo singolo | Non disponibile. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione. | |
| Failover automatico dell'host: Scalabilità orizzontale (con o senza standby)<br /> incluso 1+1 | Possibile con il nodo di standby che acquisisce il ruolo attivo.<br /> Cambio di ruolo controllato da HANA. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione.<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione. | Set di volumi HANA collegati a tutti i nodi.<br /> Il sito di ripristino di emergenza deve avere lo stesso numero di nodi. |
| Replica di sistema HANA | Possibile con la configurazione del nodo primario o secondario.<br /> Il nodo secondario assume il ruolo primario in caso di failover.<br /> Replica di sistema HANA e failover controllato dal sistema operativo. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione.<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione.<br /> Il ripristino di emergenza tramite la replica di sistema HANA non è ancora possibile senza componenti di terze parti. | Set separato di volumi di dischi collegati a ogni nodo.<br /> Solo i volumi di dischi di replica secondaria nel sito di produzione vengono replicati nella posizione di ripristino di emergenza.<br /> Nel sito di ripristino di emergenza è necessario un set di volumi. | 

In una configurazione di ripristino di emergenza dedicata, l'unità di istanze Large di HANA nel sito di ripristino di emergenza non viene usata per eseguire altri carichi di lavoro o sistemi non di produzione. L'unità è passiva e viene distribuita solo in caso di failover di emergenza. Tuttavia, per molti clienti questa configurazione non è la scelta migliore.

Vedere [Scenari HLI supportati](hana-supported-scenario.md) per informazioni sui dettagli relativi a layout di archiviazione ed Ethernet per l'architettura.

> [!NOTE]
> [Le distribuzioni di SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (più istanze di HANA in un'unità) come scenari di sovrapposizione funzionano con i metodi HA e HR elencati nella tabella. Un'eccezione consiste nell'uso della replica di sistema HANA con un cluster di failover automatico basato su Pacemaker. Un caso simile supporta solo un'istanza HANA per unità. Per le distribuzioni [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000), funzionano solo i metodi di disponibilità elevata e ripristino di emergenza se viene distribuito più di un tenant. Se è distribuito un solo tenant, sono validi tutti i metodi elencati.  

In una configurazione di ripristino di emergenza multifunzione, l'unità di istanze Large di HANA nel sito di ripristino di emergenza esegue un carico di lavoro non di produzione. In caso di emergenza, arrestare il sistema non di produzione e montare i set di volumi (aggiuntivi) con replica di archiviazione e quindi avviare l'istanza HANA di produzione. Molti clienti che usano la funzionalità di ripristino di emergenza di HANA in istanze Large usano questa configurazione. 


Per altre informazioni sulla disponibilità elevata di SAP HANA, vedere gli articoli SAP seguenti: 

- [SAP HANA High-Availability Whitepaper](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (White paper sulla disponibilità elevata di SAP HANA)
- [SAP HANA Administration Guide](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guida all'amministrazione di SAP HANA)
- [SAP HANA Academy Video on SAP HANA System Replication](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication) (Video di SAP HANA Academy sulla replica di sistema di SAP HANA)
- [SAP Support Note #1999880 - FAQ on SAP HANA System Replication](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880) (Nota di supporto SAP 1999880 - Domande frequenti sulla replica di sistema di SAP HANA)
- [SAP Support Note #2165547 - SAP HANA Backup and Restore within SAP HANA System Replication Environment](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726) (Nota di supporto SAP 2165547 - Backup e ripristino di SAP HANA nell'ambiente di replica di sistema di SAP HANA)
- [SAP Support Note #1984882 - Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226) (Nota di supporto SAP 1984882 - Uso della replica di sistema di SAP HANA per la sostituzione di hardware con tempo di inattività minimo o nullo)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerazioni sulla rete per il ripristino di emergenza con istanze Large di HANA

Per sfruttare i vantaggi della funzionalità di ripristino di emergenza di HANA in istanze Large, è necessario progettare la connettività di rete alle due aree di Azure. È necessario implementare una connessione del circuito ExpressRoute di Azure dall'ambiente locale nell'area di Azure principale e un'altra connessione dei circuito dall'ambiente locale all'area di ripristino di emergenza. Questa misura consente di gestire le situazioni in cui si verifica un problema in un'area di Azure, anche nella posizione di un router MSEE (Microsoft Enterprise Edge).

Come seconda misura, è possibile connettere tutte le reti virtuali di Azure connesse a SAP HANA in Azure (istanze Large) in una delle aree a un circuito ExpressRoute che connette HANA in istanze Large nell'altra area. Grazie a questa *connessione incrociata*, i servizi in esecuzione in una rete virtuale di Azure nell'area 1 possono connettersi alle unità HANA in istanze Large nell'area 2 e viceversa. In questo modo, è possibile gestire il caso in cui solo una delle posizioni MSEE che si connette all'infrastruttura locale con Azure è offline.

Il grafico seguente illustra una configurazione resiliente per i casi di ripristino di emergenza:

![Configurazione ottimale per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Altri requisiti per l'uso della replica di archiviazione di HANA in istanze Large per il ripristino di emergenza

Oltre ai requisiti precedenti per una configurazione di ripristino di emergenza con HANA in istanze Large, è necessario:

- Ordinare SKU di SAP HANA in Azure (istanze Large) della stessa dimensione degli SKU di produzione e distribuirli nell'area di ripristino di emergenza. Nelle distribuzioni dei clienti attuali, queste istanze vengono usate per eseguire istanze di HANA non di produzione. Queste configurazioni sono chiamate *impostazioni di ripristino di emergenza multifunzione*.   
- Ordinare archiviazione aggiuntiva nel sito di ripristino di emergenza per ogni SKU di SAP HANA in Azure (istanze Large) che si vuole ripristinare nel sito di ripristino di emergenza. L'acquisto di spazio di archiviazione aggiuntivo consente di allocare i volumi di archiviazione, che vengono usati come destinazione della replica di archiviazione dall'area di Azure di produzione nell'area di Azure di ripristino di emergenza.
- Nel caso in cui nella replica primaria sia impostato HSR e si configuri la replica basata su archiviazione nel sito di ripristino di emergenza, è necessario acquistare risorse di archiviazione aggiuntive per quest'ultimo, in modo che i dati sia del nodo primario che di quello secondario vengano replicati nel sito di ripristino di emergenza.

  **Passaggi successivi**
- Vedere [Backup e ripristino](hana-backup-restore.md).













