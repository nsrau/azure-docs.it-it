---
title: Domande frequenti - Servizio di migrazione del database di AzureFAQ - Azure Database Migration Service
description: Domande frequenti sull'uso del servizio di migrazione del database di Azure per eseguire migrazioni di database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650982"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Domande frequenti sull'uso del servizio di migrazione del database di AzureFAQ about using Azure Database Migration Service

Questo articolo elenca le domande frequenti sull'uso del servizio migrazione di database di Azure con risposte correlate.

## <a name="overview"></a>Panoramica

**D: Che cos'è il servizio di migrazione del database di Azure?**
Il servizio di migrazione del database di Azure è un servizio completamente gestito progettato per consentire migrazioni senza soluzione di continuità da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Il servizio è attualmente in Disponibilità Generale, con sforzi di sviluppo in corso incentrati su:

* Affidabilità e prestazioni.
* Aggiunta iterativa di coppie origine-destinazione.
* Investimento continuo su migrazioni senza problemi.

**D: Quali coppie di origine/destinazione sono attualmente supportate dal servizio Migrazione del database di Azure?**
Il servizio supporta attualmente un'ampia gamma di coppie di origine/destinazione o scenari di migrazione. Per un elenco completo dello stato di ogni scenario di migrazione disponibile vedere l'articolo [Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Altri scenari di migrazione sono in anteprima e richiedono l'invio di una nomination tramite il sito di anteprima DMS. Per un elenco completo degli scenari in anteprima e per iscriversi per partecipare a una di queste offerte, vedere il [sito Anteprima DMS](https://aka.ms/dms-preview/).

**D: Quali versioni di SQL Server supporta il servizio di migrazione del database di Azure come origine?**
Quando si esegue la migrazione da SQL Server, le origini supportate per il servizio di migrazione del database di Azure sono da SQL Server 2005 a SQL Server 2019.

**D: Quando si usa il servizio di migrazione del database di Azure, qual è la differenza tra una migrazione offline e una migrazione online?**
È possibile usare il servizio migrazione del database di Azure per eseguire migrazioni offline e online. Con una migrazione *offline,* il tempo di inattività dell'applicazione inizia all'avvio della migrazione. Con una migrazione *online,* i tempi di inattività sono limitati al tempo necessario per ridurre la migrazione. È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina [dei prezzi](https://azure.microsoft.com/pricing/details/database-migration/) del servizio di migrazione del database di Azure.For more information, see the Azure Database Migration Service pricing page.

**D: Quali sono le conversioni del servizio di migrazione del database di Azure rispetto ad altri strumenti di migrazione del database Microsoft, ad esempio L'Assistente migrazione database (DMA) o Assistente migrazione SQL Server (SSMA)?**
Il servizio di migrazione del database di Azure è il metodo preferito per la migrazione del database in Microsoft Azure su larga scala. Per ulteriori informazioni sul confronto tra il servizio di migrazione del database di Azure e altri strumenti di migrazione del database Microsoft e per suggerimenti sull'utilizzo del servizio per vari scenari, vedere il post di blog [Differenziazione](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)degli strumenti e dei servizi di migrazione di database di Microsoft .

**D: Quali sono le informazioni sul confronto tra il servizio di migrazione del database di Azure e l'offerta di Azure Migrate?**
Azure Migrate assists with migration of on-premises virtual machines to Azure IaaS. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione delle macchine virtuali locali in Azure. Azure Migrate è utile per le migrazioni in modalità lift-and-shift di carichi di lavoro basati su VM locali a VM IaaS di Azure. Tuttavia, a differenza del servizio di migrazione del database di Azure, Azure Migrate non è un servizio di migrazione del database specializzato che offre piattaforme di database relazionali di Azure PaaS, ad esempio il database SQL di Azure o l'istanza gestita del database SQL di Azure.However, unlike Azure Database Migration Service, Azure Migrate isn't a specialized database migration service offering for Azure PaaS relational database platforms such as Azure SQL Database or Azure SQL Database Managed Instance.

## <a name="setup"></a>Configurazione

**D: Quali sono i prerequisiti per l'utilizzo del servizio Migrazione del database di Azure?**
Esistono diversi prerequisiti necessari per garantire che il servizio Migrazione del database di Azure venga eseguito senza problemi quando si eseguono migrazioni di database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:

* Creare una rete virtuale di Microsoft Azure per il servizio di migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Verificare che le regole del gruppo di sicurezza di rete di rete virtuale non blocchino le seguenti porte di comunicazione 443, 53, 9354, 445, 12000. Per ulteriori informazioni sul filtro del traffico del gruppo di sicurezza di rete virtuale, vedere l'articolo [Filtrare](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)il traffico di rete con i gruppi di sicurezza di rete .
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

Per un elenco di tutti i prerequisiti necessari per competere scenari di migrazione specifici tramite il servizio di migrazione del database di Azure, vedere le esercitazioni correlate nella [documentazione](https://docs.microsoft.com/azure/dms/dms-overview) del servizio Migrazione di database di Azure in docs.microsoft.com.

**D: Come è possibile trovare l'indirizzo IP per il servizio di migrazione del database di Azure in modo da poter creare un elenco Consenti per le regole del firewall usate per accedere al database di origine per la migrazione?**
Potrebbe essere necessario aggiungere regole del firewall che consentano al servizio Migrazione del database di Azure di accedere al database di origine per la migrazione. L'indirizzo IP per il servizio è dinamico, ma se si usa ExpressRoute, questo indirizzo viene assegnato privatamente dalla rete aziendale. Il modo più semplice per identificare l'indirizzo IP appropriato consiste nell'esaminare lo stesso gruppo di risorse della risorsa del servizio migrazione del database di Azure di cui è stato eseguito il provisioning per trovare l'interfaccia di rete associata. Il nome della risorsa dell'interfaccia di rete inizia in genere con il prefisso NIC ed è seguito da una sequenza alfanumerica univoca, ad esempio NIC-jj6tnztnmarpsskr82rbndyp. Se si seleziona questa risorsa dell'interfaccia di rete, è possibile visualizzare l'indirizzo IP che deve essere incluso nell'elenco indirizzi consentiti nella pagina del portale di Azure della panoramica.

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

**D: Come si configura una rete virtuale di Microsoft Azure?**
Mentre più esercitazioni Microsoft che possono illustrare il processo di configurazione di una rete virtuale, la documentazione ufficiale viene visualizzata nell'articolo [Rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)di Azure .

## <a name="usage"></a>Uso

**D: Qual è un riepilogo dei passaggi necessari per usare il servizio di migrazione del database di Azure per eseguire una migrazione del database?**
Durante una semplice migrazione di database tipica, è necessario:

1. Creare uno o più database di destinazione.
2. Valutare i database di origine.
    * Per le migrazioni omogenee, valutare i database esistenti utilizzando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Per migrazioni eterogenee (da origini di concorrenza), valutare i database esistenti con [SSMA](https://aka.ms/get-ssma). Si utilizza anche SSMA per convertire gli oggetti di database ed eseguire la migrazione dello schema nella piattaforma di destinazione.
3. Creare un'istanza del Servizio Migrazione del database di Azure.
4. Creare un progetto di migrazione specificando i database di origine, i database di destinazione e le tabelle di cui eseguire la migrazione.
5. Avviare il carico completo.
6. Selezionare la convalida successiva.
7. Eseguire un passaggio manuale dell'ambiente di produzione al nuovo database basato sul cloud.

## <a name="troubleshooting-and-optimization"></a>Risoluzione dei problemi e ottimizzazione

**D. Sto configurando un progetto di migrazione in DMS e ho difficoltà a connettermi al database di origine. Cosa dovrei fare?**
In caso di problemi di connessione al sistema di database di origine durante la migrazione, creare una macchina virtuale nella rete virtuale con cui si configura l'istanza DMS. Nella macchina virtuale dovrebbe essere possibile eseguire un test di connessione, ad esempio utilizzando un file UDL per testare una connessione a SQL Server o scaricare Robo 3T per testare le connessioni MongoDB. Se il test di connessione ha esito positivo, non si dovrebbe avere un problema con la connessione al database di origine. Se il test della connessione non riesce, contattare l'amministratore di rete.

**D: Perché il servizio di migrazione del database di Azure non è disponibile o è stato arrestato?**
Se l'utente arresta in modo esplicito il servizio di migrazione del database di Azure (DMS) o se il servizio è inattivo per un periodo di 24 ore, il servizio sarà in uno stato di arresto o sospensione automatica. In entrambi i casi, il servizio non sarà disponibile e risulterà in stato di arresto.  Per riprendere l'esecuzione delle migrazioni attive, riavviare il servizio.

**D: Sono disponibili suggerimenti per l'ottimizzazione delle prestazioni del servizio migrazione di database di Azure?**
È possibile eseguire alcune operazioni per velocizzare la migrazione del database con il servizio:

* Usare il piano tariffario per utilizzo generico per più CPU quando si crea l'istanza del servizio per consentire al servizio di sfruttare più vCPU per la parallelizzazione e un trasferimento dei dati più veloce.
* Aumentare temporaneamente le prestazioni dell'istanza di destinazione del database SQL di Azure passando allo SKU del piano Premium durante l'operazione di migrazione dei dati per ridurre al minimo la limitazione delle richieste del database SQL di Azure che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
