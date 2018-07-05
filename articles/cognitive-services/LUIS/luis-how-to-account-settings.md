---
title: Gestire le impostazioni dell'account in LUIS | Microsoft Docs
description: Usare il sito Web LUIS per gestire le impostazioni dell'account.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749964"
---
# <a name="manage-your-luis-account"></a>Gestire l'account Language Understanding
Le informazioni principali di un account LUIS sono l'account utente e la chiave di creazione. Le informazioni di accesso sono gestite in [account.microsoft.com](https://account.microsoft.com). La chiave di creazione viene gestita dalla pagina **Settings** (Impostazioni) del sito Web [LUIS][LUIS]. 

## <a name="authoring-key"></a>Chiave di creazione

La chiave di creazione specifica dell'area nella pagina **Settings** (Impostazioni) consente di creare tutte le app dal sito Web [LUIS][LUIS] e dalle [API di creazione](https://aka.ms/luis-authoring-api). Per praticità, la chiave di creazione è autorizzata a creare un numero [limitato](luis-boundaries.md) di query di endpoint al mese. 

![Pagina Settings (Impostazioni) di LUIS](./media/luis-how-to-account-settings/account-settings.png)

La chiave di creazione viene usata per le app di cui si è titolari e per le app di cui si è collaboratori.

## <a name="authoring-key-regions"></a>Aree delle chiavi di creazione
La chiave di creazione è specifica dell'[area di creazione](luis-reference-regions.md#publishing-regions). La chiave non può essere usata in un'area diversa. 

## <a name="reset-authoring-key"></a>Reimpostare la chiave di creazione
Se la chiave di creazione è danneggiata, reimpostare la chiave. La chiave viene reimpostata in tutte le app nel sito Web [LUIS]. Se le app vengono create mediante le API di creazione, è necessario modificare il valore di `Ocp-Apim-Subscription-Key` con la nuova chiave. 

## <a name="delete-account"></a>Eliminare l'account
Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati). 

## <a name="azure-active-directory-tenant-user"></a>Utente del tenant di Azure Active Directory
LUIS usa il flusso di consenso standard di Azure Active Directory (Azure AD). 

L'amministratore del tenant collabora direttamente con l'utente che necessita dell'accesso per poter usare LUIS in Azure AD. 

Innanzitutto, l'utente accede a LUIS e visualizza la finestra popup che richiede l'approvazione dell'amministratore. L'utente contatta l'amministratore del tenant prima di continuare. 

L'amministratore del tenant accede quindi a LUIS e visualizza una finestra popup del flusso di consenso. La finestra consente all'amministratore di concedere l'autorizzazione all'utente. Dopo che l'amministratore ha accettato l'autorizzazione, l'utente sarà in grado di continuare a usare LUIS.

Se non accede a LUIS, l'amministratore del tenant può accedere al [consenso](https://account.activedirectory.windowsazure.com/r#/applications) per LUIS. 

![Autorizzazione di Azure Active Directory dal sito Web dell'app](./media/luis-how-to-account-settings/tenant-permissions.png)

Se l'amministratore del tenant vuole che solo determinati utenti usino LUIS, vedere il [blog sulle identità](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Account utente con più indirizzi di posta elettronica per i collaboratori
Se si aggiungono collaboratori a un'app LUIS, si specifica l'indirizzo di posta elettronica necessario al collaboratore per usare LUIS come collaboratore. Sebbene Azure Active Directory (Azure AD) consenta a un singolo utente di avere più account di posta elettronica usati in modo intercambiabile, LUIS richiede che l'utente esegua l'accesso con l'indirizzo di posta elettronica specificato nell'elenco del collaboratore. 


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [chiave di creazione](luis-concept-keys.md#authoring-key). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
