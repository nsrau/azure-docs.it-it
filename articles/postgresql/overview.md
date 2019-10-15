---
title: Panoramica del servizio di database relazionale Database di Azure per PostgreSQL
description: Offre una panoramica del servizio di database relazionale Database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177959"
---
# <a name="what-is-azure-database-for-postgresql"></a>Che cos'è Database di Azure per PostgreSQL
Il database di Azure per PostgreSQL è un servizio di database relazionale in Microsoft Cloud creato per gli sviluppatori. È basato sulla versione della community del motore di database [PostgreSQL](https://www.postgresql.org/) open source ed è disponibile in due opzioni di distribuzione: Server singolo e Hyperscale (Citus) (anteprima).

## <a name="azure-database-for-postgresql---single-server"></a>Database di Azure per PostgreSQL - Server singolo
L'opzione di distribuzione Server singolo offre:

- Disponibilità elevata integrata senza costi aggiuntivi (contratto di servizio del 99,99%)
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo
- Ridimensionamento verticale in base alle esigenze in pochi secondi
- Monitoraggio e generazione di avvisi per valutare rapidamente l'impatto del ridimensionamento
- Sicurezza per la protezione dei dati sensibili inattivi e in transito
- Backup automatici e ripristino temporizzato per un massimo di 35 giorni
- Sicurezza e conformità di livello aziendale

Tutte le funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di applicazioni e di accelerare i tempi di mercato, piuttosto che dedicare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È possibile continuare a sviluppare le applicazioni con gli strumenti open source e la piattaforma di propria scelta, senza dover acquisire nuove competenze.

L'opzione di distribuzione Server singolo offre tre piani tariffari: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Ognuno offre diverse funzionalità in termini di risorse per il supporto dei carichi di lavoro dei database. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere  [Piani tariffari](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima)
L'opzione Hyperscale (Citus) scala orizzontalmente le query tra più computer tramite partizionamento orizzontale. Il motore di query parallelizza le query SQL in ingresso in questi server per risposte più rapide in set di dati di grandi dimensioni. Risponde ai requisiti di scalabilità e prestazioni superiori delle applicazioni, in genere carichi di lavoro che si avvicinano a 100 GB di dati, o li superano già.

L'opzione di distribuzione Hyperscale (Citus) offre:

- Scalabilità orizzontale tra più computer tramite partizionamento orizzontale
- Parallelizzazione delle query tra questi server per risposte più rapide in set di dati di grandi dimensioni
- Supporto ottimale per applicazioni multi-tenant, analisi operative in tempo reale e carichi di lavoro transazionali con velocità effettiva elevata

Le applicazioni compilate per PostgreSQL possono eseguire query distribuite in Hyperscale (Citus) con [librerie di connessione](./concepts-connection-libraries.md) standard e modifiche minime.

Tenere presente che Hyperscale (Citus) è in anteprima pubblica e quindi non offre ancora un contratto di servizio.

## <a name="data-security"></a>Sicurezza dei dati
Database di Azure per PostgreSQL rispetta la tradizione di sicurezza dei dati dei servizi di database di Azure. Ha funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://azure.microsoft.com/overview/trusted-cloud/) per informazioni sulla sicurezza della piattaforma Azure.

Il servizio Database di Azure per PostgreSQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata. Per impostazione predefinita, il servizio Database di Azure per PostgreSQL richiede connessioni sicure per i dati in transito nella rete e tra il database e l'applicazione client.

## <a name="contacts"></a>Contatti
Per eventuali domande o suggerimenti sull'uso di Database di Azure per PostgreSQL, inviare un messaggio di posta elettronica al team di Database di Azure per PostgreSQL ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Questo indirizzo è per le domande generali, non per i ticket di supporto.

Inoltre, prendere in considerazione questi punti di contatto in base alle specifiche esigenze:
- Per contattare il supporto di Azure o risolvere un problema relativo all'account, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passaggi successivi
- Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/postgresql/) per confrontare i costi e usare i calcolatori.
- Per iniziare, creare il primo database di Azure per PostgreSQL tramite [Server singolo](./quickstart-create-server-database-portal.md) o [Hyperscale (Citus) (anteprima)](./quickstart-create-hyperscale-portal.md)
- Compilare la prima app in Python, PHP, Ruby, C\#, Java, Node.js: [raccolte di connessioni](./concepts-connection-libraries.md)
