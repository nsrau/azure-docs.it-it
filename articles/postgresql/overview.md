---
title: Panoramica del servizio di database relazionale Database di Azure per PostgreSQL
description: Offre una panoramica del servizio di database relazionale Database di Azure per PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 02/28/2018
ms.openlocfilehash: ef7f710fb39c3745ff5a20a73cd4dc5c1fa1fece
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-azure-database-for-postgresql"></a>Che cos'è Database di Azure per PostgreSQL

Database di Azure per PostgreSQL è un servizio di database relazionale in Microsoft Cloud creato per gli sviluppatori e basato sulla versione della community del motore di database open source [PostgreSQL](https://www.postgresql.org/). Questo servizio è disponibile in anteprima pubblica. Database di Azure per PostgreSQL offre:

- Disponibilità elevata incorporata senza costi aggiuntivi
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo
- Scalabilità istantanea in pochi secondi
- Sicurezza per la protezione dei dati sensibili inattivi e in transito
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni
- Sicurezza e conformità di livello aziendale

Tutte le funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Queste funzionalità consentono di concentrarsi sullo sviluppo rapido di applicazioni e accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source preferiti e distribuirla con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze. 

Questo articolo è un'introduzione dei concetti fondamentali su Database di Azure per PostgreSQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità. Per iniziare, vedere queste guide introduttive:

- [Creare un database di Azure per PostgreSQL usando il portale di Azure](quickstart-create-server-database-portal.md)
- [Creare un Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

Per un set di esempi dell'interfaccia della riga di comando di Azure, vedere:

- [Azure CLI samples for Azure Database for PostgreSQL](./sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi
Il servizio Database di Azure per PostgreSQL offre tre piani tariffari: Basic, Utilizzo generico e Con ottimizzazione per la memoria. Ognuno offre diverse funzionalità in termini di risorse per il supporto dei carichi di lavoro dei database. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Per decidere quando aumentare o ridurre le prestazioni, si usano le funzionalità di monitoraggio e avviso predefinite di Azure. Con questi strumenti, è possibile valutare rapidamente l'impatto del passaggio a un piano superiore o inferiore in base alle esigenze attuali o previste in termini di prestazioni o archiviazione. Per informazioni dettagliate, vedere [Avvisi](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio per la disponibilità del 99,99% (non disponibile in anteprima) leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database di Azure per il server PostgreSQL, è possibile sfruttare i vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con Database di Azure per PostgreSQL, ogni piano tariffario offre un set completo di funzionalità e opzioni per la continuità aziendale utilizzabili per diventare e rimanere operativi. È possibile usare un [ripristino temporizzato](howto-restore-server-portal.md) per riportare un database a uno stato precedente, risalente anche a 35 giorni fa. Se nel data center che ospita i database si verifica un'interruzione, è anche possibile ripristinare i database dalle copie con ridondanza geografica di backup recenti.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi di database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per PostgreSQL con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per PostgreSQL usa la crittografia di archiviazione per i dati at-rest. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati dal motore durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia di archiviazione è sempre attiva e non può essere disabilitata.

Per impostazione predefinita, il servizio Database di Azure per PostgreSQL è configurato per richiedere la [sicurezza della connessione SSL](./concepts-ssl-connection-security.md) per i dati che si spostano attraverso la rete. L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.  Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database.

## <a name="next-steps"></a>Passaggi successivi
- Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/postgresql/) per confrontare i costi e usare i calcolatori.
- Per iniziare, [creare il primo database di Azure per PostgreSQL tramite](./quickstart-create-server-database-portal.md).
- Compilare la prima app in Python, PHP, Ruby, C\#, Java, Node.js: [raccolte di connessioni](./concepts-connection-libraries.md)
