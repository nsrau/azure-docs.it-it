---
title: Domande frequenti sull'uso del Servizio Migrazione del database di Azure | Microsoft Docs
description: Domande frequenti sull'uso di servizio migrazione del Database di Azure per eseguire le migrazioni del database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717991"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Domande frequenti sull'uso di servizio migrazione del Database di Azure

Questo articolo elenca le domande frequenti sull'uso di servizio migrazione del Database e le relative risposte.

## <a name="overview"></a>Panoramica

**D. Che cos'è servizio migrazione del Database di Azure?**
Servizio migrazione del Database di Azure è un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Il servizio è attualmente in generale la disponibilità, con in corso di sviluppo incentrato su:

* Affidabilità e prestazioni.
* Aggiunta iterativa di coppie origine-destinazione.
* Investimento continuo su migrazioni senza problemi.

**D. Quali coppie di origine/destinazione servizio migrazione del Database di Azure supporta attualmente?**
Il servizio supporta attualmente un'ampia gamma di coppie origine/destinazione o scenari di migrazione. Per un elenco completo dello stato di ogni scenario di migrazione disponibile vedere l'articolo [Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Altri scenari di migrazione sono disponibili in anteprima e richiedono di inviare una candidatura tramite il sito di anteprima di servizio migrazione del database. Per un elenco completo degli scenari in fase di anteprima e per l'iscrizione partecipare a una di queste offerte, vedere la [sito di anteprima DMS](https://aka.ms/dms-preview/).

**D. Quali versioni di SQL Server supporta la migrazione di Database di Azure come origine?**
Durante la migrazione da SQL Server, origini supportate per il servizio migrazione del Database di Azure sono SQL Server 2005 tramite SQL Server 2017.

**D: Quando si usa servizio migrazione del Database di Azure, che cos'è la differenza tra una migrazione online e offline?**
È possibile usare servizio migrazione del Database di Azure per eseguire le migrazioni offline e online. Con un *offline* migrazione, tempo di inattività dell'applicazione viene avviata quando inizia la migrazione. Con un *online* migrazione, tempo di inattività è limitato al tempo di trasferimento al termine della migrazione. È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

**D. Quali differenze tra servizio migrazione del Database di Azure ad altri strumenti di migrazione del database di Microsoft, ad esempio il Database Migration Assistant (DMA) o SQL Server Migration Assistant (SSMA)?**
Servizio migrazione del Database di Azure è il metodo preferito per la migrazione di database in Microsoft Azure su larga scala. Per altre informazioni su come servizio migrazione del Database di Azure confronta con altri Microsoft gli strumenti di migrazione del database e per indicazioni sull'uso del servizio per diversi scenari, vedere il post di blog [migrazione del Database di Microsoft per distinguere i Strumenti e servizi](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**D. Servizio migrazione del Database differenze tra l'offerta Azure Migrate?**
Azure Migrate supporta la migrazione delle macchine virtuali locali a IaaS di Azure. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione delle macchine virtuali locali in Azure. Azure Migrate è utile per le migrazioni in modalità lift-and-shift di carichi di lavoro basati su VM locali a VM IaaS di Azure. Tuttavia, a differenza di servizio migrazione del Database di Azure, Azure Migrate non è un servizio migrazione del database specializzati che offre alle piattaforme di database relazionale PaaS di Azure, ad esempio Database SQL di Azure o istanza gestita di Azure SQL Database.

## <a name="setup"></a>Configurazione

**D. Quali sono i prerequisiti per usare servizio migrazione del Database di Azure?**
Esistono diversi prerequisiti obbligatori per assicurare che servizio migrazione del Database venga eseguito senza problemi quando si eseguono migrazioni di database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:

* Creare una rete virtuale per il servizio migrazione del Database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività site-to-site ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Assicurarsi che la rete virtuale di Azure (VNet) regole del gruppo di sicurezza di rete non bloccano le seguenti porte di comunicazione 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

Per un elenco di tutti i prerequisiti obbligatori per supportare scenari di migrazione specifici con servizio migrazione del Database di Azure, vedere le esercitazioni correlate nel servizio migrazione del Database di Azure [documentazione](https://docs.microsoft.com/azure/dms/dms-overview) su docs.microsoft.com.

**D. Come individuare l'indirizzo IP per il servizio migrazione del Database di Azure in modo che è possibile creare un elenco Consenti per le regole del firewall consente di accedere ai database di origine per la migrazione?**
Si potrebbe essere necessario aggiungere regole del firewall che consente di servizio migrazione del Database di Azure accedere al database di origine per la migrazione. L'indirizzo IP per il servizio è dinamico, ma, se si usa Express Route, questo indirizzo viene assegnato privatamente dalla rete aziendale. Il modo più semplice per identificare l'indirizzo IP appropriato è cercare nello stesso gruppo di risorse come la risorsa servizio migrazione del Database sottoposto a provisioning per trovare l'interfaccia di rete associato. Il nome della risorsa dell'interfaccia di rete inizia in genere con il prefisso NIC ed è seguito da una sequenza alfanumerica univoca, ad esempio NIC-jj6tnztnmarpsskr82rbndyp. Se si seleziona questa risorsa dell'interfaccia di rete, è possibile visualizzare l'indirizzo IP che deve essere incluso nell'elenco indirizzi consentiti nella pagina del portale di Azure della panoramica.

Potrebbe essere necessario includere nell'elenco indirizzi consentiti anche l'origine della porta su cui SQL Server è in ascolto. Per impostazione predefinita, si tratta della porta 1433, ma l'istanza di SQL Server di origine potrebbe essere configurata per l'ascolto anche su altre porte. In questo caso, è necessario includere anche tali porte nell'elenco indirizzi consentiti. È possibile determinare la porta su cui SQL Server è in ascolto usando una query DMV:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

È anche possibile determinare la porta su cui SQL Server è in ascolto eseguendo una query sul log degli errori di SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**D. Come configurare una rete virtuale di Azure?**
Oltre alle numerose esercitazioni Microsoft che illustrano il processo di configurazione di una rete virtuale di Azure, è disponibile la documentazione ufficiale nell'articolo[Rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Utilizzo

**D. Che cos'è un riepilogo dei passaggi necessari per usare servizio migrazione del Database di Azure per eseguire una migrazione del database?**
Durante una semplice migrazione di database tipica, è necessario:

1. Creare uno o più database di destinazione.
2. Consente di valutare il database di origine.
    * Per le migrazioni omogenee, valutare il database esistente usando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Per le migrazioni eterogenee (da origini completo), valutare i database esistenti con [SSMA](https://aka.ms/get-ssma). È anche possibile usare SSMA per convertire gli oggetti di database ed eseguire la migrazione dello schema per la piattaforma di destinazione.
3. Creare un'istanza del Servizio Migrazione del database di Azure.
4. Creare un progetto di migrazione specificando i database di origine, i database di destinazione e le tabelle da migrare.
5. Avviare il caricamento completo.
6. Selezionare la convalida successiva.
7. Eseguire un passaggio manuale dell'ambiente di produzione al nuovo database basato sul cloud.

## <a name="troubleshooting-and-optimization"></a>Risoluzione dei problemi e ottimizzazione

**D. Configurare un progetto di migrazione in servizio migrazione del database e si verificano problemi di connessione al database di origine. Cosa devo fare?**
Se hai problemi di connessione al sistema di database di origine mentre si lavora sulla migrazione, è possibile creare una macchina virtuale nella rete virtuale con cui viene configurato l'istanza del servizio migrazione del database. Nella macchina virtuale, dovrebbe essere possibile eseguire un test di connect, ad esempio usando un file UDL per testare una connessione a SQL Server o il download Robo 3T per testare le connessioni MongoDB. Se il test della connessione ha esito positivo, non è necessario un problema con la connessione al database di origine. Se il test della connessione non riesce, contattare l'amministratore di rete.

**D. È il motivo per cui il servizio migrazione del Database non disponibile o arrestato?**
Se l'utente arresta esplicitamente Azure Database Migration Service (DMS) o se il servizio rimane inattivo per un periodo di 24 ore, il servizio arresto o auto stato sospeso. In entrambi i casi, il servizio non sarà disponibile e risulterà in stato di arresto.  Per riprendere l'esecuzione delle migrazioni attive, riavviare il servizio.

**D. Quali sono le indicazioni per ottimizzare le prestazioni del servizio migrazione del Database di Azure?**
È possibile eseguire alcune operazioni per velocizzare la migrazione del database con il servizio:

* Usare il piano tariffario per utilizzo generico per più CPU quando si crea l'istanza del servizio per consentire al servizio di sfruttare più vCPU per la parallelizzazione e un trasferimento dei dati più veloce.
* Aumentare temporaneamente le prestazioni dell'istanza di destinazione del database SQL di Azure passando allo SKU del piano Premium durante l'operazione di migrazione dei dati per ridurre al minimo la limitazione delle richieste del database SQL di Azure che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
