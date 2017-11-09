---
title: Panoramica del servizio di database relazionale Database di Azure per PostgreSQL | Microsoft Docs
description: Offre una panoramica del servizio di database relazionale Database di Azure per PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 10/20/2017
ms.openlocfilehash: 5b5da758e966cc5ca536d7b291be74409f02ca73
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>Che cos'è Database di Azure per PostgreSQL

Database di Azure per PostgreSQL è un servizio di database relazionale in Microsoft Cloud creato per gli sviluppatori e basato sulla versione della community del motore di database [PostgreSQL](https://www.postgresql.org/) open source. Questo servizio è disponibile in anteprima pubblica. Database di Azure per PostgreSQL offre:

- Disponibilità elevata integrata senza costi aggiuntivi.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Scalabilità in tempo reale in pochi secondi.
- Con protezione dei dati sensibili sia quando sono inattivi sia quando sono in movimento.
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Sicurezza e conformità di livello aziendale.

Tutte le funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Queste funzionalità consentono di concentrarsi sullo sviluppo rapido di applicazioni e accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e a renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze. 

Questo articolo è un'introduzione dei concetti fondamentali su Database di Azure per PostgreSQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità. Per iniziare, vedere queste guide introduttive:

- [Creare un database di Azure per PostgreSQL usando il portale di Azure](quickstart-create-server-database-portal.md)
- [Creare un Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)

Per un set di esempi dell'interfaccia della riga di comando di Azure, vedere:

- [Azure CLI samples for Azure Database for PostgreSQL](./sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi
L'anteprima di Database di Azure per MySQL offre due livelli di servizio: Basic e Standard. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi scalare in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Per decidere quando aumentare e ridurre le prestazioni, si usano le funzionalità predefinite di monitoraggio delle prestazioni e avviso, combinate con le classificazioni delle prestazioni basate sulle unità di calcolo. Usando questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle unità di calcolo in base alle esigenze correnti o previste relative alle prestazioni. Per informazioni dettagliate, vedere [Avvisi](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio per la disponibilità del 99,99% (non disponibile in anteprima) leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database di Azure per il server PostgreSQL, è possibile sfruttare i vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con Database di Azure per PostgreSQL, ogni livello di servizio offre un set completo di funzionalità di continuità aziendale e opzioni che è possibile usare per diventare e rimanere operativi. È possibile usare un [ripristino temporizzato](howto-restore-server-portal.md) per riportare un database a uno stato precedente, risalente anche a 35 giorni fa. Se nel data center che ospita i database si verifica un'interruzione, è anche possibile ripristinare i database dalle copie con ridondanza geografica di backup recenti.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi di database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per PostgreSQL con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per PostgreSQL usa la crittografia di archiviazione per i dati at-rest. I dati che includono backup vengono crittografati su disco, a eccezione dei file temporanei creati dal motore durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia di archiviazione è sempre attiva e non può essere disabilitata.

Per impostazione predefinita, il servizio Database di Azure per PostgreSQL è configurato per richiedere la [sicurezza della connessione SSL](./concepts-ssl-connection-security.md) per i dati che si spostano attraverso la rete. L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.  Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database.

## <a name="next-steps"></a>Passaggi successivi
- Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/postgresql/) per confrontare i costi e usare i calcolatori.
- Per iniziare, [creare il primo database di Azure per PostgreSQL tramite](./quickstart-create-server-database-portal.md).
- Compilare la prima app in Python, PHP, Ruby, C\#, Java, Node.js: [raccolte di connessioni](./concepts-connection-libraries.md)
