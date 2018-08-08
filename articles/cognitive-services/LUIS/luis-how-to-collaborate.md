---
title: Collaborare con altri alle app LUIS in Azure | Microsoft Docs
description: Informazioni su come collaborare con altri alle applicazioni Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397789"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Come gestire autori e collaboratori 

È possibile collaborare con altri a un'app LUIS. 

## <a name="owner-and-collaborators"></a>Proprietario e collaboratori

Un'app ha un unico autore, il proprietario, ma può avere molti collaboratori. 

## <a name="add-collaborator"></a>Aggiungere un collaboratore

Per consentire ai collaboratori di modificare l'app LUIS nella pagina **Impostazioni** dell'app LUIS digitare l'indirizzo e-mail del collaboratore e fare clic su **Add collaborator** (Aggiungi collaboratore). I collaboratori possano accedere all'app LUIS e modificarla contemporaneamente al creatore.

![Aggiungere un collaboratore](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>Trasferire la proprietà

LUIS attualmente non supporta il trasferimento della proprietà, ma è possibile esportare l'app e un altro utente LUIS può importarla. Potrebbero essere presenti differenze nei punteggi LUIS tra le due applicazioni. 

## <a name="azure-active-directory-tenant-user"></a>Utente del tenant di Azure Active Directory

LUIS usa il flusso di consenso standard di Azure Active Directory (Azure AD). 

L'amministratore del tenant collabora direttamente con l'utente che necessita dell'accesso per poter usare LUIS in Azure AD. 

Innanzitutto, l'utente accede a LUIS e visualizza la finestra popup che richiede l'approvazione dell'amministratore. L'utente contatta l'amministratore del tenant prima di continuare. 

L'amministratore del tenant accede quindi a LUIS e visualizza una finestra popup del flusso di consenso. La finestra consente all'amministratore di concedere l'autorizzazione all'utente. Dopo che l'amministratore ha accettato l'autorizzazione, l'utente sarà in grado di continuare a usare LUIS.

Se non accede a LUIS, l'amministratore del tenant può accedere al [consenso](https://account.activedirectory.windowsazure.com/r#/applications) per LUIS. 

![Autorizzazione di Azure Active Directory dal sito Web dell'app](./media/luis-how-to-account-settings/tenant-permissions.png)

Se l'amministratore del tenant vuole che solo determinati utenti usino LUIS, vedere il [blog sulle identità](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Account utente con più indirizzi di posta elettronica per i collaboratori

Se si aggiungono collaboratori a un'app LUIS, si specifica l'indirizzo di posta elettronica necessario al collaboratore per usare LUIS come collaboratore. Anche se Azure Active Directory (Azure AD) permette a un singolo utente di avere più account di posta elettronica usati in modo intercambiabile, LUIS richiede che l'utente esegua l'accesso con l'indirizzo di posta elettronica specificato nell'elenco del collaboratore.