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
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393339"
---
# <a name="azure-app-configuration-faq"></a>Domande frequenti su configurazione di App di Azure

Questo articolo risponde alle domande frequenti sulla configurazione delle App di Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Qual è la differenza tra Configurazione app e Azure Key Vault?

Configurazione delle App è progettata per un set distinto di casi d'uso: consente agli sviluppatori di gestire le impostazioni dell'applicazione e controllare la disponibilità delle funzionalità. L'obiettivo è semplificare molte delle attività di utilizzo dei dati di configurazione complesse.

Supporta la configurazione dell'App:

- Spazi dei nomi gerarchico
- L'assegnazione di etichette
- L'ambito delle query
- Recupero in batch
- Operazioni di gestione specializzato
- Un'interfaccia utente di gestione delle funzionalità

Configurazione delle App è complementare a Key Vault e usare i due side-by-side la maggior parte delle distribuzioni di applicazioni.

## <a name="should-i-store-secrets-in-app-configuration"></a>È consigliabile archiviare i segreti nella configurazione delle App?

Anche se la configurazione dell'App offre una protezione con protezione avanzata, Key Vault è ancora il modo migliore per l'archiviazione dei segreti dell'applicazione. Key Vault offre la crittografia a livello hardware, i criteri di accesso granulare e operazioni di gestione, ad esempio la rotazione di certificati.

## <a name="does-app-configuration-encrypt-my-data"></a>Configurazione dell'App esegue la crittografia dei dati?

Sì. Configurazione delle App Crittografa tutti i valori delle chiavi, contiene e permette di crittografare le comunicazioni di rete. I nomi delle chiavi vengono usati come gli indici per il recupero dei dati di configurazione e non sono crittografati.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Modo in cui è necessario archiviare le configurazioni per più ambienti (test, staging, produzione e così via)?

Attualmente è possibile controllare chi ha accesso alla configurazione delle App a un livello per ogni negozio. Usare un archivio separato per ogni ambiente che richiede autorizzazioni diverse. Questo approccio offre la migliore isolamento di sicurezza.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quali sono i metodi consigliati per usare la configurazione dell'App?

Visualizzare [procedure consigliate](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto costa la configurazione dell'App?

Il servizio è gratuito durante l'anteprima pubblica.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Come posso segnalare un problema o fornire un suggerimento?

È possibile contattare Microsoft direttamente sulla [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passaggi successivi

* [Sulla configurazione delle App di Azure](./overview.md)
