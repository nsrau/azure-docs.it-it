---
title: App in grado di riconoscere attestazioni - Proxy di applicazione di Azure AD | Documentazione Microsoft
description: Come pubblicare applicazioni ASP.NET locali che accettano le attestazioni di AD FS per l&quot;accesso remoto sicuro da parte degli utenti.
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
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: ff07a52f6a503f07f5919b63f345878571742cac


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Uso di app in grado di riconoscere attestazioni nel proxy dell'applicazione
Le app in grado di riconoscere attestazioni eseguono un reindirizzamento al servizio token di sicurezza, che a sua volta richiede le credenziali dell'utente in cambio di un token prima di reindirizzare l'utente all'applicazione. Per abilitare il proxy dell'applicazione e poter gestire le operazioni di reindirizzamento, è necessario prima seguire questa procedura.

## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire la procedura, assicurarsi che il servizio token di sicurezza a cui reindirizza l'app basata sul riconoscimento delle attestazioni sia disponibile all'esterno della rete locale.

## <a name="azure-classic-portal-configuration"></a>Configurazione del portale di Azure classico
1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md).
2. Nell'elenco delle applicazioni selezionare l'app in grado di riconoscere attestazioni e fare clic su **Configura**.
3. Se si sceglie **Passthrough** come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **HTTPS** come schema di **URL esterno**.
4. Se si sceglie **Azure Active Directory** come **Metodo di autenticazione preliminare**, assicurarsi di selezionare **Nessuno** come **Metodo di autenticazione interna**.

## <a name="adfs-configuration"></a>Configurazione di AD FS
1. Aprire la console di gestione di AD FS.
2. Passare a **Attendibilità componente**, fare clic con il pulsante destro del mouse sull'app da pubblicare con il proxy dell'applicazione e quindi scegliere **Proprietà**.  

   ![Schermata: clic con il pulsante destro del mouse sul nome dell'app in Attendibilità componente](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Nella scheda **Endpoint** selezionare **WS-Federation** in **Tipo di endpoint**.
4. In **URL attendibile** specificare l'URL immesso in **URL esterno** nel proxy dell'applicazione e fare clic su **OK**.  

   ![Schermata: aggiunta di un endpoint e impostazione del valore per URL attendibile](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)
* [Abilitare le app client native per l'interazione con applicazioni proxy](active-directory-application-proxy-native-client.md)





<!--HONumber=Feb17_HO1-->


