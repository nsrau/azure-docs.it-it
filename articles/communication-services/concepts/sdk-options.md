---
title: Librerie client e API REST per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Altre informazioni sulle librerie client dei servizi di comunicazione di Azure e sulle API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d5aba3638ce19c24841fe5b49b19b1139327b790
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578088"
---
# <a name="client-libraries-and-rest-apis"></a>Librerie client e API REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Le funzionalità dei servizi di comunicazione di Azure sono organizzate concettualmente in sei aree. Alcune aree hanno librerie client completamente open source. La libreria client chiamante utilizza interfacce di rete proprietarie ed è attualmente closed-source e la libreria di chat include una dipendenza di origine chiusa. Gli esempi e i dettagli tecnici aggiuntivi per le librerie client sono pubblicati nel [repository GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Librerie client

| Assembly               | Protocolli             |Apri rispetto a origine chiusa| Spazi dei nomi                          | Funzionalità                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Apri            | Azure. ResourceManager. Communication | Provisioning e gestione delle risorse di servizi di comunicazione             |
| Comuni                 | REST | Apri               | Azure. Communication. Common          | Fornisce tipi di base per altre librerie client |
| Amministrazione         | REST | Apri               | Azure. Communication. Administration  | Gestire gli utenti, i token di accesso e i numeri di telefono, allocare i server STUN e TURN conformi agli standard |
| Chat                   | REST con segnalazione proprietaria | Apri con pacchetto di segnalazione origine chiuso    | Azure. Communication. chat            | Aggiungere chat basate su testo in tempo reale alle applicazioni  |
| SMS                    | REST | Apri              | Azure. Communication. SMS             | Inviare e ricevere messaggi SMS |
| Chiamata                | Trasporto proprietario | Chiusa |Azure. Communication. Calling         | Sfrutta le funzionalità di comunicazione, video, condivisione dello schermo e altre funzionalità di comunicazione dati in tempo reale          |

Si noti che le librerie client di Azure Resource Manager, amministrazione e SMS sono incentrate sull'integrazione del servizio e in molti casi si verificano problemi di sicurezza se si integrano queste funzioni nelle applicazioni dell'utente finale. Le librerie client comuni e di chat sono adatte per le applicazioni client e di servizio. La libreria client chiamante è progettata per le applicazioni client. Una libreria client incentrata sugli scenari di servizio è in fase di sviluppo.

### <a name="languages-and-publishing-locations"></a>Linguaggi e percorsi di pubblicazione

I percorsi di pubblicazione per i singoli pacchetti di librerie client sono descritti di seguito. 


| Area           | JavaScript | .NET | Python | Java | SWIFT o obj-C | Java (Android) | Altro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Vai via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Comuni         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)    | N/D      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [Swift tramite GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Amministrazione | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -  | -  | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Chiamata        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [OBJ-C tramite GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Documentazione di riferimento     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | 

## <a name="rest-apis"></a>API REST

Le API di servizi di comunicazione sono documentate insieme ad altre API REST di Azure in [docs.Microsoft.com](https://docs.microsoft.com/rest/api/azure/). In questa documentazione viene illustrato come strutturare i messaggi HTTP e vengono fornite informazioni aggiuntive per l'uso di post. Questa documentazione è disponibile anche in formato spavalderia su [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Ulteriori dettagli sul supporto

### <a name="ios-and-android-support-details"></a>dettagli del supporto per iOS e Android

- Le librerie client iOS di servizi di comunicazione sono destinate a iOS versione 13 + e Xcode 11 +.
- Le librerie client Java Android hanno come destinazione l'API Android livello 21 + e Android Studio 4.0 +

### <a name="net-support-details"></a>Dettagli del supporto .NET

Fatta eccezione per chiamare, i pacchetti di servizi di comunicazione sono destinati .NET Standard 2,0, che supporta le piattaforme elencate di seguito.

Supporto tramite .NET Framework 4.6.1
- Windows 10, 8,1, 8 e 7
- Windows Server 2012 R2, 2012 e 2008 R2 SP1

Supporto tramite .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 +
- Più versioni/distribuzioni di Linux
- UWP) 10.0.16299 (RS3) settembre 2017
- Unity 2018,1
- Mono 5.4
- Novell iOS 10,14
- Novell Mac 3,8

## <a name="api-stability-expectations"></a>Aspettative di stabilità dell'API 

> [!IMPORTANT]
> Questa sezione fornisce indicazioni sulle API REST e sulle librerie client contrassegnate come **stabili**. Le API contrassegnate come versione non definitiva, anteprima o beta possono essere modificate o deprecate **senza preavviso**. Attualmente i servizi di comunicazione di Azure si trovano in un' **anteprima pubblica**e le API sono contrassegnate come tali.

In futuro si potrebbero ritirare le versioni delle librerie client dei servizi di comunicazione ed è possibile che vengano introdotte modifiche di rilievo alle API REST e alle librerie client rilasciate. I servizi di comunicazione di Azure seguiranno *in genere* due criteri di supporto per la disattivazione delle versioni del servizio:

- Si riceverà una notifica per almeno tre anni prima che venga richiesto di modificare il codice a causa di una modifica dell'interfaccia di servizi di comunicazione. Tutte le API REST documentate e le API della libreria client in genere si riferiscono ad almeno tre anni prima di rimuovere le interfacce.
- Si riceverà una notifica almeno un anno prima di dover aggiornare gli assembly della libreria client alla versione secondaria più recente. Questi aggiornamenti richiesti non devono richiedere modifiche al codice perché si trovano nella stessa versione principale. Questa operazione è particolarmente valida per le librerie di chiamata e di chat con componenti in tempo reale che richiedono spesso aggiornamenti della sicurezza e delle prestazioni. Si consiglia vivamente di aggiornare le librerie client dei servizi di comunicazione.

### <a name="api-and-client-library-decommissioning-examples"></a>Esempi di rimozione di API e librerie client

**La versione V24 dell'API REST SMS è stata integrata nell'applicazione. Azure Communication releases V25.**

Si otterrà un avviso di 3 anni prima che queste API smettano di funzionare e vengano forzate a eseguire l'aggiornamento a V25. Questo aggiornamento potrebbe richiedere una modifica del codice.

**La versione v 2.02 della libreria client chiamante è stata integrata nell'applicazione. Azure Communication releases v 2.05.**

Potrebbe essere necessario eseguire l'aggiornamento alla versione v 2.05 della libreria client chiamante entro 12 mesi dal rilascio di v 2.05. Questa operazione deve essere una semplice sostituzione dell'artefatto senza richiedere una modifica del codice perché v 2.05 è nella versione principale V2 e non ha modifiche di rilievo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le panoramiche della libreria client seguenti:

- [Panoramica della chiamata della libreria client](../concepts/voice-video-calling/calling-sdk-features.md)
- [Panoramica della libreria client di chat](../concepts/chat/sdk-features.md)
- [Panoramica della libreria client SMS](../concepts/telephony-sms/sdk-features.md)

Per iniziare a usare i servizi di comunicazione di Azure:

- [Creare risorse di comunicazione di Azure](../quickstarts/create-communication-resource.md)
- Generare [token di accesso utente](../quickstarts/access-tokens.md)
