---
title: Configurazione dell'App Azure domande frequenti | Microsoft Docs
description: Domande frequenti sulla configurazione delle App di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411897"
---
# <a name="azure-app-configuration-faq"></a>Domande frequenti su configurazione di App di Azure

Questo articolo tratta alcune domande frequenti sulla configurazione delle App di Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Come è diverso dall'insieme di credenziali chiave di configurazione delle App?

Configurazione delle App è progettata per un set distinto di casi d'uso: consente agli sviluppatori di gestire le impostazioni dell'applicazione e controllare la disponibilità delle funzionalità. L'obiettivo è semplificare molte delle attività di utilizzo dei dati di configurazione complesse. Supporta lo spazio dei nomi gerarchico, l'assegnazione di etichette, l'ambito delle query, recupero in batch e le operazioni di gestione specializzato e le interfacce utente. Configurazione delle App è complementare all'insieme di credenziali chiave e i due devono essere utilizzati side-by-side la maggior parte delle distribuzioni di applicazioni.

## <a name="should-i-store-secrets-in-app-configuration"></a>È consigliabile archiviare i segreti nella configurazione delle App?

Durante la configurazione dell'App offre una protezione con protezione avanzata, Key Vault è ancora il modo migliore per l'archiviazione dei segreti dell'applicazione. Fornisce la crittografia a livello hardware, i criteri di accesso granulare e operazioni di gestione, ad esempio la rotazione di certificati.

## <a name="does-app-configuration-encrypt-my-data"></a>Configurazione dell'App esegue la crittografia dei dati?

Sì. Configurazione delle App consente di crittografare tutti i valori di chiave mantiene e comunicazione di rete. I nomi delle chiavi vengono usate come indici per il recupero dei dati di configurazione e non vengono crittografati.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Configurazione delle App supporta la rete virtuale di Azure (VNET)?

Per il momento no. Supporto di rete virtuale è pianificato per la disponibilità generale.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Modo in cui è necessario archiviare le configurazioni per più ambienti (test, staging, produzione e così via)?

Attualmente è possibile controllare chi ha accesso alla configurazione delle App a un livello per ogni negozio. È consigliabile usare un archivio separato per ogni ambiente che richiede autorizzazioni diverse. Questo approccio offre la migliore isolamento di sicurezza.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quali sono i metodi consigliati per usare la configurazione dell'App?

Visualizzare [procedure consigliate](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto costa la configurazione dell'App?

Il servizio è gratuito durante l'anteprima pubblica.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Come posso segnalare un problema o fornire un suggerimento?

È possibile contattare Microsoft direttamente sulla [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passaggi successivi

* [Sulla configurazione delle App di Azure](./overview.md)
