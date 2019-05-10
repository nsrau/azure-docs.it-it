---
title: Panoramica di Azure Blockchain Service
description: Panoramica del servizio Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: a200649493354f1264afb0df4cf74acb4a274017
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406414"
---
# <a name="what-is-azure-blockchain-service"></a>Che cos'è servizio di Azure Blockchain?

Azure Blockchain Service è un servizio ledger completamente gestito che consente agli utenti la possibilità di aumento delle dimensioni e gestire le reti di blockchain su larga scala in Azure. Fornendo un controllo unificato per la gestione dell'infrastruttura nonché a blockchain governance di rete, servizio Blockchain di Azure offre:

* Operazioni e la distribuzione di rete semplice
* Gestione consortium predefiniti
* Sviluppa contratti intelligenti con gli strumenti di sviluppo familiari

Azure Blockchain Service è progettato per supportare più protocolli di contabilità. Attualmente, fornisce il supporto per il Ethereum [Quorum](https://www.jpmorgan.com/Quorum) ledger utilizzando il [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) meccanismo del consenso.

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. È possibile concentrarsi sullo sviluppo di app e la logica di business piuttosto che allocare tempo e risorse per gestire le macchine virtuali e l'infrastruttura. Inoltre, è possibile continuare a sviluppare l'applicazione con la piattaforma di propria scelta di distribuzione delle soluzioni senza dover apprendere nuove competenze e strumenti open source.

## <a name="network-deployment-and-operations"></a>Distribuzione di rete e operazioni

Distribuzione del servizio di Azure Blockchain può essere eseguita tramite il portale di Azure CLI di Azure, oltre che tramite Visual Studio code usando l'estensione Azure Blockchain.  Distribuzione è semplificata, inclusi il provisioning di nodi sia delle transazioni e convalida, le reti virtuali di Azure per l'isolamento di sicurezza, nonché gestite dal servizio di archiviazione.  Inoltre, quando si distribuisce un nuovo membro di blockchain, gli utenti anche creare o partecipare, un consorzio.  Consorzi abilitare più entità in diverse sottoscrizioni di Azure sia in grado di comunicare in modo sicuro tra loro in una blockchain condivisa.  Questa distribuzione semplificata riduce la distribuzione di rete di blockchain da giorni a minuti.

### <a name="performance-and-service-tiers"></a>Livelli di prestazioni e del servizio

Il servizio di Blockchain di Azure offre due livelli di servizio: *Base* e *Standard*. Ogni livello offre diverse prestazioni e funzionalità per supportare lo sviluppo leggero e testa carichi di lavoro fino a elevata scalabilità le distribuzioni di produzione blockchain. Entrambi i livelli includono nodo almeno una transazione e un nodo di convalida (Basic) o due nodi validator (Standard).

![Piani tariffari](./media/overview/pricing-tiers.png)

Oltre a offrire due nodi di convalida, il *Standard* livello offre 2 *Vcore* per ogni nodo di validator e delle transazioni mentre il livello Basic offre una configurazione di vCore 1.  Offrendo 2 Vcore per i nodi delle transazioni e convalida, 1 vCore può essere dedicato per la contabilità Quorum mentre il restante 1 vCore può essere utilizzato per altri servizi correlati all'infrastruttura, garantendo prestazioni ottimali per la produzione dei carichi di lavoro di blockchain. Per altre informazioni su dettagli sui prezzi, vedere [prezzi del servizio di Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Sicurezza e alla manutenzione

Dopo il provisioning del primo membro di blockchain, hai la possibilità di aggiungere i nodi delle transazioni aggiuntivi al membro.  Per impostazione predefinita, i nodi delle transazioni vengono protetti mediante le regole del firewall e dovranno essere configurato per l'accesso.  Inoltre, tutti i nodi di transazione crittografare i dati in transito tramite TLS.  Siano disponibili più opzioni per la protezione dell'accesso nodo delle transazioni, tra cui le regole del firewall, l'autenticazione di base, le chiavi di accesso, nonché integrazione con Azure Active Directory. Per altre informazioni, vedere [configurare i nodi delle transazioni](configure-transaction-nodes.md) e [configurare l'accesso di Azure Active Directory](configure-aad.md).

Come servizio gestito, Azure Blockchain servizio assicura che i nodi del membro le blockchain sono applicate patch con l'host più recente operativi gli aggiornamenti dello stack di software di sistema e contabilità, configurato per la disponibilità elevata (solo per il livello Standard), eliminando gran parte di DevOps obbligatorio per i nodi di blockchain tradizionali IaaS.  Per altre informazioni su patch e aggiornamenti, vedere [servizio di Azure Blockchain ledger versioni supportate](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

Inoltre, Azure Blockchain servizio fornisce metriche avanzate tramite il servizio di monitoraggio di Azure che fornisce informazioni dettagliate sui nodi di CPU, memoria e l'utilizzo di archiviazione, nonché informazioni dettagliate utili sulle attività di rete blockchain, ad esempio le transazioni e blocchi sottoposti a data mining, profondità della coda di transazione, nonché le connessioni attive.  Le metriche possono essere personalizzate per fornire visualizzazioni sulle informazioni che sono importanti per l'applicazione di blockchain.  Inoltre, le soglie possono essere definite tramite avvisi consentendo agli utenti di attivare azioni, ad esempio l'invio di un messaggio di posta elettronica o SMS, App per la logica, funzioni di Azure in esecuzione o l'invio a un webhook personalizzati.

![Metriche](./media/overview/metrics.png)

Tramite Azure Log Analitica, gli utenti possono visualizzare i log relativi a della contabilità Quorum o di altre connessioni informazioni importanti, ad esempio provate i nodi delle transazioni.

## <a name="built-in-consortium-management"></a>Gestione consortium predefiniti

Quando si distribuisce il primo membro di blockchain, si join o creare un nuovo consortium.  Un consorzio è un gruppo logico usato per gestire la governance e la connettività tra i membri di blockchain che transact in un processo a più parte.  Azure Blockchain Service fornisce controlli di governance predefinita attraverso smart Contract predefiniti, che determinano quali membri di azioni al consorzio possono richiedere.  Questi controlli di governance possono essere personalizzati in base alle esigenze dall'amministratore del consortium. Quando si crea un nuovo consortium, il membro blockchain è l'amministratore predefinito del consorzio, consentendo la facoltà di invitare altre parti per aggiungere il consortium.  È possibile aggiungere un consorzio solo se si è stati invitati in precedenza.  Quando si aggiunge un consorzio, il membro di blockchain è soggette a mettere in atto dall'amministratore del consorzio i controlli di governance.

![Gestione Consortium](./media/overview/consortium.png)

Le operazioni di gestione Consortium quali l'aggiunta e rimozione di membri da un consorzio accessibili tramite un'API REST e PowerShell. È possibile gestire a livello di codice un consorzio usando interfacce comuni anziché modifica e l'invio basato su solidity smart Contract. Per altre informazioni, vedere [gestione consortium](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Sviluppare usando strumenti di sviluppo familiari

A seconda della contabilità Quorum Ethereum open source, è possibile sviluppare applicazioni per il servizio di Azure Blockchain esattamente come avviene per le applicazioni esistenti Ethereum. Utilizzo di partner leader nel settore, l'estensione Azure Blockchain Development Kit Visual Studio Code consente agli sviluppatori di sfruttare strumenti familiari come la Suite Truffle creare smart Contract. Usa l'estensione Azure Blockchain Development Kit, gli sviluppatori possono creare o connettersi a e consortium esistente in modo che è possibile compilare e distribuire la smart contratti tutti usando un solo IDE. Uso dell'estensione di Visual Studio Code di Azure Blockchain, è possibile creare o connettersi a un consorzio esistente in modo che è possibile compilare e distribuire i tuoi contratti intelligenti tutto da un IDE. Per altre informazioni, vedere [Kit di sviluppo di Blockchain di Azure nel marketplace di Visual Studio Code](https://aka.ms/vscodebcextension) e il [manuale dell'utente Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Guida in linea o dispone di commenti e suggerimenti?

* Visitare il [blog di Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), e [forum Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, provare una Guida introduttiva o scoprire altri dettagli, vedere queste risorse.
* [Creare un membro di blockchain usando il portale di Azure](create-member.md) o [creare un membro di blockchain con Azure CLI]()
* Per confrontare i costi e calcoli, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/blockchain-service).
* Compilare la prima app usando il [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* Estensione di Visual Studio code di Azure Blockchain [manuale dell'utente](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
