---
title: Risolvere i problemi relativi ai criteri personalizzati in Azure Active Directory B2C
description: Informazioni sugli approcci per la risoluzione degli errori quando si lavora con i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5dee0ef768180057452a232436fc295b36fd756c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963744"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Risoluzione dei problemi del framework di esperienza di gestione delle identità e criteri personalizzati di Azure AD B2C

Se si usano i criteri personalizzati di Azure Active Directory B2C (Azure AD B2C), si potrebbero verificare problemi di impostazione del framework di esperienza di gestione delle identità nel relativo formato XML del linguaggio dei criteri. Imparare a scrivere criteri personalizzati è come apprendere un nuovo linguaggio. In questo articolo vengono descritti alcuni strumenti e suggerimenti che consentono di individuare e risolvere i problemi.

Questo articolo è incentrato sulla risoluzione dei problemi della configurazione dei criteri personalizzati di Azure AD B2C. Non tratta l'applicazione relying party o la relativa libreria di identità.

## <a name="xml-editing"></a>Modifica dell'XML

L'errore più comune nell'impostazione dei criteri è la formattazione XML non corretta. Un buon editor XML è essenziale. Viene visualizzato il codice XML in modo nativo, i codici colore, precompila i termini comuni, mantiene indicizzati gli elementi XML e può essere convalidato in base a un XML Schema.

Due degli editor preferiti sono [Visual Studio Code](https://code.visualstudio.com/) e [notepad + +](https://notepad-plus-plus.org/).

La convalida dello schema XML identifica gli errori prima del caricamento del file XML. Nella cartella radice dello [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), ottenere il file di definizione XML schema *trustframeworkpolicy_ 0.3.0.0. xsd*. Per informazioni sull'utilizzo del file dello schema XSD per la convalida nell'editor, cercare gli *strumenti XML* e la *convalida XML* o simili nella documentazione dell'editor.

Una revisione delle regole XML può risultare utile. Azure AD B2C rifiuta qualsiasi errore di formattazione XML che rileva. Un codice XML non correttamente formattato può restituire a volte messaggi di errore fuorvianti.

## <a name="upload-policies-and-policy-validation"></a>Convalida dei criteri e del caricamento dei criteri

La convalida del file di criteri XML viene eseguita automaticamente al caricamento. La maggior parte degli errori causano errori del caricamento. La convalida include il file dei criteri che si sta caricando. Include anche la catena di file a cui si riferisce il file di caricamento (il file dei criteri relying party, il file delle estensioni e il file di base).

Gli errori di convalida comuni includono i seguenti:

> Frammento con errore: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Il valore ClaimType potrebbe non essere scritto correttamente oppure non esiste nello schema.
* I valori ClaimType devono essere definiti in almeno uno dei file nei criteri.
    Ad esempio: `<ClaimType Id="issuerUserId">`
* Se ClaimType è definito nel file delle estensioni, ma è usato anche in un valore TechnicalProfile nel file di base, il caricamento del file di base restituirà un errore.

> Frammento con errore: `...makes a reference to a ClaimsTransformation with id...`

* Le cause di questo errore possono essere uguali a quelle dell'errore ClaimType.

> Frammento con errore: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Verificare che il valore TenantId negli elementi `<TrustFrameworkPolicy\>` e `<BasePolicy\>` corrisponda al tenant Azure ad B2C di destinazione.

## <a name="troubleshoot-the-runtime"></a>Risoluzione dei problemi di runtime

* Usare **Esegui ora** e `https://jwt.ms` per testare i criteri indipendentemente dall'applicazione Web o per dispositivi mobili. Questo sito Web funziona come un'applicazione relying party. Viene visualizzato il contenuto del token JSON Web (JWT) che viene generato per i criteri di Azure AD B2C. Per creare un'applicazione di test, passare a **Azure ad B2C** \> **applicazioni** nel portale di Azure e aggiungere un'applicazione con i valori seguenti:

  * **Nome**: TestApp
  * **App Web/API Web**: No
  * **Native Client**: No

  Quindi, aggiungere `https://jwt.ms` come **URL di risposta**.

* Per tracciare lo scambio di messaggi tra il browser client e Azure AD B2C usare [Fiddler](https://www.telerik.com/fiddler). Consente di ottenere un'indicazione del punto in cui il percorso utente genera errori nei passaggi di orchestrazione.

* In **Modalità sviluppo** usare [Application Insights](active-directory-b2c-troubleshoot-custom.md) per tracciare l'attività del percorso utente nel framework di esperienza di gestione delle identità. In **modalità di sviluppo**è possibile osservare lo scambio di attestazioni tra il Framework dell'esperienza di identità e i diversi provider di attestazioni definiti da profili tecnici, ad esempio provider di identità, servizi basati su API, l'utente Azure ad B2C Directory e altri servizi, ad esempio Azure Multifactor Authentication.

## <a name="recommended-practices"></a>Procedure consigliate

**Mantenere più versioni degli scenari. Raggrupparli in un progetto con l'applicazione.** I file base, delle estensioni e relying party dipendono direttamente tra loro. Salvarli come gruppo. Quando vengono aggiunte nuove funzionalità ai criteri, mantenere versioni di lavoro separate. Inserire le versioni di lavoro nel file system con il codice dell'applicazione con cui interagiscono. Le applicazioni potrebbero richiamare diversi criteri relying party in un tenant. Potrebbero diventare dipendenti dalle attestazioni previste dai criteri di Azure AD B2C.

**Sviluppare e testare i profili tecnici con percorsi utente noti.** Usare i criteri starter pack testati per configurare i profili tecnici. Testarli separatamente prima di includerli nel proprio percorso utente.

**Sviluppare e testare i percorsi utente con profili tecnici testati.** Modificare i passaggi di orchestrazione di un percorso utente in modo incrementale. Compilare progressivamente gli scenari previsti.

## <a name="next-steps"></a>Passaggi successivi

Disponibile su GitHub, scaricare l'archivio [Active-Directory-B2C-Custom-Policy-Starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip. È anche possibile clonare il repository:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
