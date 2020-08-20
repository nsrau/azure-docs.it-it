---
title: Presentazione di Azure Advisor
description: Usare Azure Advisor per ottimizzare le distribuzioni di Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 27e49fa28e7b02b873ca145d514920784689b407
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654022"
---
# <a name="introduction-to-azure-advisor"></a>Presentazione di Azure Advisor

Informazioni sulle funzionalità chiave di Azure Advisor e risposte alle domande più frequenti.

## <a name="what-is-advisor"></a>Cos'è Advisor?
Advisor è un consulente cloud personalizzato che illustra come seguire le procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza la configurazione delle risorse e i dati di telemetria dell'utilizzo e quindi consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, l'affidabilità (in precedenza denominata disponibilità elevata) e la sicurezza delle risorse di Azure.

Con Advisor, è possibile:
* Ottenere consigli personalizzati, attuabili e proattivi sulle procedure consigliate. 
* Migliorare le prestazioni, la sicurezza e l'affidabilità delle risorse, quando si identificano le opportunità per ridurre la spesa complessiva di Azure.
* Ricevere consigli con azioni proposte incorporate.

È possibile accedere ad Advisor attraverso il [portale di Azure](https://aka.ms/azureadvisordashboard). Accedere al [portale](https://portal.azure.com), individuare **Advisor** nel menu di spostamento oppure cercarlo nel menu **Tutti i servizi**.

Nel dashboard di Advisor vengono visualizzati consigli personalizzati per tutte le sottoscrizioni disponibili.  È possibile applicare filtri per visualizzare i consigli in base a sottoscrizioni e tipi di risorse specifici.  Le indicazioni sono suddivise in cinque categorie: 

* **Affidabilità (in precedenza denominata disponibilità elevata)**: per garantire e migliorare la continuità delle applicazioni cruciali per l'azienda. Per ulteriori informazioni, vedere [consigli sull'affidabilità di Advisor](advisor-high-availability-recommendations.md).
* **Sicurezza**: per rilevare le minacce e le vulnerabilità che potrebbero causare violazioni della sicurezza. Per altre informazioni, vedere [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza).
* **Prestazioni**: per migliorare la velocità delle applicazioni. Per altre informazioni, vedere [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni).
* **Costo**: per ottimizzare e ridurre la spesa complessiva di Azure. Per altre informazioni, vedere [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Azure sui costi).
* **Eccellenza operativa**: per ottenere l'efficienza dei processi e dei flussi di lavoro, la gestibilità delle risorse e le procedure consigliate per la distribuzione. . Per ulteriori informazioni, vedere [consigli sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md).

  ![Tipi di consigli di Advisor](./media/advisor-overview/advisor-dashboard.png)

È possibile fare clic su una categoria per visualizzare l'elenco di consigli disponibili in tale categoria e selezionare un consiglio per ottenere altre informazioni.  È inoltre possibile trovare informazioni sulle azioni che è possibile eseguire per sfruttare un'opportunità o risolvere un problema.

![Categoria di consigli di Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Fare clic sull'azione consigliata per implementare il consiglio.  Verrà aperta un'interfaccia semplice che consentirà di implementare i consigli o di fare riferimento alla documentazione di supporto all'implementazione.  Dopo avere implementato un consiglio, Advisor può richiedere fino a un giorno per riconoscere la modifica.

Se non si intende agire immediatamente, è possibile posporre il consiglio per un intervallo di tempo specificato oppure ignorarlo.  Se non si vuole ricevere consigli per una specifica sottoscrizione o uno specifico gruppo di risorse, è possibile configurare Advisor in modo che generi i consigli solo per le sottoscrizioni e i gruppi di risorse specificati.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-access-advisor"></a>Come si accede ad Advisor?
È possibile accedere ad Advisor attraverso il [portale di Azure](https://aka.ms/azureadvisordashboard). Accedere al [portale](https://portal.azure.com), individuare **Advisor** nel menu di spostamento oppure cercarlo nel menu **Tutti i servizi**.

È possibile visualizzare i consigli di Advisor anche tramite l'interfaccia delle risorse delle macchine virtuali. Scegliere una macchina virtuale, quindi scorrere fino ai consigli di Advisor nel menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quali autorizzazioni sono necessarie per accedere ad Advisor?
 
È possibile accedere ai consigli di Advisor come *proprietario*, *collaboratore*o *lettore* di una sottoscrizione, di un gruppo di risorse o di una risorsa.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Per quali risorse fornisce consigli Advisor?

Advisor fornisce consigli per gateway applicazione, servizi app, set di disponibilità, cache di Azure, Azure Data Factory, database di Azure per MySQL, database di Azure per PostgreSQL, database di Azure per MariaDB, Azure ExpressRoute, Azure Cosmos DB, indirizzi IP pubblici di Azure, analisi sinapsi di Azure, SQL Server, account di archiviazione, profili di gestione traffico e macchine virtuali.

Azure Advisor include anche le raccomandazioni del [Centro sicurezza di Azure](../security-center/security-center-recommendations.md) che possono includere consigli per altri tipi di risorse.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>È possibile posporre o ignorare un consiglio?

Per posporre o ignorare un consiglio, fare clic sul collegamento **Posponi**. Specificare fino a quando posporre il consiglio oppure selezionare **Mai** per ignorarlo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduzione ad Advisor](advisor-get-started.md)
* [Consigli sull'affidabilità di Advisor](advisor-high-availability-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
