---
title: Domande frequenti sulla configurazione di app Azure
description: Leggere le risposte alle domande frequenti sulla configurazione di app Azure, ad esempio il modo in cui è diversa da Azure Key Vault.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 10510774409a6b75687086290b32885b30d9131a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335382"
---
# <a name="azure-app-configuration-faq"></a>Domande frequenti sulla configurazione di app Azure

Questo articolo fornisce le risposte alle domande frequenti sulla configurazione app Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>In che modo la configurazione delle app è diversa da Azure Key Vault?

La configurazione delle app consente agli sviluppatori di gestire le impostazioni dell'applicazione e controllare la disponibilità delle funzionalità Mira a semplificare molte delle attività di utilizzo di dati di configurazione complessi.

La configurazione dell'app supporta:

- Spazi dei nomi gerarchici
- Etichettatura
- Query estese
- Recupero in batch
- Operazioni di gestione specializzate
- Interfaccia utente di gestione delle funzionalità

La configurazione dell'app è complementare a Key Vault e le due devono essere usate side-by-side nella maggior parte delle distribuzioni dell'applicazione.

## <a name="should-i-store-secrets-in-app-configuration"></a>È consigliabile archiviare I segreti nella configurazione dell'app?

Sebbene la configurazione dell'app fornisca sicurezza avanzata, Key Vault rappresenta comunque il posto migliore per l'archiviazione dei segreti delle applicazioni. Key Vault fornisce la crittografia a livello di hardware, i criteri di accesso granulari e le operazioni di gestione, ad esempio la rotazione del certificato.

È possibile creare valori di configurazione dell'app che fanno riferimento a segreti archiviati in Key Vault. Per altre informazioni, vedere [usare Key Vault riferimenti in un'app ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>La configurazione dell'app crittografa i dati?

Sì. La configurazione dell'app crittografa tutti i valori di chiave che possiede e crittografa le comunicazioni di rete. I nomi delle chiavi e le etichette vengono usati come indici per il recupero dei dati di configurazione e non vengono crittografati.

## <a name="where-does-data-stored-in-app-configuration-reside"></a>Dove si trovano i dati archiviati nella configurazione dell'app? 

I dati dei clienti archiviati nella configurazione dell'app si trovano nell'area in cui è stato creato l'archivio di configurazione dell'app del cliente. La configurazione dell'app può replicare i dati nelle [aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) per la resilienza dei dati, ma non eseguirà la replica o lo spostamento dei dati del cliente all'esterno dell'area geografica, come definito dalla [residenza dei dati](https://azure.microsoft.com/global-infrastructure/data-residency/) I clienti e gli utenti finali possono spostare, copiare o accedere ai dati dei clienti da qualsiasi posizione a livello globale.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>In che modo la configurazione delle app è diversa dalle impostazioni del servizio app Azure?

App Azure servizio consente di definire le impostazioni dell'app per ogni istanza del servizio app. Queste impostazioni vengono passate come variabili di ambiente al codice dell'applicazione. Se lo si desidera, è possibile associare un'impostazione a uno slot di distribuzione specifico. Per altre informazioni, vedere [configurare le impostazioni dell'app](/azure/app-service/configure-common#configure-app-settings).

Al contrario, la configurazione di app Azure consente di definire le impostazioni che possono essere condivise tra più app. Sono incluse le app in esecuzione nel servizio app e altre piattaforme. Il codice dell'applicazione accede a queste impostazioni tramite i provider di configurazione per .NET e Java, tramite Azure SDK o direttamente tramite le API REST.

È anche possibile importare ed esportare le impostazioni tra il servizio app e la configurazione dell'app. Questa funzionalità consente di configurare rapidamente un nuovo archivio di configurazione dell'app in base alle impostazioni del servizio app esistenti. È anche possibile condividere la configurazione con un'app esistente che si basa sulle impostazioni del servizio app.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Esistono limitazioni alle dimensioni delle chiavi e dei valori archiviati nella configurazione dell'app?

È previsto un limite di 10 KB per un singolo elemento chiave-valore.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Come è possibile archiviare le configurazioni per più ambienti (test, gestione temporanea, produzione e così via)?

Si controllano gli utenti che possono accedere alla configurazione dell'app a un livello per archivio. Usare un archivio separato per ogni ambiente che richiede autorizzazioni diverse. Questo approccio fornisce l'isolamento di sicurezza migliore.

Se non è necessario l'isolamento della sicurezza tra gli ambienti, è possibile usare le etichette per distinguere i valori di configurazione. [Usare le etichette per abilitare configurazioni diverse per ambienti diversi](./howto-labels-aspnet-core.md) fornisce un esempio completo.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quali sono le modalità consigliate per usare la configurazione delle app?

Vedere [procedure consigliate](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto costa la configurazione dell'app?

Sono disponibili due piani tariffari:

- Livello gratuito
- Livello standard.

Se è stato creato un archivio prima dell'introduzione del livello standard, questo viene spostato automaticamente al livello gratuito al momento della disponibilità generale. È possibile scegliere di eseguire l'aggiornamento al livello standard o rimanere nel livello gratuito.

Non è possibile effettuare il downgrade di un archivio dal livello standard al livello gratuito. È possibile creare un nuovo archivio nel livello gratuito e quindi importare i dati di configurazione in tale archivio.

## <a name="which-app-configuration-tier-should-i-use"></a>Quale livello di configurazione dell'app è consigliabile usare?

Entrambi i livelli di configurazione delle app offrono funzionalità principali, incluse le impostazioni di configurazione, i flag delle funzionalità, i riferimenti Key Vault, le operazioni di gestione di base, le metriche e i log.

Di seguito sono riportate alcune considerazioni per la scelta di un livello.

- **Risorse per sottoscrizione**: una risorsa è costituita da un singolo archivio di configurazione. Ogni sottoscrizione è limitata a un archivio di configurazione nel livello gratuito. Le sottoscrizioni possono avere un numero illimitato di archivi di configurazione nel livello standard.
- **Archiviazione per risorsa**: nel livello gratuito ogni archivio di configurazione è limitato a 10 MB di spazio di archiviazione. Nel livello standard, ogni archivio di configurazione può usare fino a 1 GB di spazio di archiviazione.
- **Cronologia delle chiavi**: la configurazione dell'app archivia una cronologia di tutte le modifiche apportate alle chiavi. Nel livello gratuito la cronologia viene archiviata per sette giorni. Nel livello standard la cronologia viene archiviata per 30 giorni.
- **Richieste al giorno**: gli archivi di livello gratuito sono limitati a 1.000 richieste al giorno. Quando un negozio raggiunge 1.000 richieste, restituirà il codice di stato HTTP 429 per tutte le richieste fino alla mezzanotte UTC.

    Per gli archivi di livello standard, le prime 200.000 richieste ogni giorno sono incluse nell'addebito giornaliero. Le richieste aggiuntive vengono fatturate come eccedenze.

- **Contratto di servizio**: il livello standard dispone di un contratto di servizio con disponibilità del 99,9%. Il livello gratuito non ha un contratto di licenza.
- **Funzionalità di sicurezza**: entrambi i livelli includono funzionalità di sicurezza di base, tra cui la crittografia con chiavi gestite da Microsoft, l'autenticazione tramite HMAC o Azure Active Directory, il supporto RBAC e l'identità gestita. Il livello standard offre funzionalità di sicurezza più avanzate, tra cui il supporto dei collegamenti privati e la crittografia con chiavi gestite dal cliente.
- **Costo**: gli archivi di livello standard hanno un costo di utilizzo giornaliero. È anche previsto un addebito per le richieste oltre l'allocazione giornaliera. Non è previsto alcun costo per l'uso di un archivio di livello gratuito.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>È possibile aggiornare un archivio dal livello gratuito al livello standard? È possibile effettuare il downgrade di un negozio dal livello standard al livello gratuito?

È possibile eseguire l'aggiornamento dal livello gratuito al livello standard in qualsiasi momento.

Non è possibile effettuare il downgrade di un archivio dal livello standard al livello gratuito. È possibile creare un nuovo archivio nel livello gratuito e quindi [importare i dati di configurazione in tale archivio](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Sono previsti limiti per il numero di richieste effettuate alla configurazione dell'app?

Gli archivi di configurazione nel livello gratuito sono limitati a 1.000 richieste al giorno. Per gli archivi di configurazione nel livello standard è possibile che si verifichi una limitazione temporanea quando la frequenza delle richieste supera 20.000 richieste all'ora.

Quando un negozio raggiunge il limite, restituirà il codice di stato HTTP 429 per tutte le richieste effettuate fino alla scadenza del periodo di tempo. L' `retry-after-ms` intestazione nella risposta fornisce un tempo di attesa suggerito (in millisecondi) prima di ritentare la richiesta.

Se l'applicazione presenta regolarmente risposte con codice di stato HTTP 429, provare a riprogettarla per ridurre il numero di richieste effettuate. Per altre informazioni, vedere [ridurre le richieste effettuate alla configurazione dell'app](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>L'applicazione riceve il codice di stato HTTP 429 risposte. Perché?

Si riceverà una risposta con codice di stato HTTP 429 in queste circostanze:

* Superamento del limite di richieste giornaliero per un negozio nel livello gratuito.
* Limitazione temporanea a causa di una frequenza di richieste elevata per un negozio nel livello standard.
* Utilizzo eccessivo della larghezza di banda.
* Tentativo di creare o modificare una chiave quando viene superata la quota di archiviazione.

Verificare il corpo della risposta 429 per il motivo specifico per cui la richiesta non è riuscita.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Come è possibile ricevere annunci sulle nuove versioni e altre informazioni relative alla configurazione dell'app?

Sottoscrivere il [repository di annunci GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Come è possibile segnalare un problema o inviare un suggerimento?

Puoi contattarci direttamente su [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su Configurazione app di Azure](./overview.md)
