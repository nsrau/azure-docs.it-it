---
title: Domande frequenti sulla configurazione delle app di AzureAzure App Configuration FAQ
description: Domande frequenti sulla configurazione delle app di Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348778"
---
# <a name="azure-app-configuration-faq"></a>Domande frequenti sulla configurazione delle app di AzureAzure App Configuration FAQ

Questo articolo risponde alle domande frequenti sulla configurazione delle app di Azure.This article answers frequently asked questions about Azure App Configuration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>In che modo la configurazione delle app è diversa dall'insieme di credenziali delle chiavi di Azure?

Configurazione app consente agli sviluppatori di gestire le impostazioni dell'applicazione e controllare la disponibilità delle funzionalità. Ha lo scopo di semplificare molte delle attività di utilizzo di dati di configurazione complessi.

La configurazione dell'app supporta:

- Spazi dei nomi gerarchici
- Etichettatura
- Query estese
- Recupero in batch
- Operazioni di gestione specializzate
- Un'interfaccia utente di gestione delle funzionalità

Configurazione app integra Key Vault e i due devono essere utilizzati fianco a fianco nella maggior parte delle distribuzioni di applicazioni.

## <a name="should-i-store-secrets-in-app-configuration"></a>È necessario archiviare segreti in Configurazione app?

Sebbene la configurazione delle app fornisca una protezione con protezione continua, l'insieme di credenziali delle chiavi è ancora il posto migliore per archiviare i segreti dell'applicazione. L'insieme di credenziali delle chiavi fornisce la crittografia a livello hardware, criteri di accesso granulare e operazioni di gestione, ad esempio la rotazione dei certificati.

È possibile creare valori di configurazione dell'app che fanno riferimento a segreti archiviati in Key Vault.You can create App Configuration values that reference secrets stored in Key Vault. Per ulteriori informazioni, consultate Usare i riferimenti dell'insieme di credenziali delle [chiavi in un'app ASP.NET Core.](./use-key-vault-references-dotnet-core.md)

## <a name="does-app-configuration-encrypt-my-data"></a>App Configuration crittografa i miei dati?

Sì. Configurazione app crittografa tutti i valori chiave che contiene e la comunicazione di rete. I nomi e le etichette delle chiavi vengono utilizzati come indici per il recupero dei dati di configurazione e non sono crittografati.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>In che modo la configurazione delle app è diversa dalle impostazioni del servizio app di Azure?

Il servizio app di Azure consente di definire le impostazioni dell'app per ogni istanza del servizio app. Queste impostazioni vengono passate come variabili di ambiente al codice dell'applicazione. Se lo si desidera, è possibile associare un'impostazione a uno slot di distribuzione specifico. Per ulteriori informazioni, consultate [Configurare le impostazioni dell'app.](/azure/app-service/configure-common#configure-app-settings)

Al contrario, Configurazione app di Azure consente di definire impostazioni che possono essere condivise tra più app. Sono incluse le app in esecuzione nel servizio app e altre piattaforme. Il codice dell'applicazione accede a queste impostazioni tramite i provider di configurazione per .NET e Java, tramite Azure SDK o direttamente tramite API REST.

È inoltre possibile importare ed esportare le impostazioni tra il servizio app e la configurazione dell'app. Questa funzionalità consente di configurare rapidamente un nuovo archivio di configurazione app in base alle impostazioni del servizio app esistenti. È anche possibile condividere la configurazione con un'app esistente che si basa sulle impostazioni del servizio app.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Esistono limitazioni di dimensioni relative a chiavi e valori archiviati in Configurazione app?

Esiste un limite di 10 KB per un singolo elemento chiave-valore.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Come è necessario archiviare le configurazioni per più ambienti (test, gestione temporanea, produzione e così via)?

È possibile controllare chi può accedere a Configurazione app a livello di archivio. Usare un archivio separato per ogni ambiente che richiede autorizzazioni diverse. Questo approccio offre il miglior isolamento di sicurezza.

Se non è necessario l'isolamento della sicurezza tra gli ambienti, è possibile utilizzare le etichette per distinguere i valori di configurazione. [Utilizzare le etichette per abilitare configurazioni diverse per ambienti diversi](./howto-labels-aspnet-core.md) fornisce un esempio completo.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quali sono i modi consigliati per usare La configurazione delle app?

Vedere [le procedure consigliate](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto costa la configurazione delle app?

Sono disponibili due piani tariffari:There are two pricing tiers:

- Livello gratuito
- Livello standard.

Se è stato creato un archivio prima dell'introduzione del livello Standard, viene automaticamente spostato nel livello Gratuito in base alla disponibilità generale. È possibile scegliere di eseguire l'aggiornamento al livello Standard o di rimanere nel livello Gratuito.You can choose to the Standard tier or remain on the Free tier.

Non è possibile eseguire il downgrade di un negozio dal livello Standard al livello Gratuito.You can't downgrade a store from the Standard tier to the Free tier. È possibile creare un nuovo archivio nel livello Gratuito e quindi importare i dati di configurazione in tale archivio.

## <a name="which-app-configuration-tier-should-i-use"></a>Quale livello di configurazione app devo usare?

Entrambi i livelli di configurazione delle app offrono funzionalità di base, tra cui impostazioni di configurazione, flag di funzionalità, riferimenti dell'insieme di credenziali delle chiavi, operazioni di gestione di base, metriche e log.

Di seguito sono riportate alcune considerazioni per la scelta di un livello.

- **Risorse per sottoscrizione:** una risorsa è costituita da un singolo archivio di configurazione. Ogni sottoscrizione è limitata a un archivio di configurazione nel livello gratuito. Le sottoscrizioni possono avere un numero illimitato di archivi di configurazione nel livello standard.
- **Archiviazione per risorsa:** nel livello gratuito ogni archivio di configurazione è limitato a 10 MB di spazio di archiviazione. Nel livello standard, ogni archivio di configurazione può usare fino a 1 GB di spazio di archiviazione.
- **Cronologia delle**chiavi : App Configuration memorizza una cronologia di tutte le modifiche apportate alle chiavi. Nel livello gratuito, questa cronologia viene archiviata per sette giorni. Nel livello standard, questa cronologia viene archiviata per 30 giorni.
- **Richieste al giorno**: Gli archivi di livello gratuito sono limitati a 1.000 richieste al giorno. Quando un archivio raggiunge 1.000 richieste, restituirà il codice di stato HTTP 429 per tutte le richieste fino alla mezzanotte UTC.

    Per gli archivi di livello standard, le prime 200.000 richieste al giorno sono incluse nella tariffa giornaliera. Le richieste aggiuntive vengono fatturate come overage.

- **Contratto**di servizio: il livello standard ha un contratto di servizio pari al 99,9%. Il livello gratuito non dispone di un servizio di archiviazione.
- Funzionalità di **sicurezza:** entrambi i livelli includono funzionalità di sicurezza di base, tra cui la crittografia con chiavi gestite da Microsoft, l'autenticazione tramite HMAC o Azure Active Directory, il supporto RBAC e l'identità gestita. Il livello Standard offre funzionalità di sicurezza più avanzate, tra cui il supporto Private Link e la crittografia con chiavi gestite dal cliente.
- **Costo:** i negozi di livello standard hanno un costo di utilizzo giornaliero. C'è anche un costo di overage per le richieste oltre l'allocazione giornaliera. L'uso di un archivio di livello gratuito non è previsto alcun costo.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>È possibile aggiornare un negozio dal livello Gratuito al livello Standard? È possibile effettuare il downgrade di un negozio dal livello Standard al livello Gratuito?

È possibile eseguire l'aggiornamento dal livello Gratuito al livello Standard in qualsiasi momento.

Non è possibile eseguire il downgrade di un negozio dal livello Standard al livello Gratuito.You can't downgrade a store from the Standard tier to the Free tier. È possibile creare un nuovo archivio nel livello Gratuito e quindi importare i [dati di configurazione in tale archivio.](howto-import-export-data.md)

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Esistono limiti al numero di richieste effettuate a App Configuration?

Gli archivi di configurazione nel livello Gratuito sono limitati a 1.000 richieste al giorno. Gli archivi di configurazione nel livello Standard potrebbero verificarsi limitazioni temporanee quando la frequenza delle richieste supera le 20.000 richieste all'ora.

Quando un archivio raggiunge il limite, restituirà il codice di stato HTTP 429 per tutte le richieste effettuate fino alla scadenza del periodo di tempo. L'intestazione `retry-after-ms` nella risposta fornisce un tempo di attesa consigliato (in millisecondi) prima di ritentare la richiesta.

Se l'applicazione riscontra regolarmente il codice di stato HTTP 429 risposte, è consigliabile riprogettarla per ridurre il numero di richieste effettuate. Per altre informazioni, vedere [Ridurre le richieste effettuate alla configurazione dell'appFor](./howto-best-practices.md#reduce-requests-made-to-app-configuration) more information, see Reduce requests made to App Configuration

## <a name="my-application-receives-http-status-code-429-responses-why"></a>L'applicazione riceve il codice di stato HTTP 429 risposte. Perché?

Riceverai una risposta al codice di stato HTTP 429 in queste circostanze:

* Superamento del limite di richieste giornaliere per un archivio nel livello Gratuito.
* Limitazione temporanea a causa di una frequenza di richieste elevata per un archivio nel livello Standard.Temporary throttling due to a high request rate for a store in the Standard tier.
* Utilizzo eccessivo della larghezza di banda.
* Tentativo di creare o modificare una chiave quando viene superata l'offerta di archiviazione.

Controllare il corpo della risposta 429 per il motivo specifico per cui la richiesta non è riuscita.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Come posso ricevere annunci sulle nuove versioni e altre informazioni relative a Configurazione app?

Iscriviti al nostro [repository di annunci GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Come posso segnalare un problema o dare un suggerimento?

Puoi contattarci direttamente su [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su Configurazione app di Azure](./overview.md)
