---
title: Panoramica del servizio di database relazionale Database di Azure per MariaDB
description: Panoramica del servizio di database relazionale Database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 03/20/2019
ms.openlocfilehash: a5d00c24531099e66afcb6ccf07cfdf99abd28d1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "60846243"
---
# <a name="what-is-azure-database-for-mariadb"></a>Cos'è Database di Azure per MariaDB?

Database di Azure per MariaDB è un servizio di database relazionale nel cloud Microsoft. Database di Azure per MariaDB è basato sul motore di database dell'[edizione per la community di MariaDB](https://mariadb.org/download/) (disponibile con licenza GPLv2), versione 10.2.

Database di Azure per MariaDB offre:

- Disponibilità elevata integrata senza costi aggiuntivi.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Ridimensionamento in base alle esigenze in pochi secondi.
- Sicurezza per la protezione dei dati sensibili inattivi e in transito.
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Sicurezza e conformità di livello aziendale.

Queste funzionalità richiedono pochissima amministrazione. Sono disponibili senza costi aggiuntivi. Database di Azure per MariaDB consente di sviluppare rapidamente app e accelerare i tempi di immissione sul mercato. Non occorre dedicare tempo e risorse preziosi alla gestione di macchine virtuali e infrastruttura. È anche possibile continuare a sviluppare le applicazioni usando gli strumenti open source e la piattaforma di propria scelta. Completare i progetti con la velocità e l'efficienza fondamentali per l'azienda, senza dover apprendere nuove competenze.

Per altre informazioni sui concetti di base e le principali funzionalità in Database di Azure per MariaDB, tra cui prestazioni, scalabilità e gestibilità, vedere queste guide introduttive:

- [Creare un server di Database di Azure per MariaDB usando il portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Creare un server di Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi

Database di Azure per MariaDB offre vari livelli di servizio: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere  [Piani tariffari](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

Come si decide quando aumentare o ridurre le prestazioni? È possibile usare le funzionalità predefinite di monitoraggio delle prestazioni e avviso di Database di Azure per MariaDB, in combinazione con le classificazioni delle prestazioni basate sui vCore. Con questi strumenti, è possibile valutare rapidamente gli effetti dell'aumento o della riduzione dei vCore in base alle esigenze attuali o previste in termini di prestazioni. Per informazioni dettagliate, vedere [Avvisi](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda

Il contratto di servizio di Azure con disponibilità del 99,99% leader del settore si basa su una rete globale di data center gestiti da Microsoft. La rete consente di mantenere l'app in esecuzione 24/7. Si può usufruire dei vantaggi offerti da sicurezza predefinita, tolleranza di errore e protezione dei dati in Database di Azure per MariaDB. Con il Database di Azure per MariaDB, è possibile usare un ripristino temporizzato per riportare un server a uno stato precedente, addirittura fino a 35 giorni prima.

## <a name="secure-your-data"></a>Protezione dei dati

I servizi di database di Azure vantano una tradizione di sicurezza dei dati ereditata anche da Database di Azure per MariaDB. Database di Azure per MariaDB offre funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/en-us/trustcenter/security) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per MariaDB usa la crittografia del servizio di archiviazione per i dati inattivi ed è conforme allo standard FIPS 140-2. I dati, inclusi i dati di backup, vengono crittografati su disco. (I file temporanei creati dal motore durante l'esecuzione di query non vengono crittografati su disco.) Il servizio usa la crittografia AES a 256 bit, inclusa nella crittografia di Archiviazione di Azure. Le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

Per impostazione predefinita, il servizio Database di Azure per MariaDB è configurato per richiedere la [sicurezza di connessione SSL](./concepts-ssl-connection-security.md) per i dati in transito nella rete. L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Se l'applicazione client non supporta la connettività SSL, è possibile disabilitare facoltativamente il requisito SSL per la connessione al servizio di database.

## <a name="contacts"></a>Contatti

È possibile inviare eventuali domande o suggerimenti sull'uso di Database di Azure per MariaDB, al [team di Database di Azure per MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (non è un alias di supporto tecnico).

È anche possibile usare i punti di contatto seguenti:
- Per contattare il supporto tecnico di Azure, [aprire una richiesta di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nel portale di Azure.
- Per risolvere un problema relativo all'account, [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o per richiedere nuove funzionalità, creare una voce nei [forum di commenti e suggerimenti di Azure](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver letto questa introduzione a Database di Azure per MariaDB, si è pronti per:
- Vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/mariadb/) per confrontare i costi e usare i calcolatori. 
- Iniziare [creando il primo server](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
