---
title: Panoramica - Database di Azure per MySQL
description: Informazioni sul servizio Database di Azure per MySQL, un servizio di database relazionale in Microsoft Cloud basato su MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 51c194ca9b091bc685f293320750da55925ad49d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91565616"
---
# <a name="what-is-azure-database-for-mysql"></a>Database di Azure per MySQL:

Database di Azure per MySQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database [MySQL Community Edition](https://www.mysql.com/products/community/), versioni 5.6, 5.7 e 8.0, disponibile con licenza GPLv2. Database di Azure per MySQL offre:

- Disponibilità elevata predefinita.
- Protezione dati con backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Manutenzione automatica per hardware, sistema operativo e motore di database sottostanti per un servizio sempre sicuro e aggiornato.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Scalabilità elastica in pochi secondi.
- Controlli di ottimizzazione dei costi con la possibilità di arrestare/avviare il server. 
- Sicurezza di livello aziendale e conformità leader di settore per proteggere i dati sensibili inattivi e in transito.
- Monitoraggio e automazione per semplificare la gestione e il monitoraggio per distribuzioni su larga scala.
- Esperienza di supporto leader di settore.

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di app e di accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Diagramma concettuale di Azure Database for MySQL":::

## <a name="deployment-models"></a>Modelli di distribuzione

Database di Azure per MySQL basato su MySQL Community Edition è disponibile in due modalità di distribuzione:
- Server unico 
- Server flessibile (anteprima)
  
### <a name="azure-database-for-mysql---single-server"></a>Database di Azure per MySQL - Server singolo

Il server singolo di Database di Azure per MySQL è un servizio di database completamente gestito con requisiti minimi per le personalizzazioni di database. La piattaforma server singolo è progettata per gestire la maggior parte delle funzioni di gestione di database, ad esempio l'applicazione di patch, i backup, la disponibilità elevata e la sicurezza con configurazione e controllo minimi dell'utente. L'architettura è ottimizzata per la disponibilità elevata predefinita con disponibilità del 99,99% in una singola zona di disponibilità. Supporta la versione community di MySQL 5.6, 5.7 e 8.0. Il servizio è attualmente disponibile a livello generale in un'ampia gamma di [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/).

L'opzione di distribuzione Server singolo offre tre piani tariffari: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Ognuno offre diverse funzionalità in termini di risorse per il supporto dei carichi di lavoro dei database. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](concepts-pricing-tiers.md).

I server singoli sono ideali per applicazioni native per il cloud progettate per gestire l'applicazione automatizzata di patch senza la necessità di un controllo granulare sulla pianificazione dell'applicazione di patch e sulle impostazioni di configurazione di MySQL personalizzate. 

Per una panoramica dettagliata della modalità di distribuzione server singolo, vedere la [panoramica del server singolo](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Database di Azure per MySQL - Server flessibile (anteprima)

Il server flessibile di Database di Azure per MySQL è un servizio di database completamente gestito progettato per offrire un controllo più granulare e una maggiore flessibilità rispetto alle funzioni di gestione e alle impostazioni di configurazione del database. In generale, il servizio offre maggiori flessibilità e personalizzazioni in base ai requisiti dell'utente. L'architettura del server flessibile consente agli utenti di scegliere la disponibilità elevata all'interno di una singola zona di disponibilità e tra più zone di disponibilità. I server flessibili forniscono controlli di ottimizzazione dei costi migliori con la possibilità di arrestare/avviare il server e il livello di calcolo della possibilità di burst, una soluzione ideale per i carichi di lavoro che non necessitano di capacità di calcolo completa in modo continuo. Il servizio supporta attualmente la versione community di MySQL 5.7 con i piani per aggiungere presto le versioni più recenti. Il servizio è attualmente disponibile in anteprima pubblica in un'ampia gamma di [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/).

I server flessibili sono ideali per 
- Sviluppi di applicazioni che richiedono un controllo e personalizzazioni migliori.
- Disponibilità elevata di ridondanza della zona
- Finestre di manutenzione gestite

Per una panoramica dettagliata della modalità di distribuzione server flessibile, vedere la [panoramica del server flessibile](flexible-server/overview.md).

## <a name="contacts"></a>Contatti
Per eventuali domande o suggerimenti sull'uso di Database di Azure per MySQL, inviare un messaggio di posta elettronica al team di Database di Azure per MySQL ([@Ask Azure DB per MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Questo indirizzo di posta elettronica non è un alias del supporto tecnico.

Inoltre, prendere in considerazione i seguenti punti di contatto in base alle specifiche esigenze:

- Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passaggi successivi

Ottenere altre informazioni sulle due modalità di distribuzione per Database di Azure per MySQL e scegliere le opzioni appropriate in base alle proprie esigenze.

- [Server singolo](single-server/index.yml)
- [Server flessibile](flexible-server/index.yml)
- [Scegliere l'opzione di distribuzione MySQL appropriata per il carico di lavoro](select-right-deployment-type.md)
