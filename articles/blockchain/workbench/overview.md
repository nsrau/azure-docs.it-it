---
title: Panoramica di Azure Blockchain Workbench
description: Panoramica e funzionalità di Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ba102e161ae627c621b1d1f33bf3321eedc4f54d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239705"
---
# <a name="what-is-azure-blockchain-workbench"></a>Cos'è Azure Blockchain Workbench?

Azure Blockchain Workbench è una raccolta di servizi e funzionalità di Azure che consentono di creare e distribuire applicazioni blockchain per condividere processi di business e dati con altre organizzazioni. Azure Blockchain Workbench fornisce lo scaffolding di infrastruttura per la compilazione di applicazioni blockchain, consentendo agli sviluppatori di concentrarsi sulla creazione della logica di business e dei contratti intelligenti. Semplifica anche la creazione di applicazioni blockchain grazie all'integrazione di vari servizi e funzionalità di Azure che consentono di automatizzare attività di sviluppo comuni.

## <a name="create-blockchain-applications"></a>Creare un'applicazione blockchain

Con Blockchain Workbench è possibile definire applicazioni blockchain mediante la configurazione e scrivere codice per contratti intelligenti. È possibile iniziare subito a sviluppare applicazioni blockchain e concentrarsi sulla definizione del contratto e la scrittura della logica di business, invece di compilare lo scaffolding e configurare i servizi di supporto.

## <a name="manage-applications-and-users"></a>Gestire applicazioni e utenti

Azure Blockchain Workbench fornisce un'applicazione Web e API REST per gestire le applicazioni blockchain e gli utenti. Gli amministratori di Blockchain Workbench possono gestire l'accesso alle applicazioni e assegnare gli utenti ai ruoli dell'applicazione. Viene eseguito automaticamente il mapping fra utenti di Azure Active Directory e membri nell'applicazione.

## <a name="integrate-blockchain-with-applications"></a>Integrare blockchain con le applicazioni

È possibile usare le API REST di Blockchain Workbench e le API basate su messaggi per l'integrazione con sistemi esistenti. Le API forniscono un'interfaccia che consente di sostituire o usare più tecnologie contabili, risorse di archiviazione e offerte di database distribuite.

Blockchain Workbench può trasformare i messaggi inviati alla relativa API basata su messaggi per compilare le transazioni in un formato previsto dall'API nativa di quel blockchain.  Workbench può firmare e indirizzare le transazioni al blockchain appropriato. 

Workbench recapita automaticamente gli eventi al bus di servizio e alla Griglia di eventi per inviare messaggi ai consumer a valle. Gli sviluppatori possono integrare uno di questi sistemi di messaggistica per guidare le transazioni e per esaminare i risultati.

## <a name="deploy-a-blockchain-network"></a>Distribuire una rete blockchain

Azure Blockchain Workbench semplifica la configurazione della rete consortium blockchain come soluzione preconfigurata con un modello di soluzione di Azure Resource Manager. Il modello fornisce una distribuzione semplificata che consente di distribuire tutti i componenti necessari per eseguire un consortium. Attualmente Blockchain Workbench supporta Ethereum.

## <a name="use-active-directory-login"></a>Usare l'accesso Active Directory

Con i protocolli blockchain esistenti, le identità blockchain sono rappresentate come un indirizzo sulla rete. Azure Blockchain Workbench estrae l'identità di blockchain associandola a un'identità di Active Directory e in questo modo semplifica la compilazione delle applicazioni aziendali con identità di Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizzare i dati on-chain con l'archivio off-chain

Azure Blockchain Workbench semplifica l'analisi dei dati e degli eventi di blockchain eseguendo automaticamente la sincronizzazione dei dati nel blockchain con l'archivio off-chain. Anziché estrarre i dati direttamente dal blockchain, è possibile eseguire una query su sistemi di database off-chain, ad esempio SQL Server. Non è necessaria esperienza specifica con blockchain per gli utenti finali che eseguono attività di analisi dei dati. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Architettura di Azure Blockchain Workbench](architecture.md)
