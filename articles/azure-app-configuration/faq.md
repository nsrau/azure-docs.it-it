---
title: Domande frequenti sulla configurazione di app Azure | Microsoft Docs
description: Domande frequenti sulla configurazione di app Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904103"
---
# <a name="azure-app-configuration-faq"></a>Domande frequenti sulla configurazione di app Azure

Questo articolo descrive le domande frequenti sulla configurazione app Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Qual è la differenza tra Configurazione app e Azure Key Vault?

La configurazione dell'app è progettata per un set di casi di utilizzo distinti: consente agli sviluppatori di gestire le impostazioni dell'applicazione e controllare la disponibilità delle funzionalità. Mira a semplificare molte delle attività di utilizzo di dati di configurazione complessi.

La configurazione dell'app supporta:

- Spazi dei nomi gerarchici
- etichette
- Query estese
- Recupero in batch
- Operazioni di gestione specializzate
- Interfaccia utente di gestione delle funzionalità

La configurazione dell'app è complementare a Key Vault e le due devono essere usate side-by-side nella maggior parte delle distribuzioni dell'applicazione.

## <a name="should-i-store-secrets-in-app-configuration"></a>È consigliabile archiviare I segreti nella configurazione dell'app?

Sebbene la configurazione dell'app fornisca sicurezza avanzata, Key Vault rappresenta comunque il posto migliore per l'archiviazione dei segreti delle applicazioni. Key Vault fornisce la crittografia a livello di hardware, i criteri di accesso granulari e le operazioni di gestione, ad esempio la rotazione del certificato.

È possibile creare valori di configurazione dell'app che fanno riferimento a segreti archiviati in Key Vault. Per altre informazioni, vedere [usare Key Vault riferimenti in un'app ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>La configurazione dell'app crittografa i dati?

Sì. La configurazione dell'app crittografa tutti i valori di chiave che possiede e crittografa le comunicazioni di rete. I nomi delle chiavi vengono usati come indici per il recupero dei dati di configurazione e non vengono crittografati.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>In che modo la configurazione delle app è diversa dalle impostazioni del servizio app Azure?

App Azure servizio consente di definire le impostazioni dell'app per ogni istanza del servizio app. Queste impostazioni vengono passate come variabili di ambiente al codice dell'applicazione. Se lo si desidera, è possibile associare un'impostazione a uno slot di distribuzione specifico. Per altre informazioni, vedere [configurare le impostazioni dell'app](/azure/app-service/configure-common#configure-app-settings).

Al contrario, la configurazione di app Azure consente di definire le impostazioni che possono essere condivise tra più app, incluse le app in esecuzione nel servizio app. È possibile accedere a queste impostazioni nel codice dell'applicazione tramite i provider di configurazione per .NET e Java, tramite Azure SDK o direttamente tramite le API REST.

È anche possibile importare ed esportare le impostazioni tra il servizio app e la configurazione dell'app. Questo consente di configurare rapidamente un nuovo archivio di configurazione dell'app in base alle impostazioni del servizio app esistenti o di condividere facilmente la configurazione con un'app esistente che si basa sulle impostazioni del servizio app.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Esistono limitazioni alle dimensioni delle chiavi e dei valori archiviati nella configurazione dell'app?

È previsto un limite di 10KB per un singolo elemento chiave-valore.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Come è possibile archiviare le configurazioni per più ambienti (test, gestione temporanea, produzione e così via)?

Attualmente è possibile controllare chi ha accesso alla configurazione dell'app a un livello per archivio. Usare un archivio separato per ogni ambiente che richiede autorizzazioni diverse. Questo approccio offre l'isolamento di sicurezza migliore.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quali sono le modalità consigliate per usare la configurazione delle app?

Vedere [procedure consigliate](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto costa la configurazione dell'app?

Il servizio può essere usato gratuitamente durante l'anteprima pubblica.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Come è possibile ricevere annunci sulle nuove versioni e altre informazioni relative alla configurazione dell'app?

Sottoscrivere il [repository di annunci GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Come è possibile segnalare un problema o inviare un suggerimento?

Puoi contattarci direttamente su [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla configurazione di app Azure](./overview.md)
