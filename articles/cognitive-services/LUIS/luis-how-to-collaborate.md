---
title: Collaborare con altri collaboratori in app LUIS
titleSuffix: Azure Cognitive Services
description: Il proprietario di un'app può aggiungere collaboratori alla stessa. I collaboratori possono modificare il modello, eseguire il training e pubblicare l'app.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b7d108bfc0c4283e7856b93daba3f4f92af4cc5b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042194"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Come gestire autori e collaboratori 

Il proprietario di un'app può aggiungere collaboratori alla stessa. I collaboratori possono modificare il modello, eseguire il training e pubblicare l'app. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Aggiungere un collaboratore

Un'app ha un unico autore, il proprietario, ma può avere molti collaboratori. Per consentire ai collaboratori di modificare l'app LUIS, alll'elenco di collaboratori è necessario aggiungere l'indirizzo di posta elettronica che useranno per accedere al portale di LUIS. Una volta aggiunta, l'app appare nel loro portale di LUIS.

1. Selezionare **Gestisci** dal menu in alto a destra, quindi selezionare **Collaboratori** nel menu a sinistra.

2. Selezionare **Aggiungi collaboratore** nella barra degli strumenti.

    [![](./media/luis-how-to-collaborate/add-collaborator.png "Aggiungi collaboratore")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Immettere l'indirizzo di posta elettronica che il collaboratore userà per accedere al portale di LUIS.

    ![Aggiungere l'indirizzo di posta elettronica del collaboratore](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Trasferire la proprietà

LUIS attualmente non supporta il trasferimento della proprietà, ma è possibile esportare l'app e un altro utente LUIS può importarla. Potrebbero essere presenti differenze nei punteggi LUIS tra le due applicazioni. 

## <a name="azure-active-directory-resources"></a>Risorse Azure Active Directory

Se all'interno della propria organizzazione si usa Azure Active Directory (Azure AD), LUIS richiede l'autorizzazione per accedere alle informazioni relative agli utenti quando desiderano usare LUIS. Le risorse richieste da LUIS sono minime. 

La descrizione dettagliata viene visualizzata quando si tenta di effettuare l'iscrizione con un account che ha ottenuto il consenso da parte dell'amministratore o non richiede il consenso dello stesso, ad esempio il consenso amministratore:

* Consente di accedere all'app con l'account dell'organizzazione e consente all'app di leggere il profilo personale. Consente inoltre all'app di leggere le informazioni aziendali di base.
* Consente all'app di visualizzare e aggiornare i dati, anche quando non la si sta usando.

La prima autorizzazione permette a LUIS di leggere i dati di profilo di base, come ad esempio l'ID utente, l'indirizzo di posta elettronica e il nome. La seconda autorizzazione è obbligatoria per aggiornare il token di accesso dell'utente.

## <a name="azure-active-directory-tenant-user"></a>Utente del tenant di Azure Active Directory

LUIS usa il flusso di consenso standard di Azure Active Directory (Azure AD). 

L'amministratore del tenant collabora direttamente con l'utente che necessita dell'accesso per poter usare LUIS in Azure AD. 

Innanzitutto, l'utente accede a LUIS e visualizza la finestra popup che richiede l'approvazione dell'amministratore. L'utente contatta l'amministratore del tenant prima di continuare. 

L'amministratore del tenant accede quindi a LUIS e visualizza una finestra popup del flusso di consenso. La finestra consente all'amministratore di concedere l'autorizzazione all'utente. Dopo che l'amministratore ha accettato l'autorizzazione, l'utente sarà in grado di continuare a usare LUIS.

Se non accede a LUIS, l'amministratore del tenant può accedere al [consenso](https://account.activedirectory.windowsazure.com/r#/applications) per LUIS. 

![Autorizzazione di Azure Active Directory dal sito Web dell'app](./media/luis-how-to-collaborate/tenant-permissions.png)

Se l'amministratore del tenant vuole che solo determinati utenti usino LUIS, vedere il [blog sulle identità](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Account utente con più indirizzi di posta elettronica per i collaboratori

Se si aggiungono collaboratori a un'app LUIS, si specifica l'indirizzo di posta elettronica necessario al collaboratore per usare LUIS come collaboratore. Anche se Azure Active Directory (Azure AD) permette a un singolo utente di avere più account di posta elettronica usati in modo intercambiabile, LUIS richiede che l'utente esegua l'accesso con l'indirizzo di posta elettronica specificato nell'elenco del collaboratore.

