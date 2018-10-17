---
title: Panoramica del servizio di database relazionale Database di Azure per MariaDB
description: Panoramica del servizio di database relazionale Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249163"
---
# <a name="what-is-azure-database-for-mariadb"></a>Cos'è Database di Azure per MariaDB?
Database di Azure per MariaDB è un
 servizio di database relazionale di Microsoft cloud basato sul motore di database [MariaDB community edition](https://mariadb.org/download/). Questo servizio è disponibile in anteprima pubblica. Database di Azure per le esigenze di MariaDB:

- Disponibilità elevata integrata senza costi aggiuntivi.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Ridimensionamento in base alle esigenze in pochi secondi.
- Protezione dei dati sensibili inattivi e in movimento.
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Sicurezza e conformità di livello aziendale.

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di app e di accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze.

Questo articolo è un'introduzione ai concetti fondamentali del Database di Azure per MariaDB e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Per iniziare, vedere queste guide introduttive:
- [Creare un server di Database di Azure per MariaDB usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Creare un server di Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi
Nella versione di anteprima, il servizio Database di Azure per MariaDB offre diversi livelli di servizio: Basic, Utilizzo generico e Ottimizzata per la memoria. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Per decidere quando aumentare e ridurre le prestazioni, si usano le funzionalità predefinite di monitoraggio delle prestazioni e avviso, combinate con le classificazioni delle prestazioni basate sui vCore. Con questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione dei vCore in base alle esigenze attuali o previste in termini di prestazioni. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio leader del settore di Azure per la disponibilità del 99,99% (non fornito durante l'anteprima pubblica), con tecnologia di rete globale dei data center di Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni Database di Azure per il server MariaDB, è possibile usufruire dei vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con il Database di Azure per MariaDB, è possibile usare un ripristino temporizzato per riportare un server a uno stato precedente, addirittura fino a 35 giorni prima.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi del database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per MariaDB, con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/en-us/trustcenter/security) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per MariaDB usa la crittografia di archiviazione per i dati inattivi. I dati, inclusi i backup, vengono crittografati su disco, a eccezione dei file temporanei creati dal motore durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia di archiviazione è sempre attiva e non può essere disabilitata.

Per impostazione predefinita, il servizio Database di Azure per MariaDB è configurato per richiedere la [sicurezza di connessione SSL](./concepts-ssl-connection-security.md) per i dati in transito nella rete. L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database.

## <a name="contacts"></a>Contatti
Per eventuali domande o suggerimenti sull'uso di Database di Azure per MariaDB, inviare un'e-mail al team di Database di Azure per MariaDB ([ @Ask Azure DB per MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Si noti che questo non è un alias di supporto tecnico.

Inoltre, prendere in considerazione i seguenti punti di contatto in base alle specifiche esigenze:
- Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione a Database di Azure per MariaDB e avere risposto alla domanda "Cos'è Database di Azure per MariaDB?", è possibile:
- Vedere la pagina relativa ai prezzi per confrontare i costi e usare i calcolatori. [Prezzi](https://azure.microsoft.com/pricing/details/mariadb/)
- Per iniziare, creare il primo server. [Creare un server di Database di Azure per MariaDB usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
