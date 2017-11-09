---
title: Problema nella configurazione dell'accesso Single Sign-On federato per un'applicazione non inclusa nella raccolta di Azure AD | Microsoft Docs
description: Informazioni sui problemi comuni che si possono incontrare durante la configurazione dell'accesso Single Sign-On federato per un'applicazione SAML personalizzata non inclusa nella raccolta delle applicazioni di Azure AD
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
ms.openlocfilehash: d8fb9c71ede7558d12ce3e22dcd359ed82527e43
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problema nella configurazione dell'accesso Single Sign-On federato per un'applicazione non inclusa nella raccolta di Azure AD

Se si verifica un problema durante la configurazione di un'applicazione. Verificare di avere seguito tutti i passaggi indicati nell'articolo [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery).

## <a name="cant-add-another-instance-of-the-application"></a>Impossibile aggiungere un'altra istanza dell'applicazione

Per aggiungere una seconda istanza di un'applicazione, è necessario:

-   Configurare un identificatore univoco per la seconda istanza. Non è possibile configurare lo stesso identificatore usato per la prima istanza.

-   Configurare un certificato diverso da quello usato per la prima istanza.

Se l'applicazione non supporta alcuna delle operazioni sopra indicate, non è possibile configurare una seconda istanza.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Dove impostare il formato di EntityID (Identificatore utente)

Non è possibile selezionare il formato di EntityID (identificatore utente) che Azure AD invia all'applicazione in risposta all'autenticazione dell'utente.

Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per altre informazioni, vedere l'articolo relativo al [protocollo SAML per Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sotto la sezione NameIDPolicy.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Dove scaricare il certificato o i metadati dell'applicazione da Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Informazioni sulla personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)
