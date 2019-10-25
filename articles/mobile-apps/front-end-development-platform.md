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
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795497"
---
# <a name="choose-mobile-development-framework"></a>Scegliere il Framework di sviluppo per dispositivi mobili
Gli sviluppatori possono usare le tecnologie lato client per creare applicazioni per dispositivi mobili, usando Framework e modelli specifici per un approccio multipiattaforma. Gli sviluppatori possono compilare applicazioni native (native a piattaforma singola usando linguaggi come Objective-C e Java), applicazioni multipiattaforma che usano Novell, .NET e C#) e ibride (usando Cordova) e le relative varianti, a seconda della loro decisione fattori.

## <a name="native-platforms"></a>Piattaforme native
La creazione di un'applicazione nativa richiede linguaggi di programmazione, SDK, ambiente di sviluppo e altri strumenti specifici della piattaforma forniti dai fornitori del sistema operativo.

### <a name="ios"></a>iOS
Creato e sviluppato da Apple, le app basate su iOS vengono eseguite su dispositivi Apple, ovvero iPhone e iPad.

- **Linguaggi di programmazione** -Objective-C, Swift
- **IDE** -Xcode
- **SDK** -iOS SDK

### <a name="android"></a>Android
Progettato da Google e dal sistema operativo più diffuso nel mondo, le applicazioni basate su Android possono essere eseguite su una vasta gamma di smartphone e tablet.

- **Linguaggio di programmazione** -Java, Kotlin 
- **IDE** -Android Studio e Android Strumenti di sviluppo 
- **SDK** -Android SDK

### <a name="windows"></a>Windows
- **Linguaggio di programmazione** -C#
- **IDE** -Visual Studio, Visual Studio Code
- **SDK** -Windows SDK

#### <a name="pros"></a>Vantaggi
- Esperienza utente ottimale
- Applicazioni reattive con prestazioni elevate e possibilità di interfacciarsi con le librerie native
- Applicazioni altamente sicure

#### <a name="cons"></a>Svantaggi
- L'applicazione viene eseguita solo su una piattaforma
- Altre risorse per gli sviluppatori e costose per la creazione di un'applicazione
- Riutilizzo ridotto del codice

## <a name="cross-platforms-and-hybrid-applications"></a>Applicazioni ibride e multipiattaforma
Le applicazioni multipiattaforma ti offrono la possibilità di scrivere applicazioni per dispositivi mobili native una volta, condividere il codice ed eseguirle in iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
Di proprietà di Microsoft, [Novell](https://visualstudio.microsoft.com/xamarin/) consente di creare applicazioni per dispositivi mobili affidabili e multipiattaforma in C#, con una libreria di classi e un runtime che funziona su più piattaforme, tra cui iOS, Android e Windows, compilando comunque i nativi (non interpretati). ) applicazioni altamente performanti. Novell combina tutte le funzionalità delle piattaforme native e aggiunge numerose funzionalità potenti.

- **Linguaggio di programmazione** -C#
- **IDE** -Visual Studio in Windows o Mac

### <a name="react-native"></a>React Native
Rilasciato da Facebook nel 2015, [React native](https://facebook.github.io/react-native/) è un framework JavaScript [Open Source](https://github.com/facebook/react-native) per la scrittura di applicazioni per dispositivi mobili reali e con rendering nativo per iOS e Android. Si basa su REACT, la libreria JavaScript di Facebook per la creazione di interfacce utente, ma invece di indirizzare il browser, è destinata a piattaforme mobili. React native usa componenti nativi anziché componenti Web come blocchi predefiniti.
 
- **Linguaggio di programmazione** -JavaScript
- **IDE** -Visual Studio Code

### <a name="unity"></a>Unity
 Unity è un motore ottimizzato per la creazione di giochi che consente agli sviluppatori di creare applicazioni 2D/ C# 3D di alta qualità con per diverse piattaforme, tra cui Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
Cordova consente di creare applicazioni ibride usando Strumenti di Visual Studio per Apache Codova o Visual Studio Code con estensioni per Cordova. Con l'approccio ibrido, è possibile condividere componenti con siti Web e riutilizzare le applicazioni basate su server Web con approcci di applicazioni Web ospitate basate su Cordova.

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
