---
title: Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni| Documentazione Microsoft
description: Illustra come gli utenti possono partecipare ad Azure AD tramite il menu Impostazioni.
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: b844e1d9-ad43-4e4a-a398-5c4a43bf2f5c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70610ec2c5a056d63ed854b33d84e29951940c2f


---
# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni
Se si sta usando Windows 7 o Windows 8 e il computer o il dispositivo è stato aggiornato a Windows 10, è possibile eseguire le aggiunte ad Azure Active Directory (Azure AD) tramite il menu Impostazioni.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Per eseguire le aggiunte ad Azure AD dal menu Impostazioni
1. Dal menu **Start** fare clic sul charm **Impostazioni**.
2. Da **Impostazioni** selezionare **Sistema**->**Informazioni**->**Aggiungi ad Azure AD**.
   
   <center>
   ![Aggiungi ad Azure AD dal menu Impostazioni](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>
3. Fare clic su **Continua** nella finestra del messaggio Aggiungi ad Azure AD.
   
   <center>
   ![Finestra di messaggio Aggiungi ad Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Fornire le credenziali di accesso. Questa esperienza di accesso includerà tutti i passaggi necessari per completare l'autenticazione. Se si fa parte di un tenant federato, l'amministratore fornirà l’esperienza di federazione ospitata dall'organizzazione.
   <center>
   ![Fornire le credenziali di accesso](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se l'organizzazione ha configurato Azure Multi-Factor Authentication per l'aggiunta ad Azure AD, fornire il secondo fattore prima di continuare.
6. Fare clic su **Accetta** nella schermata **Consenti la gestione di questo dispositivo**.
7. Verrà visualizzato il messaggio "Il dispositivo risulta ora aggiunto all'organizzazione in Azure AD".

## <a name="additional-information"></a>Informazioni aggiuntive
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)




<!--HONumber=Dec16_HO4-->


