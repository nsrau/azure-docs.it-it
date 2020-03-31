---
title: Comprendere la gestione delle funzionalità usando La configurazione delle app di AzureUnderstand feature management using Azure App Configuration
description: Attivare e disattivare le funzionalità usando Configurazione app di Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523731"
---
# <a name="feature-management-overview"></a>Panoramica della gestione delle funzionalità

Di norma, per distribuire una nuova funzionalità di un'applicazione, è necessario ridistribuire completamente l'applicazione stessa. Il test di una funzionalità spesso richiede più distribuzioni dell'applicazione.  Ogni distribuzione può modificare la funzionalità o esporre la funzionalità a clienti diversi per il test.  

La gestione delle funzionalità è una moderna procedura di sviluppo di software che separa il rilascio di una funzionalità dalla distribuzione del codice e consente di modificare rapidamente la disponibilità on demand. Usa una tecnica chiamata *flag di funzionalità* (nota anche come *feature toggle*, *opzione di funzionalità* e così via) per gestire in modo dinamico il ciclo di vita di una funzionalità.

La gestione delle funzionalità consente agli sviluppatori di risolvere i problemi seguenti:Feature management helps developers address the following problems:

* **Gestione ramo del codice**: utilizzare i flag di funzionalità per eseguire il wrapping delle nuove funzionalità dell'applicazione attualmente in fase di sviluppo. Tali funzionalità sono "nascoste" per impostazione predefinita. È possibile distribuirle senza problemi, anche se non completate, perché rimarranno inattive nell'ambiente di produzione. Con questo approccio, chiamato *dark deployment*, è possibile rilasciare tutto il codice alla fine di ogni ciclo di sviluppo. Non è più necessario gestire i rami del codice in più cicli di sviluppo perché una determinata funzionalità richiede più di un ciclo per il completamento.
* **Test nell'ambiente di produzione:** utilizzare i flag di funzionalità per concedere l'accesso anticipato alle nuove funzionalità nell'ambiente di produzione. Ad esempio, è possibile limitare l'accesso ai membri del team o ai beta tester interni. Questi utenti sperimenteranno l'esperienza di produzione con fedeltà completa invece di un'esperienza simulata o parziale in un ambiente di test.
* **Volo**: utilizzare i flag di funzionalità per implementare in modo incrementale le nuove funzionalità agli utenti finali. È possibile scegliere come target una piccola percentuale della popolazione di utenti e aumentare tale percentuale gradualmente nel tempo.
* **Interruttore di uccisione istantanea**: I flag di funzionalità forniscono una rete di sicurezza intrinseca per il rilascio di nuove funzionalità. È possibile attivare e disattivare le funzionalità dell'applicazione senza ridistribuire alcun codice. Se necessario, è possibile disabilitare rapidamente una funzionalità senza ricompilare e ridistribuire l'applicazione.
* **Attivazione selettiva**: Utilizzare i flag di funzionalità per segmentare gli utenti e fornire un set specifico di funzionalità a ogni gruppo. Potresti avere una funzione che funziona solo su un determinato browser web. È possibile definire un flag in modo che solo gli utenti di tale browser possano visualizzare e usare la funzionalità. Questo approccio consente di espandere facilmente l'elenco di browser supportati in un secondo momento senza dover modificare il codice.

## <a name="basic-concepts"></a>Concetti fondamentali

Ecco alcuni nuovi termini relativi alla gestione delle funzionalità:

* **Flag di entità geografiche**: Un flag di entità geografiche è una variabile con uno stato binario *di on* o *off*. Ha anche un blocco di codice associato. Lo stato del flag di funzionalità attiva l'esecuzione del blocco di codice.
* **Gestione funzionalità:** un gestore di funzionalità è un pacchetto dell'applicazione che gestisce il ciclo di vita di tutti i flag di funzionalità in un'applicazione. Il gestore di funzionalità fornisce inoltre funzionalità aggiuntive, tra cui la memorizzazione nella cache dei flag di funzionalità e l'aggiornamento dei relativi stati.
* **Filtro**: Un filtro è una regola per valutare lo stato di un flag di feature. I filtri potenziali includono gruppi di utenti, tipi di dispositivi o browser, posizioni geografiche e intervalli di tempo.

Un'implementazione efficace della gestione funzionalità prevede almeno due componenti che operano congiuntamente:

* Un'applicazione che usa i flag di funzionalità.
* Un repository separato che archivia i flag di funzionalità con gli stati correnti.

## <a name="using-feature-flags-in-your-code"></a>Utilizzo dei flag di funzionalità nel codiceUsing feature flags in your code

Il modello di base per l'implementazione dei flag di funzionalità in un'applicazione è semplice. Un flag di funzionalità è una variabile di stato booleana che controlla un'istruzione condizionale nel codice:A feature flag is a Boolean state variable controlling a conditional statement in your code:

```csharp
if (featureFlag) {
    // Run the following code
}
```

È possibile impostare `featureFlag` il valore di staticamente.

```csharp
bool featureFlag = true;
```

È possibile valutare lo stato del flag in base a determinate regole:

```csharp
bool featureFlag = isBetaUser();
```

È possibile estendere la condizione per impostare il comportamento dell'applicazione per entrambi gli stati:You can extend the conditional to set application behavior for either state:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repository di flag funzionalità

Per usare i flag di funzionalità in modo efficace, è necessario esternalizzare tutti i flag di funzionalità usati in un'applicazione. In questo modo è possibile modificare gli stati dei flag delle funzionalità senza modificare e ridistribuire l'applicazione stessa.

Configurazione app di Azure offre un repository centralizzato per i flag di funzionalità. È possibile usarlo per definire diversi tipi di flag di funzionalità e modificarne gli stati rapidamente e in tutta sicurezza. È quindi possibile usare le librerie di Configurazione app per diversi framework di linguaggi di programmazione per accedere con facilità a questi flag di funzionalità dall'applicazione.

L'esercitazione [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md) illustra come il provider di Configurazione app per .NET Core e le librerie di Gestione funzionalità vengano usati insieme per implementare i flag di funzionalità per l'applicazione Web ASP.NET.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere i flag di funzionalità a un'app Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
