---
title: Scegliere l'opzione del server PostgreSQL più adatta in Azure
description: Fornisce le linee guida per la scelta dell'opzione del server PostgreSQL più corretta per le distribuzioni.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: f81a44af4a90ccda1875c0a9f2bfaa8dc0b73441
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944645"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Scegliere l'opzione del server PostgreSQL più adatta in Azure

Con Azure, i carichi di lavoro del server PostgreSQL possono essere eseguiti in un'infrastruttura di macchina virtuale come servizio (IaaS) ospitata o come piattaforma come servizio (PaaS) ospitata. PaaS dispone di più opzioni di distribuzione, ognuna con più livelli di servizio. Quando si sceglie tra IaaS e PaaS, è necessario decidere se si vuole gestire il database, applicare patch ed eseguire backup oppure se si vuole delegare queste operazioni ad Azure.

Quando si decide, prendere in considerazione le tre opzioni seguenti in PaaS o in alternativa in esecuzione in macchine virtuali di Azure (IaaS)
- [Database di Azure per server PostgreSQL singolo](./overview-single-server.md)
- [Un server flessibile di Database di Azure per PostgreSQL](./flexible-server/overview.md)
- [Database di Azure per PostgreSQL Hyperscale (Citus)]()

L'opzione **PostgreSQL in macchine virtuali di Azure** rientra nella categoria di settore IaaS. Con questo servizio è possibile eseguire il server PostgreSQL all'interno di una macchina virtuale completamente gestita nella piattaforma cloud di Azure. Tutte le versioni e le edizioni recenti di PostgreSQL possono essere installate in una macchina virtuale IaaS. La differenza più significativa rispetto al database di Azure per PostgreSQL è che PostgreSQL in macchine virtuali di Azure offre il controllo sul motore di database. Tuttavia, questo controllo è responsabile della gestione delle macchine virtuali e di molte attività di amministrazione di database (DBA). Queste attività includono la manutenzione e l'applicazione di patch ai server di database, il recupero del database e la progettazione a disponibilità elevata.

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

| **Attributo** | **Postgres nelle VM di Azure** | **PostgreSQL come PaaS** |
| ----- | ----- | ----- |
| <B> Disponibilità SLA |- 99,99% con set di disponibilità <br> - 99,95% con macchine virtuali singole | - Server singolo: 99,99% <br> - Server flessibile: non disponibile durante l'anteprima <br> - Hyperscale (Citus): 99,95% (quando è abilitata la disponibilità elevata)|
| <B> Applicazione di patch al sistema operativo e a PostgreSQL | - Gestita dal cliente | - Server singolo: automatica <br> - Server flessibile-automatica con finestra facoltativa gestita dal cliente <br> - Hyperscale (Citus): automatica |
| <B> Disponibilità elevata | - I clienti progettano, implementano, testano e gestiscono la disponibilità elevata. Le funzionalità possono includere clustering, replica e così via. | - Server singolo: predefinita <br> - Server flessibile: predefinita <br> - Hyperscale (Citus): compilata con standby |
| <B> Ridondanza della zona | - Le macchine virtuali di Azure possono essere configurate per l'esecuzione in diverse zone di disponibilità. Per una soluzione locale, i clienti devono creare, gestire e mantenere i propri data center secondari. | - Server singolo: No <br> - Server flessibile: Sì <br> - Hyperscale (Citus): No |
| <B> Scenario ibrido | - Gestita dal cliente |- Server singolo: replica in lettura <br> - Server flessibile: non disponibile durante l'anteprima <br> - Hyperscale (Citus): No |
| <B> Backup e ripristino | - Gestito dal cliente | - Server singolo: integrato con la configurazione utente per locale e geo <br> - Server flessibile: integrato con configurazione utente nell'archiviazione con ridondanza della zona <br> - Hyperscale (Citus): predefinito |
| <B> Monitoraggio delle operazioni del database | - Gestito dal cliente | - Server singolo, server flessibile e Hyperscale (Citus): offrono tutti ai clienti la possibilità di impostare avvisi sull'operazione del database e agire al raggiungimento delle soglie. |
| <B>Protezione avanzata dalle minacce | - I clienti devono compilare questa protezione per se stessi. |- Server singolo: Sì <br> - Server flessibile: non disponibile durante l'anteprima <br> - Hyperscale (Citus): No |
| <B>Ripristino di emergenza | - Gestito dal cliente | - Server singolo: backup con ridondanza geografica e repliche in lettura geografica <br> - Server flessibile: non disponibile durante l'anteprima <br> - Hyperscale (Citus): No |
| <B> Prestazioni intelligenti | - Gestito dal cliente | - Server singolo: Sì <br> - Server flessibile: non disponibile durante l'anteprima <br> - Hyperscale (Citus): No |

## <a name="total-cost-of-ownership-tco"></a>Costo totale di proprietà

Il costo totale di proprietà è spesso la considerazione principale che determina la migliore soluzione per l'hosting dei database. Ciò vale sia per le startup con scarse disponibilità economiche, sia per team di una società consolidata che operano con vincoli di budget limitati. Questa sezione descrive le nozioni di base relative a fatturazione e licenze in Azure che si applicano a database di Azure per PostgreSQL e PostgreSQL in macchine virtuali di Azure.

## <a name="billing"></a>Fatturazione

Database di Azure per PostgreSQL è attualmente disponibile come servizio in diversi livelli con prezzi diversi per le risorse. Tutte le risorse vengono fatturate su base oraria a una tariffa fissa. Per le informazioni più recenti sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione attualmente supportati, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/server/) È possibile modificare dinamicamente i livelli di servizio e le dimensioni di calcolo in base alle esigenze di velocità effettiva variate dell'applicazione. Viene fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/) normale.

Con database di Azure per PostgreSQL, Microsoft automaticamente configura, esegue patch e aggiorna il software del database. Queste azioni automatiche riducono i costi amministrativi. Inoltre, database di Azure per PostgreSQL dispone di [funzionalità automatiche di collegamento di backup](). Le funzionalità di backup predefinite consentono di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database. Al contrario, con PostgreSQL in macchine virtuali di Azure è possibile scegliere ed eseguire qualsiasi versione di PostgreSQL. Tuttavia, è necessario pagare per la macchina virtuale di cui è stato effettuato il provisioning, i costi di archiviazione associati ai dati, il backup, il monitoraggio dei dati e l'archiviazione dei log e i costi per il tipo di licenza PostgreSQL usato (se presente).

Database di Azure per PostgreSQL offre disponibilità elevata incorporata per qualsiasi tipo di interruzione a livello di nodo mantenendo al tempo stesso la garanzia SLA del 99,99% per il servizio. Tuttavia, per la disponibilità elevata del database all'interno delle VM, è possibile usare le opzioni di disponibilità elevata, ad esempio [replica di streaming](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) disponibili in un database PostgreSQL. L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99% a costi aggiuntivi e carichi amministrativi.

Per altre informazioni sui prezzi, vedere gli articoli seguenti:
- [Piani tariffari di Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Prezzi per le macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione, sia i costi.

Con IaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Fornisce l'applicazione automatica delle patch per l'hardware e il sistema operativo sottostanti

Con PaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Fornisce l'applicazione automatica delle patch per l'hardware e il sistema operativo e il motore di database sottostanti.
- Gestisce la disponibilità elevata del database.
- Esegue automaticamente i backup e replica tutti i dati per fornire il ripristino di emergenza.
- Crittografa i dati inattivi e in movimento per impostazione predefinita.
- Monitora il server e fornisce funzionalità per informazioni dettagliate sulle prestazioni delle query e consigli sulle prestazioni.

Con database di Azure per PostgreSQL è possibile continuare a amministrare il proprio database. Tuttavia, non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Esempi di elementi che è possibile continuare ad amministrare includono:

- Database
- Accesso
- Ottimizzazione indici
- Ottimizzazione delle query
- Controllo
- Sicurezza

Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione da minime a nulle.

- Con PostgreSQL in macchine virtuali di Azure si ha il pieno controllo del sistema operativo e della configurazione dell'istanza del server PostgreSQL. Con una macchina virtuale, si decide quando eseguire l'aggiornamento o l'upgrade del sistema operativo e del software del database e quali patch applicare. Si decide inoltre quando installare software aggiuntivi, ad esempio un'applicazione antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. È possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Per altre informazioni, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Tempo per lo spostamento al servizio PostgreSQL di Azure (PaaS)

- Database di Azure per PostgreSQL è la soluzione ideale per le applicazioni progettate per il cloud quando i fattori critici sono la produttività degli sviluppatori e un time-to-market rapido per le nuove soluzioni. Con la funzionalità programmatica che è come DBA, il servizio è adatto agli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti.

- Quando si vuole risparmiare tempo e costi di acquisizione per un nuovo hardware locale, PostgreSQL in macchine virtuali di Azure è la soluzione ideale per le applicazioni che richiedono un controllo granulare e la personalizzazione del motore PostgreSQL non supportato dal servizio o che richiedono l'accesso al sistema operativo sottostante.

## <a name="next-steps"></a>Passaggi successivi

- Vedere i [piani tariffari di Database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Per iniziare, creare il primo server.

