---
title: Panoramica - Database di Azure per MySQL
description: Informazioni sul servizio Database di Azure per MySQL, un servizio di database relazionale in Microsoft Cloud basato su MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 12/02/2019
ms.openlocfilehash: b7b29a07e9d56a9b961192352d0bfa13a8986d7a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775118"
---
# <a name="what-is-azure-database-for-mysql"></a>Database di Azure per MySQL:

Database di Azure per MySQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database [MySQL Community Edition](https://www.mysql.com/products/community/), versioni 5.6, 5.7 e 8.0, disponibile con licenza GPLv2. Database di Azure per MySQL offre:

- Disponibilità elevata integrata senza costi aggiuntivi.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Ridimensionamento in base alle esigenze in pochi secondi.
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
Database di Azure per MySQL offre vari livelli di servizio: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere  [Piani tariffari](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Per decidere quando aumentare e ridurre le prestazioni, si usano le funzionalità predefinite di monitoraggio delle prestazioni e avviso, combinate con le classificazioni delle prestazioni basate sui vCore. Con questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione dei vCore in base alle esigenze attuali o previste in termini di prestazioni. Per informazioni dettagliate, vedere [Avvisi](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio per la disponibilità del 99,99% leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database di Azure per il server MySQL, è possibile sfruttare i vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con Database di Azure per MySQL, è possibile usare un ripristino temporizzato per ripristinare uno stato precedente di un server, addirittura fino a 35 giorni prima.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi di database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per MySQL con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/trustcenter/security) per informazioni sulla sicurezza della piattaforma Azure. Per altre informazioni sulle funzionalità di sicurezza di Database di Azure per MySQL, vedere la [panoramica sulla sicurezza](concepts-security.md).

## <a name="contacts"></a>Contatti
Per eventuali domande o suggerimenti sull'uso di Database di Azure per MySQL, inviare un messaggio di posta elettronica al team di Database di Azure per MySQL ([ @Ask Azure DB per MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Questo indirizzo di posta elettronica non è un alias del supporto tecnico.

Inoltre, prendere in considerazione i seguenti punti di contatto in base alle specifiche esigenze:

- Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione a Database di Azure per MySQL e avere capito di cosa si tratta, è possibile:

- Vedere la pagina relativa ai prezzi per confrontare i costi e usare i calcolatori. [Prezzi](https://azure.microsoft.com/pricing/details/mysql/)
- Per iniziare, creare il primo server. [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- Creare la prima app usando il linguaggio preferito: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
