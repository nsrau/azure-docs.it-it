---
title: Panoramica del servizio di database relazionale Database di Azure per MySQL
description: Panoramica del servizio di database relazionale Database di Azure per MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 82d96424569daf481e31ab04c08dcb5e63e58476
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Database di Azure per MySQL:
Database di Azure per MySQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database [MySQL Community Edition](https://www.mysql.com/products/community/). Questo servizio è disponibile in anteprima pubblica. Database di Azure per MySQL offre:

- Disponibilità elevata integrata senza costi aggiuntivi.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Scalabilità in tempo reale in pochi secondi.
- Protezione dei dati sensibili inattivi e in movimento.
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Sicurezza e conformità di livello aziendale.

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di app e di accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze.

![Diagramma concettuale di Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Questo articolo è un'introduzione dei concetti fondamentali su Database di Azure per MySQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Per iniziare, vedere queste guide introduttive:
- [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Per un set di esempi dell'interfaccia della riga di comando di Azure, vedere:
- [Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi
Nella versione di anteprima, il servizio Database di Azure per MySQL offre diversi livelli di servizio: Basic, Utilizzo generico e Con ottimizzazione per la memoria. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Per decidere quando aumentare e ridurre le prestazioni, si usano le funzionalità predefinite di monitoraggio delle prestazioni e avviso, combinate con le classificazioni delle prestazioni basate sui vCore. Con questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione dei vCore in base alle esigenze attuali o previste in termini di prestazioni. Per informazioni dettagliate, vedere [Avvisi](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio per la disponibilità del 99,99% leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database di Azure per il server MySQL, è possibile sfruttare i vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con Database di Azure per MySQL, è possibile usare un ripristino temporizzato per ripristinare uno stato precedente di un server, addirittura fino a 35 giorni prima.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi di database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per MySQL con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per MySQL usa la crittografia di archiviazione per i dati at-rest. I dati, inclusi i backup, vengono crittografati su disco, a eccezione dei file temporanei creati dal motore durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia di archiviazione è sempre attiva e non può essere disabilitata.

Per impostazione predefinita, il servizio Database di Azure per MySQL è configurato per richiedere la [sicurezza della connessione SSL](./concepts-ssl-connection-security.md) per i dati che si spostano attraverso la rete. L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.  Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione a Database di Azure per MySQL e avere ottenuto informazioni su Database di Azure per MySQL, è possibile:
- Vedere la pagina relativa ai prezzi per confrontare i costi e usare i calcolatori. [Prezzi](https://azure.microsoft.com/pricing/details/mysql/)
- Per iniziare, creare il primo server. [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- Compilare la prima app usando il linguaggio preferito: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
