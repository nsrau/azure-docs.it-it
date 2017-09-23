---
title: 'Azure Active Directory B2C: Risoluzione dei problemi dei criteri personalizzati| Microsoft Docs'
description: Informazioni sugli approcci per la risoluzione degli errori quando si lavora con i criteri personalizzati in Azure Active Directory.
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Risoluzione dei problemi del framework di esperienza di gestione delle identità e criteri personalizzati di Azure AD B2C

Se si usano i criteri personalizzati di Azure Active Directory B2C (Azure AD B2C), si potrebbero verificare problemi di impostazione del framework di esperienza di gestione delle identità nel relativo formato XML del linguaggio dei criteri.  Imparare a scrivere criteri personalizzati è come apprendere un nuovo linguaggio. Questo articolo descrive gli strumenti e suggerimenti che consentono di individuare e risolvere rapidamente i problemi. 

> [!NOTE]
> Questo articolo è incentrato sulla risoluzione dei problemi della configurazione dei criteri personalizzati di Azure AD B2C. Non tratta l'applicazione relying party o la relativa libreria di identità.

## <a name="xml-editing"></a>Modifica dell'XML

L'errore più comune nell'impostazione dei criteri è la formattazione XML non corretta. Un buon editor XML è essenziale. Un editor XML valido visualizza il codice XML in modo nativo, applica un colore specifico ai contenuti, precompila i termini comuni, mantiene indicizzati gli elementi XML e riesce a eseguire la convalida con lo schema. Seguono due editor XML tra i più validi:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

La convalida dello schema XML identifica gli errori prima del caricamento del file XML. Nella cartella radice dello starter pack ottenere la definizione dello schema XML TrustFrameworkPolicy_0.3.0.0.xsd. Per ulteriori informazioni, nella documentazione dell'editor XML cercare *strumenti XML* e *convalida XML*.

Una revisione delle regole XML può risultare utile. Azure AD B2C rifiuta qualsiasi errore di formattazione XML che rileva. Un codice XML non correttamente formattato può restituire a volte messaggi di errore fuorvianti.

## <a name="upload-policies-and-policy-validation"></a>Convalida dei criteri e del caricamento dei criteri

 La convalida del caricamento del file XML è automatica. La maggior parte degli errori causano errori del caricamento. La convalida include il file dei criteri che si sta caricando. Include anche la catena di file a cui si riferisce il file di caricamento (il file dei criteri relying party, il file delle estensioni e il file di base). 
 
 Gli errori di convalida più comuni sono i seguenti.

Frammento con errore: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* Il valore ClaimType potrebbe non essere scritto correttamente oppure non esiste nello schema.
* I valori ClaimType devono essere definiti in almeno uno dei file nei criteri. 
    Ad esempio: ` <ClaimType Id="socialIdpUserId">`
* Se ClaimType è definito nel file delle estensioni, ma è usato anche in un valore TechnicalProfile nel file di base, il caricamento del file di base restituirà un errore.

Frammento con errore: `...makes a reference to a ClaimsTransformation with id...`
* Le cause dell'errore potrebbero essere le stesse dell'errore ClaimType.

Frammento con errore: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Verificare che il valore TenantId negli elementi **\<TrustFrameworkPolicy\>**  e  **\<BasePolicy\>** corrisponda al tenant di Azure Active Directory B2C di destinazione.  

## <a name="troubleshoot-the-runtime"></a>Risoluzione dei problemi di runtime

* Usare `Run Now` e `https://jwt.io` per testare i criteri indipendentemente dall'applicazione Web o per dispositivi mobili. Questo sito Web funziona come un'applicazione relying party. Viene visualizzato il contenuto del token JSON Web (JWT) che viene generato per i criteri di Azure AD B2C. Per creare un'applicazione di test nel framework di esperienza di gestione delle identità usare i seguenti valori:
    * Nome: TestApp
    * App Web/API Web: no
    * Client nativo: no

* Per tracciare lo scambio di messaggi tra il browser client e Azure AD B2C usare [Fiddler](http://www.telerik.com/fiddler). Consente di ottenere un'indicazione del punto in cui il percorso utente genera errori nei passaggi di orchestrazione.

* In **Modalità sviluppo** usare **Application Insights** per tracciare l'attività del percorso utente nel framework di esperienza di gestione delle identità. In **Modalità sviluppo** è possibile osservare lo scambio di attestazioni tra il framework di esperienza di gestione delle identità e i diversi provider di attestazioni definiti dai profili tecnici, ad esempio provider di identità, servizi basati su API, la directory dell'utente di Azure AD B2C e altri servizi come Multi-Factor Authentication di Azure.  

## <a name="recommended-practices"></a>Procedure consigliate

**Mantenere più versioni degli scenari. Raggrupparli in un progetto con l'applicazione.** I file base, delle estensioni e relying party dipendono direttamente tra loro. Salvarli come gruppo. Quando vengono aggiunte nuove funzionalità ai criteri, mantenere versioni di lavoro separate. Inserire le versioni di lavoro nel file system con il codice dell'applicazione con cui interagiscono.  Le applicazioni potrebbero richiamare diversi criteri relying party in un tenant. Potrebbero diventare dipendenti dalle attestazioni previste dai criteri di Azure AD B2C.

**Sviluppare e testare i profili tecnici con percorsi utente noti.** Usare i criteri starter pack testati per configurare i profili tecnici. Testarli separatamente prima di includerli nel proprio percorso utente.

**Sviluppare e testare i percorsi utente con profili tecnici testati.** Modificare i passaggi di orchestrazione di un percorso utente in modo incrementale. Compilare progressivamente gli scenari previsti.

## <a name="next-steps"></a>Passaggi successivi

* In GitHub, scaricare il file zip [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip).

