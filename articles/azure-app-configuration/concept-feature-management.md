---
title: Gestione di funzionalità di Configurazione app di Azure | Microsoft Docs
description: Panoramica dell'uso di Configurazione app di Azure per attivare e disattivare le funzionalità dell'applicazione on demand.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393369"
---
# <a name="feature-management-overview"></a>Panoramica della gestione delle funzionalità

Di norma, per distribuire una nuova funzionalità di un'applicazione, è necessario ridistribuire completamente l'applicazione stessa. Per testare una funzionalità, è probabile che sia necessario distribuire l'applicazione più volte per controllare quando diventa visibile o chi la visualizza.

La gestione delle funzionalità è una moderna procedura di sviluppo di software che separa il rilascio di una funzionalità dalla distribuzione del codice e consente di modificare rapidamente la disponibilità on demand. Usa una tecnica chiamata *flag di funzionalità* (nota anche come *feature toggle*, *opzione di funzionalità* e così via) per gestire in modo dinamico il ciclo di vita di una funzionalità.

La gestione delle funzionalità consente agli sviluppatori di risolvere i problemi seguenti:

* **Gestione dei rami del codice**: i flag di funzionalità vengono spesso usati per eseguire il wrapping di nuove funzionalità dell'applicazione, che sono ancora in fase di sviluppo. Tali funzionalità sono "nascoste" per impostazione predefinita. È possibile distribuirle senza problemi, anche se non completate, perché rimarranno inattive nell'ambiente di produzione. Con questo approccio, chiamato *dark deployment*, è possibile rilasciare tutto il codice alla fine di ogni ciclo di sviluppo. Non è più necessario mantenere alcun ramo di codice in più cicli a causa di una funzionalità che richiede più di un ciclo per il completamento.
* **Test in produzione**: è possibile usare i flag di funzionalità per concedere l'accesso in anteprima alle nuove funzionalità in fase di produzione. È ad esempio possibile limitare l'accesso ai soli membri del team o ai beta tester interni. Questi utenti vivranno un'esperienza ad altissima fedeltà nell'ambiente di produzione, invece di un'esperienza simulata o parziale in un ambiente di test.
* **Distribuzione in anteprima**: è anche possibile usare i flag di funzionalità per implementare nuove funzionalità per gli utenti finali in modo incrementale. All'inizio è possibile rivolgersi a una piccola percentuale della popolazione di utenti e aumentare tale percentuale gradualmente nel corso del tempo, dopo aver acquisito maggiore fiducia nell'implementazione.
* **Opzione di interruzione immediata**: i flag funzionalità offrono una rete di sicurezza intrinseca per il rilascio delle nuove funzionalità. Usandoli, è possibile attivare e disattivare facilmente le funzionalità dell'applicazione. Se necessario, è possibile disabilitare rapidamente una funzionalità senza ricompilare e ridistribuire l'applicazione.
* **Attivazione selettiva**: anche se la maggior parte dei flag è disponibile solo fino a quando le funzionalità associate vengono rilasciate correttamente, alcuni possono rimanere in uso a lungo. È possibile servirsi dei flag di funzionalità per segmentare gli utenti e rendere disponibile un set specifico di funzionalità per ogni gruppo. Una funzionalità, ad esempio, potrebbe funzionare solo in un determinato Web browser. È possibile definire un flag in modo che solo gli utenti di tale browser possano visualizzare e usare la funzionalità. Questo approccio consente di espandere facilmente l'elenco di browser supportati in un secondo momento senza dover modificare il codice.

## <a name="basic-concepts"></a>Concetti di base

Ecco alcuni nuovi termini relativi alla gestione delle funzionalità:

* **Flag di funzionalità**: un flag di funzionalità è una variabile con uno stato binario, *attivo* o *inattivo*. Ha anche un blocco di codice associato. Lo stato del flag di funzionalità determina se il blocco di codice viene eseguito o meno.
* **Gestione funzionalità**: la gestione funzionalità è un pacchetto dell'applicazione che gestisce il ciclo di vita di tutti i flag di funzionalità di un'applicazione. In genere fornisce funzioni aggiuntive, ad esempio la memorizzazione nella cache dei flag di funzionalità e l'aggiornamento dei relativi stati.
* **Filtro**: un filtro è una regola per valutare lo stato di un flag di funzionalità. Un filtro può rappresentare, ad esempio, un gruppo utenti, un tipo di dispositivo o di browser, una posizione geografica o un intervallo di tempo.

Un'implementazione efficace della gestione funzionalità prevede almeno due componenti che operano congiuntamente:

* Un'applicazione che usa i flag di funzionalità.
* Un repository separato che archivia i flag di funzionalità con gli stati correnti.

L'interazione tra questi componenti è illustrata negli esempi seguenti.

## <a name="feature-flag-usage-in-code"></a>Utilizzo dei flag di funzionalità nel codice

Il modello di base per l'implementazione dei flag di funzionalità in un'applicazione è semplice. Si può pensare a un flag di funzionalità come a una variabile di stato booleana usata con un'istruzione condizionale `if` nel codice:

```csharp
if (featureFlag) {
    // Run the following code
}
```

In questo caso, se `featureFlag` è impostato su `True`, il blocco di codice racchiuso viene eseguito. In caso contrario, viene ignorato. È possibile impostare il valore di `featureFlag` in modo statico, come nell'esempio di codice seguente:

```csharp
bool featureFlag = true;
```

Si può anche valutare lo stato del flag in base a determinate regole:

```csharp
bool featureFlag = isBetaUser();
```

Un modello di flag di funzionalità leggermente più complesso include anche un'istruzione `else`:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Tuttavia, è possibile riscrivere questo comportamento nel modello di base. L'esercitazione [Usare i flag di funzionalità in un'app ASP.NET](./use-feature-flags-dotnet-core.md) illustra i vantaggi di standardizzare un modello di codice semplice. Ad esempio:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repository di flag funzionalità

Per usare i flag di funzionalità in modo efficace, è necessario esternalizzare tutti i flag di funzionalità usati in un'applicazione. Questo approccio consente di cambiare gli stati dei flag di funzionalità senza dover modificare e ridistribuire l'applicazione stessa.

Configurazione app di Azure è progettato come archivio centralizzato per i flag di funzionalità. È possibile usarlo per definire diversi tipi di flag di funzionalità e modificarne gli stati rapidamente e in tutta sicurezza. È quindi possibile usare le librerie di Configurazione app per diversi framework di linguaggi di programmazione per accedere con facilità a questi flag di funzionalità dall'applicazione.

L'esercitazione [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md) illustra come il provider di Configurazione app per .NET Core e le librerie di Gestione funzionalità vengano usati insieme per implementare i flag di funzionalità per l'applicazione Web ASP.NET.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere i flag di funzionalità a un'app Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
