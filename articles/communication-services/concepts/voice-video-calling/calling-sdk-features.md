---
title: Panoramica della libreria client per le chiamate di Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Fornisce una panoramica della libreria client per le chiamate.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 44365dec247b9f3135a090cee397cad32598fd29
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977868"
---
# <a name="calling-client-library-overview"></a>Panoramica della libreria client per le chiamate

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Sono disponibili due famiglie separate di librerie client per le chiamate: per i *client* e per i *servizi*. Le librerie client attualmente disponibili sono destinate all'esperienza degli utenti finali: siti Web e app native.

Le librerie client del servizio non sono ancora disponibili e forniscono l'accesso ai piani dati vocali e video non elaborati, adatti per l'integrazione con i bot e altri servizi.

## <a name="calling-client-library-capabilities"></a>Funzionalità della libreria client per le chiamate

L'elenco seguente presenta il set di funzionalità attualmente disponibili nelle librerie client per le chiamate di Servizi di comunicazione di Azure.

| Gruppo di funzionalità | Funzionalità                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Funzionalità principali | Inserire una chiamata uno-a-uno tra due utenti                                                                           | ✔️   | ✔️            | ✔️  
|                   | Inserire una chiamata di gruppo con più di due utenti (fino a 350 utenti)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Alzare di livello una chiamata uno-a-uno con due utenti in una chiamata di gruppo con più di due utenti                                 | ✔️   | ✔️            | ✔️ 
|                   | Partecipare a una chiamata di gruppo dopo che è stata avviata                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Invitare un altro utente VoIP a partecipare a una chiamata di gruppo in corso                                                       | ✔️   | ✔️            | ✔️
|                   | Attivare/Disattivare il video                                                         | ✔️   | ✔️            | ✔️ 
|                   | Attivare/Disattivare il microfono                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Passare da una fotocamera all'altra                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Bloccare/Sbloccare chiamata locale                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Altoparlante attivo                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Scegliere l'altoparlante per le chiamate                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Scegliere il microfono per le chiamate                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Mostrare lo stato di un partecipante<br/>*Inattivo, Contenuto multimediale anticipato, Connessione in corso, Connesso, In attesa, In sala di attesa, Disconnesso*         | ✔️   | ✔️            | ✔️           
|                   | Mostrare lo stato di una chiamata<br/>*Contenuto multimediale anticipato, In arrivo, Connessione in corso, Chiamata in corso, Connesso, In attesa, Disconnessione in corso, Disconnesso* | ✔️   | ✔️            | ✔️           
|                   | Mostrare se un partecipante ha l'audio disattivato                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Mostrare il motivo per cui un partecipante ha abbandonato una chiamata                                                                       | ✔️   | ✔️            | ✔️     
| Condivisione schermo    | Condividere l'intero schermo dall'applicazione                                                                 | ✔️   | ❌            | ❌           
|                   | Condividere un'applicazione specifica (dall'elenco delle applicazioni in esecuzione)                                                | ✔️   | ❌            | ❌           
|                   | Condividere una scheda del Web browser dall'elenco delle schede aperte                                                                  | ✔️   | ❌            | ❌           
|                   | Il partecipante può visualizzare la condivisione dello schermo remoto                                                                            | ✔️   | ✔️            | ✔️         
| Registro            | Elencare i partecipanti                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Rimuovere un partecipante                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Inserire una chiamata uno-a-uno con un partecipante PSTN                                                                     | ✔️   | ✔️            | ✔️   
|                   | Inserire una chiamata di gruppo con partecipanti PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Alzare di livello una chiamata uno-a-uno con un partecipante PSTN in una chiamata di gruppo                                                 | ✔️   | ✔️            | ✔️
|                   | Disconnettersi da una chiamata di gruppo come partecipante PSTN                                                                    | ✔️   | ✔️            | ✔️   
| Generale           | Testare microfono, altoparlante e fotocamera con un servizio di test audio (disponibile chiamando 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Supporto browser per la libreria client per le chiamate

La tabella seguente rappresenta il set di versioni e browser supportati attualmente disponibili.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Libreria client per le chiamate** | Chrome*, nuovo Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*Si noti che la versione più recente di Chrome è supportata in aggiunta alle due versioni precedenti.<br/>

**Si noti che sono supportate le versioni di Safari 13.1 e successive. Il video in uscita per Safari macOS non è ancora supportato, ma è supportato in iOS. La condivisione dello schermo in uscita è supportata solo in iOS per desktop.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Per altre informazioni, vedere gli articoli seguenti:
- Acquisire familiarità con i [flussi di chiamate](../call-flows.md) generali
- Informazioni sui [tipi di chiamate](../voice-video-calling/about-call-types.md)
- [Pianificare la soluzione PSTN](../telephony-sms/plan-solution.md)
