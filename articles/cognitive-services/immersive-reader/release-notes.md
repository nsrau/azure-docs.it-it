---
title: Note sulla versione di immersive Reader SDK
titleSuffix: Azure Cognitive Services
description: Altre informazioni sulle novità di immersive Reader JavaScript SDK.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133595"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Note sulla versione di Reader immersive per JavaScript SDK

## <a name="version-110"></a>Versione 1.1.0

Questa versione contiene le nuove funzionalità, le correzioni delle vulnerabilità di sicurezza, le correzioni di bug, gli aggiornamenti agli esempi di codice e le opzioni di configurazione.

#### <a name="new-features"></a>Nuove funzioni e caratteristiche

* Consentire il salvataggio e il caricamento di preferenze utente in browser e dispositivi diversi
* Abilitare la configurazione delle opzioni di visualizzazione predefinite
* Aggiunta dell'opzione per impostare il linguaggio di traduzione, abilitare la traduzione di Word e abilitare la traduzione dei documenti all'avvio del lettore immersivo
* Aggiunta del supporto per la configurazione di lettura ad alta voce tramite opzioni
* Aggiungere la possibilità di disabilitare l'esperienza di esecuzione prima
* Aggiungere ImmersiveReaderView per UWP

#### <a name="improvements"></a>Miglioramenti

* Aggiornare il codice HTML di esempio di codice Android per lavorare con l'SDK più recente
* Aggiornamento della risposta di avvio per restituire il numero di caratteri elaborati
* Aggiornare gli esempi di codice per usare v 1.1.0
* Non consentire la chiamata di launchAsync quando è già in corso il caricamento
* Verificare la presenza di contenuto non valido ignorando i messaggi in cui i dati non sono una stringa
* Eseguire il wrapping della chiamata a Window in una clausola if per verificare il supporto del browser di Promise

#### <a name="fixes"></a>Correzioni

* Correggere dependabot rimuovendo Yarn. Lock da gitignore
* Correzione della vulnerabilità della sicurezza mediante l'aggiornamento di Pug a v 3.0.0 in Guida introduttiva-esempio di codice NodeJS
* Correzione di più vulnerabilità di sicurezza tramite l'aggiornamento di pacchetti jest e TypeScript
* Correzione di una vulnerabilità di sicurezza tramite l'aggiornamento di Microsoft. IdentityModel. clients. ActiveDirectory a v 5.2.0

<br>

## <a name="version-100"></a>Versione 1.0.0

Questa versione contiene modifiche di rilievo, nuove funzionalità, miglioramenti degli esempi di codice e correzioni di bug.

#### <a name="breaking-changes"></a>Modifiche di rilievo

* Richiedi Azure AD token e sottodominio e depreca i token usati nelle versioni precedenti.
* Impostare CookiePolicy su Disabled. Per impostazione predefinita, la conservazione delle preferenze utente è disabilitata. Il lettore viene avviato ogni volta con le impostazioni predefinite, a meno che CookiePolicy non sia impostato su abilitato.

#### <a name="new-features"></a>Nuove funzioni e caratteristiche

* Aggiungere il supporto per abilitare o disabilitare i cookie
* Aggiungere l'esempio di codice di avvio rapido di Android Kotlin
* Aggiungere l'esempio di codice di avvio rapido per Android Java
* Aggiungere Node.js esempio di codice di avvio rapido

#### <a name="improvements"></a>Miglioramenti

* Aggiornare Node.js Advanced README.md
* Modificare l'esempio di codice Python da avanzate a avvio rapido
* Spostare l'esempio di codice iOS Swift in JS/Samples
* Aggiornare gli esempi di codice per usare la versione 1.0.0

#### <a name="fixes"></a>Correzioni

* Correzione per Node.js esempio di codice avanzato
* Aggiungere i file mancanti per Advanced-CSharp-multiple-Resources
* Rimuovi it-US da collegamenti ipertestuali

<br>

## <a name="version-003"></a>Versione 0.0.3

Questa versione include nuove funzionalità, miglioramenti a esempi di codice, correzioni di vulnerabilità di sicurezza e correzioni di bug.

#### <a name="new-features"></a>Nuove funzioni e caratteristiche

* Aggiungere un esempio di codice Swift iOS
* Aggiungere un esempio di codice C# avanzato che illustra l'uso di più risorse 
* Aggiungere il supporto per disabilitare la funzionalità di attivazione/disattivazione a schermo intero
* Aggiungere il supporto per nascondere il pulsante di uscita dell'applicazione immersive Reader
* Aggiungere una funzione di callback che può essere usata dall'applicazione host all'uscita dal reader immersivo
* Aggiornare gli esempi di codice per usare l'autenticazione Azure Active Directory

#### <a name="improvements"></a>Miglioramenti

* Aggiornare il codice C# Advanced sample per includere un documento di Word
* Aggiornare gli esempi di codice per usare v 0.0.3

#### <a name="fixes"></a>Correzioni

* Aggiornare lodash alla versione 4.17.14 per correggere la vulnerabilità di sicurezza
* Aggiornare la libreria MSAL C# per correggere la vulnerabilità di sicurezza
* Aggiornare mixin-Deep alla versione 1.3.2 per correggere la vulnerabilità di sicurezza
* Aggiornamento di jest, Webpack e Webpack-CLI che usavano versioni vulnerabili di set-Value e mixin-Deep per la correzione della vulnerabilità della sicurezza

<br>

## <a name="version-002"></a>Versione 0.0.2

Questa versione include nuove funzionalità, miglioramenti a esempi di codice, correzioni di vulnerabilità di sicurezza e correzioni di bug.

#### <a name="new-features"></a>Nuove funzioni e caratteristiche

* Aggiungere l'esempio di codice avanzato Python
* Aggiungere l'esempio di codice di avvio rapido Java
* Aggiungere un semplice esempio di codice

#### <a name="improvements"></a>Miglioramenti

* Rinominare resourceName in cogSvcsSubdomain
* Spostare i segreti fuori dal codice e usare le variabili di ambiente
* Aggiornare gli esempi di codice per usare v 0.0.2

#### <a name="fixes"></a>Correzioni

* Correzione dei bug di accessibilità dei pulsanti di lettura immersivi
* Correzione dello scorrimento interruppe
* Aggiornare il pacchetto manubrio alla versione 4.1.2 per correggere la vulnerabilità di sicurezza
* Corregge i bug negli unit test SDK
* Corregge i bug di compatibilità JavaScript di Internet Explorer 11
* Aggiornamenti URL SDK

<br>

## <a name="version-001"></a>Versione 0.0.1

Versione iniziale dell'SDK JavaScript immersive Reader.

* Aggiunta dell'SDK JavaScript immersive Reader
* Aggiunta del supporto per specificare la lingua dell'interfaccia utente
* Aggiungere un timeout per determinare quando la funzione launchAsync deve avere esito negativo con un errore di timeout
* Aggiungere il supporto per specificare l'indice z dell'iframe del lettore immersivo
* Aggiungere il supporto per usare un tag WebView anziché un iframe, per la compatibilità con le app Chrome
* Aggiungere unit test SDK
* Aggiungere Node.js esempio di codice avanzato
* Aggiungere un esempio di codice C# avanzato
* Aggiungere l'esempio di codice di avvio rapido C#
* Aggiungere la configurazione del pacchetto, Yarn e altri file di compilazione
* Aggiungere i file di configurazione git
* Aggiungere file README.md a esempi di codice e SDK
* Aggiungi licenza MIT
* Aggiungere istruzioni per i collaboratori
* Aggiungere asset SVG pulsante icona statica

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare lo strumento di lettura immersiva:

* Vedere le [informazioni di riferimento sulla libreria client dello strumento di lettura immersiva](./reference.md)
* Esplorare la [libreria client dello strumento di lettura immersiva in GitHub](https://github.com/microsoft/immersive-reader-sdk)
