---
title: Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure | Documentazione Microsoft
description: Riferimento concettuale e panoramica della funzionalità di sincronizzazione di dati offline nelle app per dispositivi mobili di Azure
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 7f5b24915ddb9fd12085583844770dd7587f2394
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025211"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) oggi stesso.

## <a name="what-is-offline-data-sync"></a>Che cos'è la sincronizzazione di dati offline?
La sincronizzazione di dati offline è una funzionalità dell'SDK client e server delle app per dispositivi mobili di Azure che permette agli sviluppatori di creare con facilità app che possono essere usate anche senza connessione di rete.

Quando l'app è in modalità offline, è comunque possibile creare e modificare i dati, che vengono salvati in un archivio locale. Quando l'app torna online, può sincronizzare le modifiche locali con il back-end dell'app per dispositivi mobili di Azure. Questa funzionalità include anche il supporto per il rilevamento di conflitti quando lo stesso record viene modificato nel client e nel back-end. I conflitti possono essere gestiti sul server o sul client.

La sincronizzazione offline presenta diversi vantaggi:

* Migliorare la velocità di risposta dell'app memorizzando i dati del server nella cache locale del dispositivo
* Creare app affidabili che possono essere usate anche in caso di problemi di rete
* Permettere agli utenti finali di creare e modificare i dati anche in mancanza di accesso alla rete, supportando scenari con connettività scarsa o assente
* Sincronizzare i dati tra più dispositivi e rilevare i conflitti quando lo stesso record viene modificato da due dispositivi
* Limitare l'uso della rete in reti a latenza elevata o a consumo

Le esercitazioni seguenti illustrano come aggiungere la sincronizzazione offline ai client mobili mediante le app per dispositivi mobili di Azure:

* [Android: Abilita sincronizzazione offline @ no__t-0
* [Apache Cordova: Abilita sincronizzazione offline @ no__t-0
* [iOS: Abilita sincronizzazione offline @ no__t-0
* [Xamarin iOS: Abilita sincronizzazione offline @ no__t-0
* [Xamarin Android: Abilita sincronizzazione offline @ no__t-0
* [Xamarin. Forms: Abilita sincronizzazione offline @ no__t-0
* Piattaforma @no__t 0Universal Windows Platform): Abilita sincronizzazione offline @ no__t-0

## <a name="what-is-a-sync-table"></a>Informazioni sulla tabella di sincronizzazione
Per accedere all'endpoint "/tables", gli SDK del client mobile di Azure offrono interfacce quali `IMobileServiceTable` (SDK del client .NET) o `MSTable` (client iOS). Queste API si connettono direttamente al back-end dell'app per dispositivi mobili di Azure e hanno esito negativo se il dispositivo client non ha una connessione di rete.

Per supportare l'uso offline, l'app deve usare invece le API della *tabella di sincronizzazione*, ad esempio `IMobileServiceSyncTable` (SDK del client .NET) o `MSSyncTable` (client iOS). Tutte le operazioni CRUD (Create, Read, Update, Delete) funzionano con le API della tabella di sincronizzazione, ma la lettura o la scrittura vengono effettuate in un *archivio locale*. Prima di poter eseguire qualsiasi operazione sulla tabella di sincronizzazione, è necessario inizializzare l'archivio locale.

## <a name="what-is-a-local-store"></a>Informazioni sull'archivio locale
Un archivio locale è un livello di persistenza dei dati nel dispositivo client. Gli SDK del client delle app per dispositivi mobili di Azure forniscono un'implementazione predefinita dell'archivio locale. In Windows, Xamarin e Android è basato su SQLite, mentre in iOS è basato su Core Data.

Per usare l'implementazione basata su SQLite in Windows Phone o Microsoft Store, è necessario installare un'estensione SQLite. Per ulteriori informazioni, vedere [Universal Windows Platform): Abilitare la sincronizzazione offline @ no__t-0. Android e iOS includono una versione di SQLite nel sistema operativo stesso del dispositivo, quindi non è necessario fare riferimento a una versione specifica di SQLite.

Gli sviluppatori possono anche implementare il proprio archivio locale. Ad esempio, per archiviare dati in un formato crittografato nel client mobile, è possibile definire un archivio locale che usa SQLCipher per la crittografia.

## <a name="what-is-a-sync-context"></a>Informazioni sul contesto di sincronizzazione
Un *contesto di sincronizzazione* è associato a un oggetto client mobile, ad esempio `IMobileServiceClient` o `MSClient`, e tiene traccia delle modifiche apportate con le tabelle di sincronizzazione. Il contesto di sincronizzazione gestisce una *coda operazioni*, che include un elenco ordinato di operazioni CUD (Create, Update, Delete) che viene inviato successivamente al server.

Un archivio locale è associato al contesto di sincronizzazione tramite un metodo di inizializzazione, ad esempio `IMobileServicesSyncContext.InitializeAsync(localstore)` nel [.NET SDK per client].

## <a name="how-sync-works"></a>Funzionamento della sincronizzazione offline
Quando si usano le tabelle di sincronizzazione, il codice client controlla quando le modifiche locali vengono sincronizzate con un back-end dell'app per dispositivi mobili di Azure. I dati vengono inviati al back-end solo quando viene effettuata una chiamata per il *push* delle modifiche locali. Analogamente, l'archivio locale viene popolato con nuovi dati solo quando viene effettuata una chiamata per il *pull* dei dati.

* **Push**: Il push è un'operazione sul contesto di sincronizzazione e invia tutte le modifiche CUD dall'ultimo push. Si noti che non è possibile inviare solo le modifiche di una singola tabella, perché ciò potrebbe alterare l'ordine di invio delle operazioni. L'operazione push esegue una serie di chiamate REST al back-end dell'app per dispositivi mobili di Azure, che a sua volta modifica il database del server.
* **Pull**: Il pull viene eseguito in base alla tabella e può essere personalizzato con una query per recuperare solo un subset dei dati del server. Gli SDK del client mobile di Azure inseriscono quindi i dati risultanti nell'archivio locale.
* **Push impliciti**: Se viene eseguito il pull in una tabella con aggiornamenti locali in sospeso, il pull esegue prima un `push()` nel contesto di sincronizzazione. Questo push consente di ridurre al minimo i conflitti tra le modifiche già in coda e i nuovi dati dal server.
* **Sincronizzazione incrementale**: il primo parametro dell'operazione pull è un *nome query* usato solo nel client. Se si usa un nome di query non Null, Azure Mobile SDK esegue una *sincronizzazione incrementale*. Ogni volta che un'operazione pull restituisce un set di risultati, il timestamp `updatedAt` più recente di tale set di risultati viene archiviato nelle tabelle di sistema locali dell'SDK. Le operazioni pull successive recuperano solo i record successivi a tale timestamp.

  Per usare la sincronizzazione incrementale, il server deve restituire valori `updatedAt` significativi e deve anche supportare l'ordinamento in base a questo campo. Poiché tuttavia l'SDK aggiunge il proprio ordinamento al campo updatedAt, non è possibile usare una query pull con una clausola `orderBy` specifica.

  Il nome della query può essere una stringa qualsiasi, ma deve essere univoco per ogni query logica dell'app.
  In caso contrario, diverse operazioni pull potrebbero sovrascrivere lo stesso timestamp di sincronizzazione incrementale e le query potrebbero restituire risultati non corretti.

  Se la query ha un parametro, è possibile creare un nome di query univoco incorporando il valore del parametro.
  Ad esempio, se si applica un filtro in base all'ID utente, il nome della query potrebbe essere analogo al seguente (in C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se si intende rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In questo caso, vengono recuperati tutti i record in ogni chiamata a `PullAsync`, potenzialmente inefficace.
* **Eliminazione**in: È possibile cancellare il contenuto dell'archivio locale utilizzando `IMobileServiceSyncTable.PurgeAsync`.
  La ripulitura potrebbe essere necessaria se il database client include dati non aggiornati o se si vogliono eliminare tutte le modifiche in sospeso.

  L'operazione di ripulitura cancella una tabella dall'archivio locale. Se sono presenti operazioni in attesa di sincronizzazione con il database del server, la ripulitura genera un'eccezione, a meno che non sia impostato il parametro *force purge*.

  Come esempio di dati non aggiornati sul client, si supponga che Device1 nell'esempio "todo list" esegua il pull solo di elementi non completati. Un elemento todoitem "Buy milk" viene contrassegnato come completato nel server da un altro dispositivo. Device1 include ancora tuttavia l'elemento todoitem "Buy milk" nell'archivio locale, perché esegue il pull solo degli elementi non contrassegnati come completi. Questo elemento non aggiornato viene cancellato da un'operazione di ripulitura.

## <a name="next-steps"></a>Passaggi successivi
* [iOS: Abilita sincronizzazione offline @ no__t-0
* [Xamarin iOS: Abilita sincronizzazione offline @ no__t-0
* [Xamarin Android: Abilita sincronizzazione offline @ no__t-0
* Piattaforma @no__t 0Universal Windows Platform): Abilita sincronizzazione offline @ no__t-0

<!-- Links -->
[.NET SDK per client]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Abilita sincronizzazione offline @ no__t-0
[iOS: Abilita sincronizzazione offline @ no__t-0
[Xamarin iOS: Abilita sincronizzazione offline @ no__t-0
[Xamarin Android: Abilita sincronizzazione offline @ no__t-0
Piattaforma @no__t 0Universal Windows Platform): Abilita sincronizzazione offline @ no__t-0
