---
title: Authentication
description: Illustra il funzionamento dell'autenticazione
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195298"
---
# <a name="configure-authentication"></a>Configurare l'autenticazione

Il rendering remoto di Azure usa lo stesso meccanismo di autenticazione di [Azure Spatial Anchor (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). I client devono impostare *AccountKey*, *AuthenticationToken*o *AccessToken* per chiamare correttamente le API REST. È possibile ottenere *AccountKey* nella scheda "chiavi" per l'account di rendering remoto nel portale di Azure. *AuthenticationToken* è un token di Azure ad, che può essere ottenuto tramite la [libreria adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* è un token Mr, che può essere ottenuto dal servizio token di sicurezza di Azure Mixed Reality (STS).

## <a name="authentication-for-deployed-applications"></a>Autenticazione per le applicazioni distribuite

 L'uso delle chiavi dell'account è consigliato per il caricamento rapido, ma solo durante lo sviluppo e la creazione di prototipi. Si consiglia di non spedire l'applicazione alla produzione usando una chiave dell'account incorporata e di usare invece gli approcci di autenticazione Azure AD basati sull'utente o sul servizio.

 Azure AD autenticazione è descritta nella sezione [autenticazione utente Azure ad](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) del servizio [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesso a applicazioni, servizi o Azure AD utenti del servizio, sono stati creati i ruoli seguenti per l'assegnazione in base alle esigenze per gli account di rendering remoto di Azure:

* **Remote rendering Administrator**: fornisce agli utenti le funzionalità di conversione, gestione delle sessioni, rendering e diagnostica per il rendering remoto di Azure.
* **Client di rendering remoto**: consente agli utenti di gestire le funzionalità di sessione, rendering e diagnostica per il rendering remoto di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account](create-an-account.md)
* [Uso delle API front-end di Azure per l'autenticazione](frontend-apis.md)
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
