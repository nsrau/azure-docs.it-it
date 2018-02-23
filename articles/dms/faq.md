---
title: Domande frequenti sull'uso del Servizio Migrazione del database di Azure | Microsoft Docs
description: Domande frequenti sull'uso del Servizio Migrazione del database di Azure per eseguire migrazioni di database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 87bd27147d20fec8c5839b744d70f215e2c1ec47
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Domande frequenti sull'uso del Servizio Migrazione del database di Azure
Questo articolo elenca le domande frequenti sull'uso del Servizio Migrazione del database di Azure e le relative risposte.

### <a name="q-what-is-azure-database-migration-service"></a>D: Che cos'è il Servizio Migrazione del database di Azure?
Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Il servizio è attualmente in anteprima pubblica e le relative attività di sviluppo si concentrano su:
- Affidabilità e prestazioni.
- Aggiunta iterativa di coppie origine-destinazione.
- Investimento continuo su migrazioni senza problemi.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>D: Quali coppie di origine-destinazione sono attualmente supportate dal Servizio Migrazione del database di Azure?
Il servizio in anteprima pubblica attualmente supporta le migrazioni da SQL Server al database SQL di Azure ed è ora possibile andare al portale di Azure per iniziare a usare il Servizio Migrazione del database di Azure per questo scenario. Altre coppie di origine-destinazione, ad esempio da SQL Server a Istanza gestita di database SQL di Azure e da Oracle al database SQL di Azure sono disponibili tramite un'anteprima privata limitata. Per poter partecipare all'anteprima privata limitata di questi scenari, iscriversi [qui](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>D: Quali sono le differenze tra il Servizio Migrazione del database di Azure e altri strumenti di migrazione di database Microsoft, ad esempio Database Migration Assistant (DMA) o SQL Server Migration Assistant (SSMA)?
Il Servizio Migrazione del database di Azure è il metodo preferito per la migrazione di database in Microsoft Azure su larga scala. Per altre informazioni dettagliate sulle differenze tra il Servizio Migrazione del database di Azure e altri strumenti di migrazione di database Microsoft e per raccomandazioni sull'uso del servizio in diversi scenari, vedere il post di blog [Differentiating Microsoft’s Database Migration Tools and Services](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/) (Differenze tra gli strumenti e i servizi di migrazione di database di Microsoft).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>D: Quali sono le differenze tra il Servizio Migrazione del database di Azure e l'offerta Azure Migrate?
Il servizio Azure Migrate supporta la migrazione di macchine virtuali locali a IaaS di Azure. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione delle macchine virtuali locali in Azure. Azure Migrate è utile per le migrazioni in modalità lift-and-shift di carichi di lavoro basati su VM locali a VM IaaS di Azure. Tuttavia, a differenza del Servizio Migrazione del database di Azure, Azure Migrate non è un'offerta di servizio di migrazione di database specializzata per le piattaforme di database relazionale PaaS di Azure, ad esempio il database SQL di Azure, SQL Azure o Istanza gestita di database SQL di Azure.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>D: Quali sono i passaggi necessari per usare il Servizio Migrazione del database di Azure per eseguire una migrazione di database?
Durante una semplice migrazione di database tipica, è necessario:
1.  Creare uno o più database di destinazione.
2.  Eseguire la migrazione dello schema di database usando [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Creare un'istanza del Servizio Migrazione del database di Azure.
4.  Creare un progetto di migrazione specificando i database di origine, i database di destinazione e le tabelle di cui eseguire la migrazione.
5.  Avviare il carico completo.
6.  Selezionare la convalida successiva.
7.  Eseguire un passaggio manuale dell'ambiente di produzione al nuovo database basato sul cloud. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>D: Quali sono i prerequisiti per usare il Servizio Migrazione del database di Azure?
Esistono diversi prerequisiti obbligatori per assicurare che il Servizio Migrazione del database di Azure venga eseguito senza problemi quando si eseguono migrazioni di database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.
In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:
- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
 
Per un elenco di tutti i prerequisiti obbligatori per supportare scenari di migrazione specifici con il Servizio Migrazione del database di Azure, vedere le esercitazioni correlate nella [documentazione](https://docs.microsoft.com/en-us/azure/dms/dms-overview) del Servizio Migrazione del database di Azure in docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>D: Come è possibile trovare l'indirizzo IP per il Servizio Migrazione del database di Azure per poter creare un elenco indirizzi consentiti per le regole del firewall usate per accedere al database di origine per la migrazione?
Potrebbe essere necessario aggiungere regole del firewall che consentono al Servizio Migrazione del database di Azure di accedere al database di origine per la migrazione. L'indirizzo IP per il servizio è dinamico, ma, se si usa Express Route, questo indirizzo viene assegnato privatamente dalla rete aziendale. Il modo più semplice per identificare l'indirizzo IP appropriato è cercare nello stesso gruppo di risorse della risorsa del Servizio Migrazione del database di Azure di cui viene effettuato il provisioning per trovare l'interfaccia di rete associata. Il nome della risorsa dell'interfaccia di rete inizia in genere con il prefisso NIC ed è seguito da una sequenza alfanumerica univoca, ad esempio NIC-jj6tnztnmarpsskr82rbndyp. Se si seleziona questa risorsa dell'interfaccia di rete, è possibile visualizzare l'indirizzo IP che deve essere incluso nell'elenco indirizzi consentiti nella pagina del portale di Azure della panoramica.

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

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>D: Quali sono le indicazioni per ottimizzare le prestazioni del Servizio Migrazione del database di Azure?
È possibile eseguire alcune operazioni per velocizzare la migrazione del database con il servizio:
- Usare il piano tariffario per utilizzo generico per più CPU quando si crea l'istanza del servizio per consentire al servizio di sfruttare più vCPU per la parallelizzazione e un trasferimento dei dati più veloce.
- Aumentare temporaneamente le prestazioni dell'istanza di destinazione del database SQL di Azure passando allo SKU del piano Premium durante l'operazione di migrazione dei dati per ridurre al minimo la limitazione delle richieste del database SQL di Azure che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>D: Come si configura una rete virtuale di Azure?
Oltre alle numerose esercitazioni Microsoft che illustrano il processo di configurazione di una rete virtuale di Azure, è disponibile la documentazione ufficiale nell'articolo[Rete virtuale di Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview).

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>D: Dove è possibile lasciare commenti e suggerimenti relativi al Servizio Migrazione del database di Azure?
Per esprimere un'opinione, inviare commenti e suggerimenti e idee sul Servizio Migrazione del database di Azure tramite UserVoice, [qui](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e la Disponibilità a livello di area durante l'anteprima pubblica, vedere l'articolo [Informazioni sull'anteprima del Servizio Migrazione del database di Azure](dms-overview.md). 