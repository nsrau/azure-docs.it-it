---
title: Scegliere la piattaforma di sviluppo front-end per la creazione di applicazioni client con Visual Studio e i servizi di Azure
description: Informazioni sui linguaggi nativi e multipiattaforma supportati per la creazione di applicazioni client.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453162"
---
# <a name="choose-mobile-development-frameworks"></a>Scegli Framework di sviluppo per dispositivi mobili
Gli sviluppatori possono utilizzare tecnologie lato client per creare applicazioni per dispositivi mobili utilizzando Framework e modelli specifici per un approccio multipiattaforma. In base ai fattori decisionali, gli sviluppatori possono compilare:
- Applicazioni a piattaforma singola native usando linguaggi come Objective C e Java
- Applicazioni multipiattaforma tramite Novell, .NET eC#
- Applicazioni ibride tramite Cordova e le relative varianti

## <a name="native-platforms"></a>Piattaforme native
La creazione di un'applicazione nativa richiede linguaggi di programmazione specifici per la piattaforma, SDK, ambienti di sviluppo e altri strumenti offerti dai fornitori del sistema operativo.

### <a name="ios"></a>iOS
Creato e sviluppato da Apple, iOS viene usato per creare app in dispositivi Apple, ovvero iPhone e iPad.

- **Linguaggi di programmazione**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Progettato da Google e dal sistema operativo più diffuso al mondo, Android viene usato per creare applicazioni che possono essere eseguite su una vasta gamma di smartphone e tablet.

- **Linguaggio di programmazione**: Java, Kotlin 
- **IDE**: strumenti di sviluppo Android Studio e Android 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Linguaggio di programmazione**:C#
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Vantaggi
- Esperienza utente ottimale
- Applicazioni reattive con prestazioni elevate e possibilità di interfacciarsi con le librerie native
- Applicazioni altamente sicure

#### <a name="cons"></a>Svantaggi
- L'applicazione viene eseguita su una sola piattaforma
- Maggiore quantità di risorse per sviluppatori e costose per la creazione di un'applicazione
- Riutilizzo ridotto del codice

## <a name="cross-platforms-and-hybrid-applications"></a>Applicazioni ibride e multipiattaforma
Le applicazioni multipiattaforma ti offrono la possibilità di scrivere applicazioni per dispositivi mobili native una volta, condividere il codice ed eseguirle in iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
Di proprietà di Microsoft, [Novell](https://visualstudio.microsoft.com/xamarin/) viene usato per creare potenti applicazioni per dispositivi mobili multipiattaforma C#in. Novell dispone di una libreria di classi e di un runtime che funziona in molte piattaforme, ad esempio iOS, Android e Windows. Vengono inoltre compilate applicazioni native (non interpretate) che offrono prestazioni elevate. Novell combina tutte le funzionalità delle piattaforme native e aggiunge numerose funzionalità potenti.

- **Linguaggio di programmazione**:C#
- **IDE**: Visual Studio in Windows o Mac

### <a name="react-native"></a>React Native
Rilasciato da Facebook nel 2015, [React native](https://facebook.github.io/react-native/) è un framework JavaScript [Open Source](https://github.com/facebook/react-native) per la scrittura di applicazioni per dispositivi mobili reali e con rendering nativo per iOS e Android. Si basa su REACT, la libreria JavaScript di Facebook per la creazione di interfacce utente. Anziché indirizzare il browser, è destinato alle piattaforme mobili. React native usa componenti nativi anziché componenti Web come blocchi predefiniti.
 
- **Linguaggio di programmazione**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Unity è un motore ottimizzato per la creazione di giochi. È possibile usarlo per creare applicazioni 2D o 3D di alta qualità con C# per piattaforme quali Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
Cordova consente di creare applicazioni ibride usando Strumenti di Visual Studio per Apache Cordova o Visual Studio Code con estensioni per Cordova. Con l'approccio ibrido, è possibile condividere i componenti con i siti Web e riutilizzare le applicazioni basate su server Web con approcci di applicazioni Web ospitate basate su Cordova.

#### <a name="pros"></a>Vantaggi
- Maggiore usabilità del codice tramite la creazione di una codebase per più piattaforme
- Rivolgersi a un pubblico più ampio in molte piattaforme
- Riduzione significativa del tempo di sviluppo
- Facile da avviare e aggiornare

#### <a name="cons"></a>Svantaggi
- Prestazioni ridotte
- Mancanza di flessibilità
- Ogni piattaforma dispone di un set univoco di caratteristiche e funzionalità per rendere più creativa l'applicazione nativa
- Miglioramento della fase di progettazione dell'interfaccia utente
- Limitazione dello strumento
