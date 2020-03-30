---
title: Gestione di certificati di federazione in Azure AD | Documentazione Microsoft
description: Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente.
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
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159030"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory

In questo articolo vengono trattate le domande comuni e le informazioni relative ai certificati creati da Azure Active Directory (Azure AD) per stabilire l'accesso Single Sign-On (SSO) federato alle applicazioni SSO (Client Sign-On) federate per il software come servizio (SaaS). Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. Configurare l'applicazione utilizzando l'opzione di SSO federato.

Questo articolo è rilevante solo per le app configurate per l'uso di Azure AD SSO tramite la federazione SAML [(Security Assertion Markup Language).](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta

Quando si aggiunge una nuova applicazione dalla raccolta e si configura un servizio Di accesso basato su SAML (selezionando**SAML** **Single Sign-On** > dalla pagina di panoramica dell'applicazione), Azure AD genera un certificato per l'applicazione valido per tre anni. Per scaricare il certificato attivo come file di certificato di sicurezza (**con estensione cer),** tornare a tale pagina **(accesso basato su SAML)** e selezionare un collegamento di download nell'intestazione Certificato di **firma SAML.** È possibile scegliere tra il certificato non elaborato (binario) o il certificato Base64 (testo con codifica base 64). Per le applicazioni di raccolta, questa sezione potrebbe anche mostrare un collegamento per scaricare il certificato come XML dei metadati federativi (un file **XML),** a seconda dei requisiti dell'applicazione.

![Opzioni di download del certificato di firma attivo SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

È inoltre possibile scaricare un certificato attivo o inattivo selezionando l'icona Modifica dell'intestazione **Certificato** di **firma SAML** (una matita), che visualizza la pagina Certificato di firma **SAML.** Selezionare i puntiri di sospensione (**...**) accanto al certificato che si desidera scaricare e quindi scegliere il formato di certificato desiderato. È possibile scaricare il certificato in formato PEM (Privacy-Enhanced Mail). Questo formato è identico a Base64 ma con estensione **pem,** che non è riconosciuto in Windows come formato del certificato.

![Opzioni di download del certificato di firma SAML (attivo e inattivo)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover di un nuovo certificato

Per impostazione predefinita, Azure configura un certificato in modo che scada dopo tre anni quando viene creato automaticamente durante la configurazione del single sign-on SAML. Poiché non è possibile modificare la data di un certificato dopo averlo salvato, è necessario:Because you can't change the date of a certificate after you save it, you have to:

1. Creare un nuovo certificato con la data desiderata.
1. Salvare il nuovo certificato.
1. Scaricare il nuovo certificato nel formato corretto.
1. Caricare il nuovo certificato nell'applicazione.
1. Rendere attivo il nuovo certificato nel portale di Azure Active Directory.Make the new certificate active in the Azure Active Directory portal.

Le due sezioni seguenti consentono di eseguire questi passaggi.

### <a name="create-a-new-certificate"></a>Creare un nuovo certificatoCreate a new certificate

Creare e salvare un nuovo certificato con una data di scadenza diversa:First, create and save new certificate with a different expiration date:

1. Accedere al [portale](https://aad.portal.azure.com/)di Azure Active Directory . Viene visualizzata la pagina **dell'interfaccia** di amministrazione di Azure Active Directory.The Azure Active Directory admin center page appears.
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nel tuo account.
1. Selezionare l'applicazione interessata. Viene visualizzata una pagina di panoramica per l'applicazione.
1. Nel riquadro sinistro della pagina di panoramica dell'applicazione selezionare **Single Sign-On**.
1. Se viene visualizzata la pagina Selezionare un metodo Single **Sign-On,** selezionare **SAML**.
1. Nella pagina **Configura Single Sign-On con SAML - Anteprima,** trova l'intestazione Certificato di **firma SAML** e seleziona l'icona **Modifica** (una matita). Viene visualizzata la pagina Certificato di **firma SAML,** in cui vengono visualizzati lo stato (**Attivo** o **Inattivo**), la data di scadenza e l'identificazione personale (una stringa hash) di ogni certificato.
1. Selezionare **Nuovo certificato**. Una nuova riga viene visualizzata sotto l'elenco dei certificati, in cui la data di scadenza è esattamente tre anni dopo la data corrente. Le modifiche non sono ancora state salvate, pertanto è comunque possibile modificare la data di scadenza.
1. Nella nuova riga del certificato, passa il mouse sopra la colonna della data di scadenza e seleziona l'icona **Seleziona data** (un calendario). Viene visualizzato un controllo calendario che visualizza i giorni di un mese della data di scadenza corrente della nuova riga.
1. Utilizzare il controllo calendario per impostare una nuova data. È possibile impostare qualsiasi data tra la data corrente e tre anni dopo la data corrente.
1. Selezionare **Salva**. Il nuovo certificato viene ora visualizzato con lo stato **Inattivo**, la data di scadenza scelta e un'identificazione personale.
1. Selezionare la **X** per tornare alla pagina **Configura Single Sign-On con SAML - Anteprima.**

### <a name="upload-and-activate-a-certificate"></a>Caricare e attivare un certificato

Successivamente, scaricare il nuovo certificato nel formato corretto, caricarlo nell'applicazione e renderlo attivo in Azure Active Directory:Next, download the new certificate in the correct format, upload it to the application, and make it active in Azure Active Directory:

1. Visualizzare le istruzioni di configurazione SAML aggiuntive dell'applicazione:

   - Selezione del collegamento alla guida di **configurazione** da visualizzare in una finestra o scheda separata del browser oppure
   - Andare all'intestazione di **configurazione** e selezionare **Visualizza istruzioni dettagliate** per visualizzare in una barra laterale.

1. Nelle istruzioni prendere nota del formato di codifica richiesto per il caricamento del certificato.
1. Seguire le istruzioni nella sezione Certificato generato automaticamente per le [applicazioni della raccolta e delle applicazioni non della raccolta](#auto-generated-certificate-for-gallery-and-non-gallery-applications) in precedenza. Questo passaggio consente di scaricare il certificato nel formato di codifica richiesto per il caricamento da parte dell'applicazione.
1. Quando si desidera eseguire il rollover al nuovo certificato, tornare alla pagina Certificato di **firma SAML** e nella riga del certificato appena salvato selezionare i coni trattini (**...**) e selezionare Rendi il **certificato attivo**. Lo stato del nuovo certificato diventa **Attivo**e il certificato precedentemente attivo diventa **Inattivo**.
1. Continuare a seguire le istruzioni di configurazione SAML dell'applicazione visualizzate in precedenza, in modo da poter caricare il certificato di firma SAML nel formato di codifica corretto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Aggiungere indirizzi di notifica tramite posta elettronica per la scadenza del certificatoAdd email notification addresses for certificate expiration

Azure AD invierà una notifica tramite posta elettronica 60, 30 e 7 giorni prima della scadenza del certificato SAML. Puoi aggiungere più di un indirizzo email per ricevere le notifiche. Per specificare gli indirizzi e-mail a cui inviare le notifiche:

1. Nella pagina **Certificato di firma SAML** passare all'intestazione degli indirizzi di posta elettronica di **notifica.** Per impostazione predefinita, questa intestazione utilizza solo l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione.
1. Sotto l'indirizzo di posta elettronica finale digitare l'indirizzo di posta elettronica che deve ricevere l'avviso di scadenza del certificato e quindi premere INVIO.
1. Ripetere il passaggio precedente per ogni indirizzo di posta elettronica che si desidera aggiungere.
1. Per ogni indirizzo e-mail che desideri eliminare, seleziona l'icona **Elimina** (un bidone nel garbage) accanto all'indirizzo e-mail.
1. Selezionare **Salva**.

Si riceverà la notifica tramite posta elettronica da aadnotification@microsoft.com. Per evitare che l'e-mail vada nella tua posizione di spam, aggiungi questa email ai tuoi contatti.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Rinnovare un certificato con scadenza imminente

Se un certificato sta per scadere, è possibile rinnovarlo utilizzando una procedura che non comporta tempi di inattività significativi per gli utenti. Per rinnovare un certificato in scadenza:

1. Seguire le istruzioni nella sezione [Creare un nuovo certificato](#create-a-new-certificate) in precedenza, utilizzando una data che si sovrappone al certificato esistente. Tale data limita la quantità di tempo di inattività causata dalla scadenza del certificato.
1. Se l'applicazione può eseguire automaticamente il rollover di un certificato, impostare il nuovo certificato su attivo attenendosi alla seguente procedura:
   1. Tornare alla pagina **Certificato di firma SAML.**
   1. Nella riga del certificato appena salvata selezionare i conivizio (**...**) e quindi selezionare **Rendi attivo il certificato**.
   1. Saltare i due passaggi successivi.

1. Se l'app è in grado di gestire un solo certificato alla volta, selezionare un intervallo di inattività per eseguire il passaggio successivo. In caso contrario, se l'applicazione non rileva automaticamente il nuovo certificato ma è in grado di gestire più certificati di firma, è possibile eseguire il passaggio successivo in qualsiasi momento.
1. Prima della scadenza del certificato precedente, seguire le istruzioni nella sezione [Caricare e attivare un certificato](#upload-and-activate-a-certificate) in precedenza.
1. Accedere all'applicazione per assicurarsi che il certificato funzioni correttamente.

## <a name="related-articles"></a>Articoli correlati

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Debug SAML-based single sign-on to applications in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
