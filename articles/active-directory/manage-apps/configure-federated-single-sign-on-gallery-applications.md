---
title: Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD | Microsoft Docs
description: Come configurare Single Sign-On federati per un'applicazione Azure AD Gallery esistente e come usare le esercitazioni per iniziare rapidamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207125"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD

È disponibile un'esercitazione dettagliata per tutte le applicazioni nella raccolta Azure Active Directory (Azure AD) con funzionalità di Single Sign-On aziendali. Per le istruzioni dettagliate, è possibile accedere all'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="overview-of-steps-required"></a>Panoramica dei passaggi necessari
Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperare i metadati e il certificato di Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurare i valori dei metadati Azure AD nell'applicazione (URL di accesso, autorità emittente, URL di disconnessione e certificato)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Assegnare utenti all'applicazione](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il [portale di Azure](https://portal.azure.com) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "Azure Active Directory" nella casella di ricerca e selezionare **Azure Active Directory**.

4.  Selezionare **applicazioni aziendali** dal menu di spostamento a sinistra Azure ad.

5.  Selezionare **Aggiungi** nell'angolo in alto a destra nel riquadro **applicazioni aziendali** .

6.  Nella casella **immettere un nome** nella sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione.

7.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome nella casella **nome** .

9.  Selezionare **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, dovrebbe essere possibile visualizzare il riquadro di configurazione dell'applicazione.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurare l'accesso Single Sign-On per un'applicazione della raccolta di Azure AD

Per configurare l'accesso Single Sign-On per un'applicazione, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "Azure Active Directory" nella casella di ricerca e selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal menu di spostamento a sinistra Azure Active Directory.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7. Al termine del caricamento dell'applicazione, selezionare **Single Sign-on** nel menu di navigazione a sinistra dell'applicazione.

8. Selezionare **Accesso basato su SAML** dal menu a discesa **Modalità**.

9. Immettere i valori necessari in **URL e dominio**. È necessario ottenere questi valori dal fornitore dell'applicazione.

   1. Per configurare l'applicazione come SSO avviato da provider di servizi, l'URL di accesso è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

   2. Per configurare l'applicazione come SSO avviato da IdP, l'URL di risposta è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

10. **Facoltativo**: Selezionare **Mostra impostazioni URL avanzate** se si desidera visualizzare i valori non richiesti.

11. In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

12. **Facoltativo**: Selezionare **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi da inviare all'applicazione nel token SAML quando l'utente esegue l'accesso.

    Per aggiungere un attributo:
   
    1. Selezionare **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

    1. Selezionare **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

13. Selezionare **Configura &lt;nome&gt; applicazione** per accedere alla documentazione su come configurare Single Sign-on nell'applicazione. Sono inoltre disponibili il certificato e gli URL dei metadati necessari per configurare l'accesso SSO con l'applicazione.

14. Selezionare **Salva** per salvare la configurazione.

15. Assegnare gli utenti all'applicazione.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione

Per selezionare l'identificatore utente o aggiungere gli attributi utente, attenersi alla seguente procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale** o coamministratore **.**

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "Azure Active Directory" nella casella di ricerca e selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal menu di spostamento a sinistra Azure Active Directory.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare l'applicazione configurata con Single Sign-On.

7. Al termine del caricamento dell'applicazione, selezionare **Single Sign-on** nel menu di navigazione a sinistra dell'applicazione.

8. Nella sezione **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**. L'opzione selezionata deve corrispondere al valore previsto nell'applicazione per autenticare l'utente.

   >[!NOTE] 
   >Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione in oggetto authrequest SAML. Per ulteriori informazioni, vedere il [protocollo SAML Single Sign-on](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) nella sezione NameIDPolicy.
   >
   >

9. Per aggiungere gli attributi utente, selezionare **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi da inviare all'applicazione nel token SAML quando l'utente esegue l'accesso.

   Per aggiungere un attributo:
  
   1. Selezionare **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   2. Selezionare **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Scaricare il certificato o i metadati di Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale** o coamministratore **.**

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "Azure Active Directory" nella casella di ricerca e selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal menu di spostamento a sinistra Azure Active Directory.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   *  Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni**.

6. Selezionare l'applicazione configurata con Single Sign-On.

7. Al termine del caricamento dell'applicazione, selezionare **Single Sign-on** nel menu di navigazione a sinistra dell'applicazione.

8. Passare alla sezione **certificato di firma SAML** , quindi selezionare **Scarica** valore colonna. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD fornisce inoltre un URL per accedere ai metadati. Usare il modello seguente per ottenere l'URL dei metadati specifico dell'applicazione:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Assegnare utenti all'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "Azure Active Directory" nella casella di ricerca e selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal menu di spostamento a sinistra Azure Active Directory.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Al termine del caricamento dell'applicazione, selezionare **utenti e gruppi** dal menu di navigazione a sinistra dell'applicazione.

8. Selezionare il pulsante **Aggiungi** nella parte superiore dell'elenco **utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione** .

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Digitare il **nome completo** o l' **indirizzo di posta elettronica** dell'utente che si desidera assegnare alla casella di ricerca **Cerca per nome o indirizzo di posta elettronica** .

11. Passare il puntatore del mouse sull' **utente** nell'elenco per visualizzare una **casella di controllo**. Selezionare la casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **selezionato** .

12. **Facoltativo**: Se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella Cerca per **nome o indirizzo di posta elettronica** e selezionare la casella di controllo per aggiungere l'utente all'elenco **selezionato** .

13. Al termine della selezione degli utenti, selezionare il pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo**: Selezionare il selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per selezionare un ruolo da assegnare agli utenti selezionati.

15. Selezionare il pulsante **assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [mapping delle attestazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)



