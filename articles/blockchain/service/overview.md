---
title: Panoramica del servizio Azure Blockchain
description: Panoramica del servizio Azure Blockchain
ms.date: 11/21/2019
ms.topic: overview
ms.reviewer: janders
ms.openlocfilehash: 02cc955822987e3be6f5a2184fc49e5510b29626
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455897"
---
# <a name="what-is-azure-blockchain-service"></a>Che cos'è il servizio Azure Blockchain?

Il servizio Azure Blockchain è un servizio di contabilità generale completamente gestito che consente agli utenti di aumentare e gestire le reti blockchain su larga scala in Azure. Grazie al controllo unificato per la gestione dell'infrastruttura e per la governance della rete blockchain, il servizio Azure Blockchain fornisce:

* Semplicità di distribuzione e gestione delle reti
* Gestione predefinita del consorzio
* Sviluppo di contratti intelligenti con strumenti di sviluppo noti

Il servizio Azure Blockchain è progettato per supportare più protocolli di contabilità generale. Attualmente, fornisce il supporto per [Quorum](https://www.jpmorgan.com/Quorum) di Ethereum che usa il meccanismo di consenso [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. È possibile concentrarsi sullo sviluppo di app e sulla logica di business anziché allocare tempo e risorse per la gestione delle macchine virtuali e dell'infrastruttura. È inoltre possibile continuare a sviluppare le applicazioni con gli strumenti open source e la piattaforma di propria scelta, per fornire soluzioni senza dover acquisire nuove competenze.

## <a name="network-deployment-and-operations"></a>Distribuzione e operazioni di rete

Per distribuire il servizio Azure Blockchain, è possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e Visual Studio Code con l'estensione Azure Blockchain. La distribuzione è semplificata e include il provisioning di nodi delle transazioni e di convalida, Reti virtuali di Azure per l'isolamento di sicurezza e l'archiviazione gestita dal servizio.  Quando si distribuisce un nuovo membro della blockchain, gli utenti possono inoltre creare o partecipare a un consorzio.  I consorzi consentono a più parti in sottoscrizioni di Azure diverse di comunicare in modo sicuro tra loro in una blockchain condivisa.  Questa distribuzione semplificata riduce i tempi necessari per la distribuzione della rete blockchain da giorni a minuti.

### <a name="performance-and-service-tiers"></a>Prestazioni e livelli di servizio

Il servizio Azure Blockchain offre due livelli di servizio: *Di base* e *Standard*. Ogni livello offre prestazioni e funzionalità diverse per supportare diversi tipi di attività, da carichi di lavoro di test e sviluppo leggeri fino a distribuzioni delle blockchain di produzione con scalabilità elevata. Entrambi i livelli includono almeno un nodo della transazione e un nodo di convalida (Di base) oppure due nodi di convalida (Standard).

![Piani tariffari](./media/overview/pricing-tiers.png)

Oltre a offrire due nodi di convalida, il livello *Standard* rende disponibili 2 *vCore* per ogni nodo della transazione e di convalida, mentre il livello Basic offre una configurazione vCore.  Con 2 vCore per i nodi della transazione e di convalida, 1 vCore può essere dedicato al libro mastro Quorum mentre il vCore può essere usato per altri servizi correlati all'infrastruttura, garantendo prestazioni ottimali per i carichi di lavoro della blockchain di produzione. Per altre informazioni sui prezzi, vedere [Prezzi di Servizio Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Sicurezza e manutenzione

Dopo il provisioning del primo membro della blockchain, è possibile aggiungere altri nodi della transazione al membro.  Per impostazione predefinita, i nodi delle transazioni sono protetti tramite regole del firewall e devono essere configurati per l'accesso.  Inoltre, tutti i nodi delle transazioni crittografano i dati in movimento tramite TLS.  Sono disponibili più opzioni per la protezione dell'accesso ai nodi delle transazioni, incluse le regole del firewall, l'autenticazione di base, le chiavi di accesso e l'integrazione di Azure Active Directory. Per altre informazioni, vedere pagine relative alla [configurazione dei nodi delle transazioni](configure-transaction-nodes.md) e alla [configurazione dell'accesso Azure Active Directory Access](configure-aad.md).

Come servizio gestito, il servizio Azure Blockchain garantisce che ai nodi dei membri della blockchain vengano applicate patch con gli aggiornamenti alle ultime versioni del sistema operativo host e allo stack del software di contabilità generale, configurati per la disponibilità elevata (solo livello Standard), eliminando gran parte delle istanze DevOps necessarie per i nodi della blockchain IaaS tradizionali.  Per altre informazioni sull'applicazione di patch e sugli aggiornamenti, vedere la pagina relativa alle [versioni del libro mastro del servizio Azure Blockchain supportate](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

Il servizio Azure Blockchain offre anche metriche avanzate tramite il servizio Monitoraggio di Azure, che fornisce dati analitici sull'utilizzo della CPU, della memoria e dell'archiviazione dei nodi.  Monitoraggio di Azure offre anche dati analitici utili sull'attività di rete blockchain, ad esempio su transazioni e blocchi estratti, profondità della coda delle transazioni e connessioni attive.  Le metriche possono essere personalizzate per offrire visualizzazioni delle informazioni dettagliate importanti per l'applicazione blockchain.  È inoltre possibile definire le soglie tramite avvisi che consentono agli utenti di attivare azioni come l'invio di un messaggio di posta elettronica o di un SMS, l'esecuzione di un'app per la logica e Funzione di Azure o l'invio a un webhook con definizione personalizzata.

![Metriche](./media/overview/metrics.png)

Tramite Log Analytics di Azure, gli utenti possono visualizzare i log relativi al libro mastro Quorum o altre informazioni importanti, ad esempio tentativi di connessione ai nodi delle transazioni.

## <a name="built-in-consortium-management"></a>Gestione predefinita del consorzio

Quando si distribuisce il primo membro della blockchain, si entra a far parte di un consorzio o se ne crea uno nuovo.  Un consorzio è un gruppo logico usato per gestire la governance e la connettività tra i membri delle blockchain che eseguono transazioni in un processo a più parti.  Il servizio Azure Bockchain offre controlli di governance integrati tramite contratti intelligenti predefiniti, che determinano quali azioni possono essere eseguite dai membri del consorzio.  Questi controlli di governance possono essere personalizzati in base alle esigenze dell'amministratore del consorzio. Quando si crea un nuovo consorzio, il membro della blockchain rappresenta l'amministratore predefinito del consorzio e ha la possibilità di invitare altre parti a partecipare al consorzio.  È possibile entrare a fare parte di un consorzio solo se si è stati invitati in precedenza.  Quando entra a far parte di un consorzio, il membro della blockchain è soggetto ai controlli di governance eseguiti dall'amministratore del consorzio.

![Gestione del consorzio](./media/overview/consortium.png)

È possibile accedere alle azioni di gestione del consorzio, ad esempio l'aggiunta e la rimozione di membri da un consorzio, tramite PowerShell e un'API REST. È possibile gestire un consorzio a livello programmatico usando interfacce comuni anziché modificare e inviare contratti intelligenti basati su solidità. Per altre informazioni, vedere la pagina relativa alla [gestione del consorzio](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Usare strumenti di sviluppo familiari

In base al libro mastro Quorum di Ethereum, è possibile sviluppare le applicazioni per il servizio Azure Blockchain con procedure analoghe a quelle usate per le applicazioni Ethereum esistenti. Collaborando con i principali partner del settore, l'estensione Azure Blockchain Development Kit di Visual Studio Code consente agli sviluppatori di sfruttare strumenti familiari come la suite Truffle per creare contratti intelligenti. L'estensione Azure Blockchain Development Kit consente agli sviluppatori di creare un consorzio o connettersi a uno esistente, in modo da poter compilare e distribuire i contratti intelligenti da un solo IDE. L'estensione Azure Blockchain Visual Studio Code consente agli sviluppatori di creare un consorzio o connettersi a uno esistente, in modo da poter compilare e distribuire i contratti intelligenti da un solo IDE. Per altre informazioni, vedere [Azure Blockchain Development Kit nel marketplace di VS Code](https://aka.ms/vscodebcextension) e il [Manuale dell'utente di Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Pubblicare i dati della blockchain

Blockchain Data Manager per il servizio Azure Blockchain consente di acquisire, trasformare e distribuire i dati delle transazioni del servizio Azure Blockchain negli argomenti di Griglia di eventi di Azure, offrendo un'integrazione affidabile e scalabile del libro mastro della blockchain con i servizi di Azure. È possibile usare Blockchain Data Manager per integrare applicazioni e archivi dati off-chain. Per altre informazioni, vedere [Blockchain Data Manager per il servizio Azure Blockchain](data-manager.md).

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Serve aiuto o si vuole fornire commenti?

* Visitare il [blog di Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain) e il [forum di Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, provare un avvio rapido o ottenere altri dettagli, vedere le risorse seguenti.
* [Creare un membro della blockchain tramite il portale di Azure](create-member.md) o [creare un membro della blockchain tramite l'interfaccia della riga di comando di Azure](create-member-cli.md)
* Per confrontare i costi e usare i calcolatori, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/blockchain-service).
* Creare la prima app usando [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Manuale dell'utente](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) dell'estensione Azure Blockchain VSCode
