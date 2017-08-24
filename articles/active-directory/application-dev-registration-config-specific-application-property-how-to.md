---
title: Come compilare campi specifici per un'applicazione personalizzata | Microsoft Docs
description: Materiale sussidiario su come compilare campi specifici quando si registra un'applicazione personalizzata con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9a57185938ebe9af0ca1395e75545473f45b9889
ms.contentlocale: it-it
ms.lasthandoff: 04/17/2017

---

# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Come compilare campi specifici per un'applicazione personalizzata

Questo articolo include una breve descrizione di tutti i campi disponibili nel modulo di registrazione dell'applicazione del [Portale di Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

-   Per registrare una nuova applicazione, passare al [Portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Scegliere **Registrazioni per l'app** e fare clic su **Aggiungi**.

-   Si apre il modulo di registrazione dell'applicazione.

## <a name="fields-in-the-application-registration-form"></a>Campi nel modulo di registrazione dell'applicazione


| Campo            | Descrizione                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | Il nome dell'applicazione. Deve essere minimo di quattro caratteri.                |
| Tipo di applicazione | **App Web/API Web**: un'applicazione Web, un'API Web o entrambe 
| |**Nativa**: un'applicazione che può essere installata in un computer o dispositivo utente           |
| URL di accesso      | L'URL a cui possono accedere gli utenti per usare l'applicazione                                  |

Dopo aver compilato i campi sopra elencati, registrare l'applicazione nel Portale di Azure. Verrà visualizzata la pagina dell'applicazione. Il pulsante **Impostazioni** del riquadro dell'applicazione apre la pagina Impostazioni, che include più campi per personalizzare l'applicazione. La tabella seguente illustra tutti i campi nella pagina Impostazioni. Si noti che viene visualizzato solo un sottoinsieme di questi campi, a seconda se è stata creata un'applicazione Web o un'applicazione nativa.

| Campo           | Descrizione                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID applicazione  | Quando si registra un'applicazione, Azure AD assegna un ID all'applicazione. L'ID dell'applicazione può essere usato per identificare in modo univoco l'applicazione nelle richieste di autenticazione ad Azure AD, nonché per accedere a risorse, ad esempio l'API Graph.                                                          |
| URI dell'ID dell'app      | Deve essere un URI univoco, in genere nel formato **https://&lt;nome\_tenant&gt;/&lt;nome\_applicazione&gt;.** L'URI viene usato durante il flusso di concessione delle autorizzazioni, come identificatore univoco per specificare la risorsa per cui deve essere emesso il token. Diventa anche l'attestazione "aud" nel token di accesso emesso. |
| Carica nuovo logo | Questo campo consente di caricare un logo per l'applicazione. Il logo deve essere in formato bmp, jpg o png e la dimensione del file deve essere inferiore a 100 KB. Le dimensioni dell'immagine devono essere 215 x 215 pixel, con le dimensioni dell'immagine centrale di 94 x 94 pixel.                                                       |
| URL della home page   | Questo è l'URL di accesso specificato durante la registrazione dell'applicazione.                                                                                                                                                                                                                                              |
| URL di chiusura sessione      | Questo è l'URL del punto di uscita singolo. Azure AD invia una richiesta di disconnessione a questo URL quando l'utente cancella la sessione di Azure AD usando qualsiasi altra applicazione registrata.                                                                                                                                       |
| Multi-tenant  | Questa opzione specifica se l'applicazione può essere usata da più tenant. In genere, ciò significa che le organizzazioni esterne possono usare l'applicazione registrandola nel proprio tenant e concedendo accesso ai dati dell'organizzazione.                                                                   |
| URL di risposta      | Gli URL di risposta sono gli endpoint a cui Azure AD restituisce tutti i token richiesti dall'applicazione.                                                                                                                                                                                                          |
| URI di reindirizzamento   | Per le applicazioni native, questo è il punto in cui l'utente deve essere inviato in seguito a un'autorizzazione riuscita. Azure AD verifica che l'URI di reindirizzamento specificato dall'applicazione nella richiesta OAuth 2.0 corrisponda a uno dei valori registrati nel portale.                                                            |
| Chiavi            | È possibile creare chiavi per accedere a livello di codice ad API Web protette da Azure AD senza alcuna interazione dell'utente. Nella pagina \*\*Chiavi\*\* immettere una descrizione e una data di scadenza della chiave e fare clic su Salva per generare la chiave. Assicurarsi di salvarla in un luogo sicuro, poiché non sarà possibile accedervi successivamente.             |

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

