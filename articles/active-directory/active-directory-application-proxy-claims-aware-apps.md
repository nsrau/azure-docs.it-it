---
title: App in grado di riconoscere attestazioni - Proxy di applicazione di Azure AD | Documentazione Microsoft
description: Come pubblicare applicazioni ASP.NET locali che accettano le attestazioni di AD FS per l'accesso remoto sicuro da parte degli utenti.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 5784222608b01509fc4ff84b1a8792cbcfea89e6
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Uso di app in grado di riconoscere attestazioni nel proxy di applicazione
Le [app in grado di riconoscere attestazioni](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) eseguono un reindirizzamento al servizio token di sicurezza. Il servizio token di sicurezza richiede le credenziali all'utente in cambio di un token e quindi reindirizza l'utente all'applicazione. È possibile consentire al proxy di applicazione di usare questi reindirizzamenti in diversi modi. Usare questo articolo per configurare la distribuzione per app in grado di riconoscere attestazioni. 

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi che il servizio token di sicurezza cui viene reindirizzata l'app in grado di riconoscere attestazioni sia disponibile esternamente alla rete locale. Per rendere disponibile il servizio token di sicurezza, è possibile esporlo tramite un proxy o consentire le connessioni esterne. 

## <a name="publish-your-application"></a>Pubblicare l'applicazione

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](application-proxy-publish-azure-portal.md).
2. Passare alla pagina dell'applicazione nel portale e selezionare **Single Sign-On**.
3. Se si sceglie **Azure Active Directory** come **Metodo di autenticazione preliminare**, selezionare **Single Sign-On di Azure AD disabilitato** come **Metodo di autenticazione interna**. Se si sceglie **Pass-through** come **Metodo di autenticazione preliminare**, non è necessario apportare alcuna modifica.

## <a name="configure-adfs"></a>Configurare AD FS

È possibile configurare AD FS per le app in grado di riconoscere attestazioni in due modi diversi. Il primo consiste nell'usare domini personalizzati. Il secondo è con WS-Federation. 

### <a name="option-1-custom-domains"></a>Opzione 1: domini personalizzati

Se tutti gli URL interni per le applicazioni sono nomi di dominio completi (FQDN), è possibile configurare [domini personalizzati](active-directory-application-proxy-custom-domains.md) per le applicazioni. Usare i domini personalizzati per creare URL esterni identici agli URL interni. Se gli URL esterni corrispondono agli URL interni, i reindirizzamenti del servizio token di sicurezza funzionano indipendentemente dal fatto che gli utenti siano in locale o in remoto. 

### <a name="option-2-ws-federation"></a>Opzione 2: WS-Federation

1. Aprire la console di gestione di AD FS.
2. Passare a **Attendibilità componente**, fare clic con il pulsante destro del mouse sull'app da pubblicare con il proxy dell'applicazione e quindi scegliere **Proprietà**.  

   ![Schermata: clic con il pulsante destro del mouse sul nome dell'app in Attendibilità componente](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Nella scheda **Endpoint** selezionare **WS-Federation** in **Tipo di endpoint**.
4. In **URL attendibile** specificare l'URL immesso in **URL esterno** nel proxy di applicazione e fare clic su **OK**.  

   ![Schermata: aggiunta di un endpoint e impostazione del valore per URL attendibile](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare Single Sign-On](application-proxy-sso-overview.md) per le applicazioni che non sono in grado di riconoscere attestazioni
* [Abilitare le app client native per l'interazione con applicazioni proxy](active-directory-application-proxy-native-client.md)



