---
title: Domande frequenti-servizio migrazione del database di Azure
description: Domande frequenti sull'uso del servizio migrazione del database di Azure per eseguire le migrazioni di database.
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
ms.openlocfilehash: 3383a16624ecf045753609865931f9d0c44b6b3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196579"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Domande frequenti sull'uso del servizio migrazione del database di Azure

Questo articolo elenca le domande frequenti sull'uso del servizio migrazione del database di Azure insieme alle risposte correlate.

## <a name="overview"></a>Panoramica

**D. che cos'è il servizio migrazione del database di Azure?**
Il servizio migrazione del database di Azure è un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi. Il servizio è attualmente disponibile a livello generale, con attività di sviluppo continue incentrate su:

* Affidabilità e prestazioni.
* Aggiunta iterativa di coppie origine-destinazione.
* Investimento continuo su migrazioni senza problemi.

**D. quali coppie di origine/destinazione attualmente supportano il servizio migrazione del database di Azure?**
Il servizio attualmente supporta un'ampia gamma di coppie di origine/destinazione o scenari di migrazione. Per un elenco completo dello stato di ogni scenario di migrazione disponibile vedere l'articolo [Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Altri scenari di migrazione sono in anteprima e richiedono l'invio di una candidatura tramite il sito di anteprima DMS. Per un elenco completo degli scenari in anteprima e per iscriversi a una di queste offerte, vedere il [sito di anteprima DMS](https://aka.ms/dms-preview/).

**D. quali versioni di SQL Server supportano il servizio migrazione del database di Azure come origine?**
Quando si esegue la migrazione da SQL Server, le origini supportate per il servizio migrazione del database di Azure sono SQL Server da 2005 a SQL Server 2019.

**D: quando si usa il servizio migrazione del database di Azure, qual è la differenza tra una migrazione offline e una migrazione in linea?**
È possibile usare il servizio migrazione del database di Azure per eseguire migrazioni offline e online. Con una migrazione *offline* , il tempo di inattività dell'applicazione viene avviato all'avvio della migrazione. Con una migrazione in *linea* , il tempo di inattività è limitato al tempo necessario per il superamento al termine della migrazione. È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) del servizio migrazione del database di Azure.

**D. come si confronta il servizio migrazione del database di Azure con altri strumenti di migrazione del database Microsoft, ad esempio il database Migration Assistant (DMA) o SQL Server Migration Assistant (SSMA)?**
Il servizio migrazione del database di Azure è il metodo preferito per la migrazione dei database Microsoft Azure su larga scala. Per informazioni dettagliate sul confronto tra il servizio migrazione del database di Azure e altri strumenti di migrazione del database Microsoft e per consigli sull'uso del servizio per diversi scenari, vedere il post di Blog relativo alla [differenziazione degli strumenti e dei servizi di migrazione di database di Microsoft](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**D. come si confronta il servizio migrazione del database di Azure con la Azure Migrate offerta?**
Azure Migrate facilita la migrazione di macchine virtuali locali in Azure IaaS. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione delle macchine virtuali locali in Azure. Azure Migrate è utile per le migrazioni in modalità lift-and-shift di carichi di lavoro basati su VM locali a VM IaaS di Azure. Tuttavia, a differenza del servizio migrazione del database di Azure, Azure Migrate non è un'offerta di servizio di migrazione del database specializzata per le piattaforme di database relazionali di Azure PaaS, ad esempio il database SQL di Azure o Istanza gestita Azure SQL.

## <a name="setup"></a>Configurazione

**D. quali sono i prerequisiti per l'utilizzo del servizio migrazione del database di Azure?**
Sono necessari alcuni prerequisiti per garantire che il servizio migrazione del database di Azure venga eseguito senza problemi durante l'esecuzione delle migrazioni del database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:

* Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Verificare che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino le porte di comunicazione seguenti 443, 53, 9354, 445, 12000. Per informazioni più dettagliate sul filtro del traffico NSG per la rete virtuale, vedere l'articolo [filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

Per un elenco di tutti i prerequisiti necessari per competere con scenari di migrazione specifici usando il servizio migrazione del database di Azure, vedere le esercitazioni correlate nella [documentazione](https://docs.microsoft.com/azure/dms/dms-overview) del servizio migrazione del database di azure in docs.Microsoft.com.

**D. Ricerca per categorie trovare l'indirizzo IP per il servizio migrazione del database di Azure in modo che sia possibile creare un elenco Consenti per le regole del firewall usate per accedere al database di origine per la migrazione?**
Potrebbe essere necessario aggiungere regole del firewall che consentono al servizio migrazione del database di Azure di accedere al database di origine per la migrazione. L'indirizzo IP del servizio è dinamico, ma se si usa ExpressRoute, questo indirizzo viene assegnato privatamente dalla rete aziendale. Il modo più semplice per identificare l'indirizzo IP appropriato consiste nel cercare nello stesso gruppo di risorse della risorsa del servizio migrazione del database di Azure di cui è stato effettuato il provisioning per trovare l'interfaccia di rete associata. Il nome della risorsa dell'interfaccia di rete inizia in genere con il prefisso NIC ed è seguito da una sequenza alfanumerica univoca, ad esempio NIC-jj6tnztnmarpsskr82rbndyp. Se si seleziona questa risorsa dell'interfaccia di rete, è possibile visualizzare l'indirizzo IP che deve essere incluso nell'elenco indirizzi consentiti nella pagina del portale di Azure della panoramica.

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

**D. Ricerca per categorie configurare una Rete virtuale di Microsoft Azure?**
Sebbene più esercitazioni di Microsoft possano illustrare il processo di configurazione di una rete virtuale, la documentazione ufficiale viene visualizzata nell'articolo [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Utilizzo

**D. che cos'è un riepilogo dei passaggi necessari per usare il servizio migrazione del database di Azure per eseguire una migrazione del database?**
Durante una semplice migrazione di database tipica, è necessario:

1. Creare uno o più database di destinazione.
2. Valutare i database di origine.
    * Per le migrazioni omogenee, è necessario valutare i database esistenti usando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Per le migrazioni eterogenee (dalle origini di concorrenza), valutare i database esistenti con [SSMA](https://aka.ms/get-ssma). È inoltre possibile utilizzare SSMA per convertire gli oggetti di database ed eseguire la migrazione dello schema alla piattaforma di destinazione.
3. Creare un'istanza del Servizio Migrazione del database di Azure.
4. Creare un progetto di migrazione specificando i database di origine, i database di destinazione e le tabelle di cui eseguire la migrazione.
5. Avviare il caricamento completo.
6. Selezionare la convalida successiva.
7. Eseguire un passaggio manuale dell'ambiente di produzione al nuovo database basato sul cloud.

## <a name="troubleshooting-and-optimization"></a>Risoluzione dei problemi e ottimizzazione

**D. Sto configurando un progetto di migrazione in DMS e ho difficoltà a connettersi al database di origine. Cosa dovrei fare?**
In caso di problemi di connessione al sistema di database di origine durante la migrazione, creare una macchina virtuale nella rete virtuale con cui si configura l'istanza di DMS. Nella macchina virtuale dovrebbe essere possibile eseguire un test di connessione, ad esempio usando un file UDL per testare una connessione a SQL Server o scaricare Robo 3T per testare le connessioni MongoDB. Se il test della connessione ha esito positivo, non è necessario un problema con la connessione al database di origine. Se il test della connessione non riesce, contattare l'amministratore di rete.

**D. perché il servizio migrazione del database di Azure non è disponibile o è stato arrestato?**
Se l'utente arresta in modo esplicito il servizio migrazione del database di Azure o se il servizio è inattivo per un periodo di 24 ore, il servizio si troverà in uno stato interrotto o sospeso automaticamente. In entrambi i casi, il servizio non sarà disponibile e risulterà in stato di arresto.  Per riprendere l'esecuzione delle migrazioni attive, riavviare il servizio.

**D. sono disponibili raccomandazioni per l'ottimizzazione delle prestazioni del servizio migrazione del database di Azure?**
È possibile eseguire alcune operazioni per velocizzare la migrazione del database con il servizio:

* Usare il piano tariffario per utilizzo generico per più CPU quando si crea l'istanza del servizio per consentire al servizio di sfruttare più vCPU per la parallelizzazione e un trasferimento dei dati più veloce.
* Aumentare temporaneamente le prestazioni dell'istanza di destinazione del database SQL di Azure passando allo SKU del piano Premium durante l'operazione di migrazione dei dati per ridurre al minimo la limitazione delle richieste del database SQL di Azure che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
