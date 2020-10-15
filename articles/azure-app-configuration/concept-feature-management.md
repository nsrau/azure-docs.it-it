---
title: Informazioni sulla gestione delle funzionalità con la configurazione di app Azure
description: Attivare e disattivare le funzionalità usando la configurazione di app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b77f0063b37adbfaecaff68387e858d0077561b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212661"
---
# <a name="feature-management-overview"></a>Panoramica della gestione delle funzionalità

Di norma, per distribuire una nuova funzionalità di un'applicazione, è necessario ridistribuire completamente l'applicazione stessa. Il test di una funzionalità spesso richiede più distribuzioni dell'applicazione.  Ogni distribuzione può modificare la funzionalità o esporre la funzionalità a clienti diversi per il test.  

La gestione delle funzionalità è una moderna procedura di sviluppo di software che separa il rilascio di una funzionalità dalla distribuzione del codice e consente di modificare rapidamente la disponibilità on demand. Usa una tecnica chiamata *flag di funzionalità* (nota anche come *feature toggle*, *opzione di funzionalità* e così via) per gestire in modo dinamico il ciclo di vita di una funzionalità.

Gestione delle funzionalità consente agli sviluppatori di risolvere i problemi seguenti:

* **Gestione dei rami del codice**: usare i flag funzionalità per eseguire il wrapping delle nuove funzionalità dell'applicazione attualmente in fase di sviluppo Tali funzionalità sono "nascoste" per impostazione predefinita. È possibile distribuirle senza problemi, anche se non completate, perché rimarranno inattive nell'ambiente di produzione. Con questo approccio, chiamato *dark deployment*, è possibile rilasciare tutto il codice alla fine di ogni ciclo di sviluppo. Non è più necessario mantenere i rami di codice tra più cicli di sviluppo perché una determinata funzionalità richiede più di un ciclo per il completamento.
* **Test in produzione**: usare i flag funzionalità per concedere l'accesso in anticipo alle nuove funzionalità nell'ambiente di produzione. Ad esempio, è possibile limitare l'accesso ai membri del team o ai tester beta interni. Questi utenti apprezzeranno l'esperienza di produzione con la massima fedeltà anziché un'esperienza simulata o parziale in un ambiente di test.
* **Volo**: usare i flag funzionalità per implementare in modo incrementale nuove funzionalità per gli utenti finali. È possibile impostare come destinazione una piccola percentuale della popolazione utente e aumentare gradualmente la percentuale nel tempo.
* **Switch Kill immediato**: i flag delle funzionalità forniscono una rete di sicurezza intrinseca per il rilascio di nuove funzionalità. È possibile attivare e disattivare le funzionalità dell'applicazione senza ridistribuire il codice. Se necessario, è possibile disabilitare rapidamente una funzionalità senza ricompilare e ridistribuire l'applicazione.
* **Attivazione selettiva**: usare i flag funzionalità per segmentare gli utenti e fornire un set specifico di funzionalità a ogni gruppo. È possibile che si disponga di una funzionalità che funziona solo in un determinato Web browser. È possibile definire un flag in modo che solo gli utenti di tale browser possano visualizzare e usare la funzionalità. Questo approccio consente di espandere facilmente l'elenco di browser supportati in un secondo momento senza dover modificare il codice.

## <a name="basic-concepts"></a>Concetti fondamentali

Ecco alcuni nuovi termini relativi alla gestione delle funzionalità:

* **Flag funzionalità**: un flag di funzionalità è una variabile con lo stato binario *on* o *off*. Ha anche un blocco di codice associato. Lo stato del flag funzionalità attiva se il blocco di codice viene eseguito.
* **Gestione funzionalità**: un gestore di funzionalità è un pacchetto dell'applicazione che gestisce il ciclo di vita di tutti i flag di funzionalità in un'applicazione. Gestione funzionalità fornisce inoltre funzionalità aggiuntive, inclusi i flag di funzionalità di memorizzazione nella cache e l'aggiornamento dei relativi stati.
* **Filter**: un filtro è una regola per la valutazione dello stato di un flag di funzionalità. I filtri potenziali includono i gruppi di utenti, i tipi di dispositivi o browser, le posizioni geografiche e le finestre temporali.

Un'implementazione efficace della gestione funzionalità prevede almeno due componenti che operano congiuntamente:

* Un'applicazione che usa i flag di funzionalità.
* Un repository separato che archivia i flag di funzionalità con gli stati correnti.

## <a name="using-feature-flags-in-your-code"></a>Uso dei flag funzionalità nel codice

Il modello di base per l'implementazione dei flag di funzionalità in un'applicazione è semplice. Un flag di funzionalità è una variabile di stato booleana che controlla un'istruzione condizionale nel codice:

```csharp
if (featureFlag) {
    // Run the following code
}
```

È possibile impostare il valore di in modo `featureFlag` statico.

```csharp
bool featureFlag = true;
```

È possibile valutare lo stato del flag in base a determinate regole:

```csharp
bool featureFlag = isBetaUser();
```

È possibile estendere l'oggetto condizionale per impostare il comportamento dell'applicazione per uno dei due stati:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repository di flag funzionalità

Per usare i flag di funzionalità in modo efficace, è necessario esternalizzare tutti i flag di funzionalità usati in un'applicazione. In questo modo è possibile modificare gli Stati dei flag delle funzionalità senza modificare e ridistribuire l'applicazione stessa.

App Azure configurazione fornisce un repository centralizzato per i flag funzionalità. È possibile usarlo per definire diversi tipi di flag di funzionalità e modificarne gli stati rapidamente e in tutta sicurezza. È quindi possibile usare le librerie di Configurazione app per diversi framework di linguaggi di programmazione per accedere con facilità a questi flag di funzionalità dall'applicazione.

L'esercitazione [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md) illustra come il provider di Configurazione app per .NET Core e le librerie di Gestione funzionalità vengano usati insieme per implementare i flag di funzionalità per l'applicazione Web ASP.NET.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere i flag di funzionalità a un'app Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
