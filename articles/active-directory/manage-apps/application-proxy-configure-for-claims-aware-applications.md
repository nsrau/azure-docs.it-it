---
title: App in grado di riconoscere attestazioni - Proxy di applicazione di Azure AD | Documentazione Microsoft
description: Come pubblicare applicazioni ASP.NET locali che accettano le attestazioni di AD FS per l'accesso remoto sicuro da parte degli utenti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c97729cf7d88ebdeefb44c83eb571bb6d7ebd0ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825588"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Uso di app in grado di riconoscere attestazioni nel proxy di applicazione
Le [app in grado di riconoscere attestazioni](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) eseguono un reindirizzamento al servizio token di sicurezza. Il servizio token di sicurezza richiede le credenziali all'utente in cambio di un token e quindi reindirizza l'utente all'applicazione. È possibile consentire al proxy di applicazione di usare questi reindirizzamenti in diversi modi. Usare questo articolo per configurare la distribuzione per app in grado di riconoscere attestazioni. 

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi che il servizio token di sicurezza cui viene reindirizzata l'app in grado di riconoscere attestazioni sia disponibile esternamente alla rete locale. Per rendere disponibile il servizio token di sicurezza, è possibile esporlo tramite un proxy o consentire le connessioni esterne. 

## <a name="publish-your-application"></a>Pubblicare l'applicazione

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](application-proxy-add-on-premises-application.md).
2. Passare alla pagina dell'applicazione nel portale e selezionare **Single Sign-On**.
3. Se si sceglie **Azure Active Directory** come **Metodo di autenticazione preliminare**, selezionare **Single Sign-On di Azure AD disabilitato** come **Metodo di autenticazione interna**. Se si sceglie **Pass-through** come **Metodo di autenticazione preliminare**, non è necessario apportare alcuna modifica.

## <a name="configure-adfs"></a>Configurare AD FS

È possibile configurare AD FS per le app in grado di riconoscere attestazioni in due modi diversi. Il primo consiste nell'usare domini personalizzati. Il secondo è con WS-Federation. 

### <a name="option-1-custom-domains"></a>Opzione 1: Domini personalizzati

Se tutti gli URL interni per le applicazioni sono nomi di dominio completi (FQDN), è possibile configurare [domini personalizzati](application-proxy-configure-custom-domain.md) per le applicazioni. Usare i domini personalizzati per creare URL esterni identici agli URL interni. Se gli URL esterni corrispondono agli URL interni, i reindirizzamenti del servizio token di sicurezza funzionano indipendentemente dal fatto che gli utenti siano in locale o in remoto. 

### <a name="option-2-ws-federation"></a>Opzione 2: WS-Federation

1. Aprire la console di gestione di AD FS.
2. Passare a **Attendibilità componente**, fare clic con il pulsante destro del mouse sull'app da pubblicare con il proxy dell'applicazione e quindi scegliere **Proprietà**.  

   ![Schermata: clic con il pulsante destro del mouse sul nome dell'app in Attendibilità componente](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Nella scheda **Endpoint** selezionare **WS-Federation** in **Tipo di endpoint**.
4. In **URL attendibile** specificare l'URL immesso in **URL esterno** nel proxy di applicazione e fare clic su **OK**.  

   ![Schermata: aggiunta di un endpoint e impostazione del valore per URL attendibile](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare Single Sign-On](configure-single-sign-on-portal.md) per le applicazioni che non sono in grado di riconoscere attestazioni
* [Abilitare le app client native per l'interazione con applicazioni proxy](application-proxy-configure-native-client-application.md)


