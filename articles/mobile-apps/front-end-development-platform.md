---
title: Scegliere la piattaforma di sviluppo front-end per la creazione di applicazioni client con Visual Studio e i servizi di AzureChoose the front-end development platform for building client applications with Visual Studio and Azure services
description: Informazioni sui linguaggi nativi e multipiattaforma supportati per la creazione di applicazioni client.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240851"
---
# <a name="choose-mobile-development-frameworks"></a>Scegli framework di sviluppo mobile
Gli sviluppatori possono utilizzare le tecnologie lato client per creare applicazioni mobili autonomamente utilizzando framework e modelli specifici per un approccio multipiattaforma. In base ai fattori decisionali, gli sviluppatori possono creare:
- Applicazioni native a piattaforma singola che utilizzano linguaggi come Objective C e Java
- Applicazioni multipiattaforma utilizzando Xamarin, .NET e C #
- Applicazioni ibride con Cordova e le sue varianti

## <a name="native-platforms"></a>Piattaforme native
La creazione di un'applicazione nativa richiede linguaggi di programmazione specifici della piattaforma, SDK, ambienti di sviluppo e altri strumenti forniti dai fornitori del sistema operativo.

### <a name="ios"></a>iOS
Creato e sviluppato da Apple, iOS viene utilizzato per costruire applicazioni su dispositivi Apple, vale a dire l'iPhone e l'iPad.

- **Linguaggi di programmazione**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: SDK iOS

### <a name="android"></a>Android
Progettato da Google e il sistema operativo più popolare al mondo, Android viene utilizzato per creare applicazioni che possono essere eseguite su una gamma di smartphone e tablet.

- **Linguaggio di programmazione**: Java, Kotlin 
- **IDE**: Strumenti di sviluppo Android Studio e Android 
- **SDK**: SDK Android

### <a name="windows"></a>WINDOWS
- **Linguaggio di programmazione**: C #
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Vantaggi
- Buona esperienza utente
- Applicazioni reattive con prestazioni elevate e la possibilità di interfacciarsi con librerie nativeResponsive applications with high performance and the ability to interface with native libraries
- Applicazioni altamente sicure

#### <a name="cons"></a>Svantaggi
- L'applicazione viene eseguita su una sola piattaformaApplication runs on only platform
- Maggiore utilizzo intensivo e costoso per la creazione di un'applicazione
- Riutilizzo del codice inferiore

## <a name="cross-platforms-and-hybrid-applications"></a>Applicazioni cross-piattaforma e ibride
Le applicazioni multipiattaforma ti danno il potere di scrivere applicazioni mobili native una sola volta, condividere codice ed eseguirle su iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
Di proprietà di Microsoft, [Xamarin](https://visualstudio.microsoft.com/xamarin/) viene utilizzato per creare applicazioni mobili affidabili e multipiattaforma in C . Xamarin ha una libreria di classi e runtime che funziona su molte piattaforme, ad esempio iOS, Android e Windows. Vengono inoltre compilate applicazioni native (non interpretate) che offrono prestazioni elevate. Xamarin combina tutte le abilità delle piattaforme native e aggiunge una serie di potenti caratteristiche proprie.

- **Linguaggio di programmazione**: C #
- **IDE**: Visual Studio su Windows o Mac

### <a name="react-native"></a>Reagisci in modo nativo
Rilasciato da Facebook nel 2015, [React Native](https://facebook.github.io/react-native/) è un framework JavaScript [open-source](https://github.com/facebook/react-native) per la scrittura di applicazioni mobili reali e di rendering in modo nativo per iOS e Android. Si basa su React, la libreria JavaScript di Facebook per la creazione di interfacce utente. Invece di scegliere come target il browser, si rivolge alle piattaforme mobili. React Native utilizza componenti nativi anziché componenti Web come blocchi predefiniti.
 
- **Linguaggio di programmazione:** JavaScript
- **IDE**: Codice di Visual Studio

### <a name="unity"></a>Unity
 Unity è un motore ottimizzato per la creazione di giochi. È possibile utilizzarlo per creare applicazioni 2D o 3D di alta qualità con C , per piattaforme come Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
Cordova consente di compilare applicazioni ibride utilizzando Visual Studio Tools per Apache Cordova o Visual Studio Code con estensioni per Cordova. Con l'approccio ibrido, è possibile condividere i componenti con i siti Web e riutilizzare le applicazioni basate su server Web con approcci di applicazioni Web ospitate basati su Cordova.

#### <a name="pros"></a>Vantaggi
- Maggiore usabilità del codice mediante la creazione di una codebase per più piattaforme
- Cater a un pubblico più ampio su molte piattaforme
- Drastica riduzione dei tempi di sviluppo
- Facile da lanciare e aggiornare

#### <a name="cons"></a>Svantaggi
- Prestazioni inferiori
- Mancanza di flessibilità
- Ogni piattaforma ha un set unico di caratteristiche e funzionalità per rendere l'applicazione nativa più creativa
- Aumento della fase di progettazione dell'interfaccia utente
- Limitazione dello strumento
