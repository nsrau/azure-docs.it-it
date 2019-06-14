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
ms.openlocfilehash: ecadb499d140ccfc993820080cae0b749977fc61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824737"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory

In questo articolo illustra le domande frequenti e le informazioni relative ai certificati che consente di creare Azure Active Directory (Azure AD) per stabilire single sign-on federato (SSO) con il software come applicazioni di servizio (SaaS). Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. Configurare l'applicazione utilizzando l'opzione di SSO federato.

Questo articolo è applicabile solo alle App configurate per l'uso dell'accesso SSO di Azure AD tramite [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) federazione (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta

Quando si aggiunge una nuova applicazione dalla raccolta e si configura un sign-on basato su SAML (selezionando **l'accesso Single sign-on** > **SAML** dalla pagina di panoramica dell'applicazione), Azure AD genera una certificato per l'applicazione che sono valida per tre anni. Per scaricare il certificato come certificato di sicurezza attivo (**CER**) file, tornare alla pagina (**basato su SAML sign-on**) e selezionare un collegamento di download nel **certificatodifirmaSAML** intestazione. È possibile scegliere tra il (binario) non elaborati del certificato o il certificato (base 64 codificata in formato testo) Base64. Per le applicazioni della raccolta, questa sezione può apparire anche un collegamento per scaricare il certificato come file XML dei metadati di federazione (un **XML** file), a seconda dei requisiti dell'applicazione.

![Attiva firmare opzioni di download certificato SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

È anche possibile scaricare un certificato attivo o inattivo, selezionando il **certificato di firma SAML** dell'intestazione **Edit** icona (una matita), che consente di visualizzare il **certificatodifirmaSAML** pagina. Selezionare i puntini di sospensione ( **...** ) accanto al certificato che si desidera scaricare e quindi scegliere il formato di certificato desiderato. È presente l'opzione aggiuntiva per scaricare il certificato nel formato di posta elettronica sulla privacy avanzata (con estensione PEM). Questo formato è identico in Base64, ma con un **PEM** estensione nome file, che non viene riconosciuto nel Windows come formato di certificato.

![Opzioni di download di certificati (attive e inattive) di firma SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover di un nuovo certificato

Per impostazione predefinita, Azure configura un certificato per far scadere dopo tre anni, quando viene creato automaticamente durante la configurazione SAML single sign-on. Poiché è Impossibile modificare la data di un certificato dopo il salvataggio, è necessario:

1. Creare un nuovo certificato con la data desiderata.
2. Salvare il nuovo certificato.
3. Scaricare il nuovo certificato in formato corretto.
4. Caricare il nuovo certificato all'applicazione.
5. Attiva il nuovo certificato nel portale di Azure Active Directory.

Le due sezioni seguenti consentono di eseguire questi passaggi.

### <a name="create-a-new-certificate"></a>Creare un nuovo certificato

In primo luogo, creare e salvare di nuovo certificato con una data di scadenza diversa:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/). Il **interfaccia di amministrazione di Azure Active Directory** verrà visualizzata la pagina.

2. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.

3. Selezionare l'applicazione interessata. Viene visualizzata una pagina di panoramica per l'applicazione.

4. Nel riquadro sinistro della pagina di panoramica dell'applicazione, selezionare **l'accesso Single sign-on**.

5. Se il **selezionare un metodo single sign-on** verrà visualizzata la pagina, selezionare **SAML**.

6. Nel **impostata su Single Sign-On con SAML - Preview** pagina, trovare il **certificato di firma SAML** titolo e selezionare il **modifica** icona (una matita). Il **certificato di firma SAML** verrà visualizzata la pagina, che consente di visualizzare lo stato (**Active** oppure **Inactive**), data di scadenza e identificazione personale (una stringa hash) di ogni certificato.

7. Selezionare **nuovo certificato**. Una nuova riga viene visualizzato sotto l'elenco di certificati, in cui la data di scadenza predefinito è esattamente tre anni dopo la data corrente. (Le modifiche non sono state ancora salvate, quindi è comunque possibile modificare la data di scadenza.)

8. Nella nuova riga certificato, passare il mouse sopra la colonna di date di scadenza e selezionare il **selezione data** icona (un calendario). Verrà visualizzato un controllo calendario contenente i giorni del mese della data di scadenza attuale della nuova riga.

9. Usare il controllo calendario per impostare una nuova data. È possibile impostare qualsiasi data compresa tra la data corrente e tre anni dopo la data corrente.

10. Selezionare **Salva**. Il nuovo certificato verrà visualizzato con stato **Inactive**, la scadenza e date che si scelto, un'identificazione personale.

11. Selezionare il **X** da restituire per il **impostata su Single Sign-On con SAML - anteprima** pagina.

### <a name="upload-and-activate-a-certificate"></a>Caricare e attivare un certificato

Successivamente, scaricare il nuovo certificato in formato corretto, caricarlo per l'applicazione e renderlo attivo in Azure Active Directory:

1. Visualizzare altre istruzioni di configurazione dell'accesso sign-on SAML dell'applicazione in uno dei modi:
   - Selezionare il **Guida alla configurazione** collegamento per visualizzare in una finestra distinta del browser o una scheda, o
   - passare al **configurare** titolo e selezionando **visualizzare istruzioni dettagliate** per visualizzare in un'intestazione laterale.

2. Nelle istruzioni, si noti il formato di codifica necessario per il caricamento del certificato.

3. Seguire le istruzioni di [certificato generato automaticamente per le applicazioni incluse e non inclusa nella raccolta](#auto-generated-certificate-for-gallery-and-non-gallery-applications) sezione in precedenza. Questo passaggio consente di scaricare il certificato nel formato di codifica per il caricamento richiesto dall'applicazione.

4. Quando si desidera eseguire il rollover al nuovo certificato, tornare indietro per la **certificato di firma SAML** pagina e nella riga certificato appena salvato, selezionare i puntini di sospensione ( **...** ) e selezionare **Rendi attivo il certificato**. Lo stato del nuovo certificato diventa **Active**, e il certificato precedentemente attivo viene modificato in uno stato **Inactive**.

5. Continuare segue le istruzioni di configurazione dell'accesso sign-on SAML dell'applicazione visualizzato in precedenza, in modo che è possibile caricare la firma di SAML del certificato nel formato di codifica corretto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Aggiungere indirizzi di notifica di posta elettronica per la scadenza del certificato

Azure AD invierà giorni notifica 7, 30 e 60 un messaggio di posta elettronica prima della scadenza del certificato SAML. È possibile aggiungere più di un indirizzo di posta elettronica per ricevere le notifiche. Per specificare gli indirizzi di posta elettronica si desidera che le notifiche da inviare al:

1. Nel **certificato di firma SAML** page, passare al **gli indirizzi di posta elettronica di notifica** intestazione. Per impostazione predefinita, questa intestazione Usa solo l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione.

2. Di seguito l'indirizzo di posta elettronica finale, digitare l'indirizzo di posta elettronica che deve ricevere notifica di scadenza del certificato e quindi premere INVIO.

3. Ripetere il passaggio precedente per ogni indirizzo di posta elettronica da aggiungere.

4. Per ogni indirizzo di posta elettronica che si desidera eliminare, selezionare la **eliminare** icona (un Bidone della spazzatura) accanto all'indirizzo di posta elettronica.

5. Selezionare **Salva**.

Si riceverà la notifica tramite posta elettronica da aadnotification@microsoft.com. Per evitare il messaggio di posta elettronica passare al percorso della posta indesiderata, aggiungere questo indirizzo di posta elettronica ai contatti.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Rinnovare un certificato con scadenza imminente

Se un certificato sta per scadere, è possibile rinnovarlo tramite una procedura che restituisce senza tempi di inattività significativi per gli utenti. Per rinnovare un certificato in scadenza:

1. Seguire le istruzioni di [creare un nuovo certificato](#create-a-new-certificate) sezione precedente, utilizzando una data che si sovrappone con il certificato esistente. Tale data limita la quantità di tempo di inattività causato la scadenza del certificato.

2. Se l'applicazione può automaticamente il rollover un certificato, impostare il nuovo certificato come attivo seguendo questa procedura:
   1. Tornare al **certificato di firma SAML** pagina.
   2. Nella riga certificato appena salvato, selezionare i puntini di sospensione ( **...** ) e quindi selezionare **Rendi attivo il certificato**.
   3. Ignorare i due passaggi successivi.

3. Se l'app può gestire solo un certificato alla volta, selezionare un intervallo di tempo di inattività per eseguire il passaggio successivo. (In caso contrario, se l'applicazione non rilevano automaticamente il nuovo certificato, ma può gestire più di un certificato di firma, è possibile eseguire il passaggio successivo in qualsiasi momento.)

4. Prima della scadenza del certificato precedente, seguire le istruzioni di [caricare e attivare un certificato](#upload-and-activate-a-certificate) sezione in precedenza.

5. Accedere all'applicazione per assicurarsi che il certificato funziona correttamente.

## <a name="related-articles"></a>Articoli correlati

* [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
* [Gestione delle applicazioni con Azure Active Directory](what-is-application-management.md)
* [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
* [Eseguire il debug basato su SAML single sign-on alle applicazioni in Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
