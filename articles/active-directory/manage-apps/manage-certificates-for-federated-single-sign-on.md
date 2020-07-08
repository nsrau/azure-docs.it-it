---
title: Gestione di certificati di federazione in Azure AD | Documentazione Microsoft
description: Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1731d5ea5d8db9ea1c5855a32d2daca0387c0bf5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763211"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory

In questo articolo vengono illustrate le domande e le informazioni comuni relative ai certificati creati da Azure Active Directory (Azure AD) per stabilire la Single Sign-On federativa (SSO) per le applicazioni Software as a Service (SaaS). Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. Configurare l'applicazione utilizzando l'opzione di SSO federato.

Questo articolo è pertinente solo per le app configurate per l'uso di Azure AD SSO tramite la federazione [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta

Quando si aggiunge una nuova applicazione dalla raccolta e si configura un accesso basato su SAML (selezionando SAML **Single Sign-on**nella  >  **SAML** pagina Panoramica applicazione), Azure ad genera un certificato per l'applicazione valida per tre anni. Per scaricare il certificato attivo come file del certificato di sicurezza (con**estensione cer**), tornare alla pagina (**accesso basato su SAML**) e selezionare un collegamento per il download nell'intestazione del **certificato di firma SAML** . È possibile scegliere tra il certificato non elaborato (binario) o il certificato Base64 (testo codificato in base 64). Per le applicazioni della raccolta, in questa sezione può essere anche visualizzato un collegamento per scaricare il certificato come XML dei metadati di federazione (un file con **estensione XML** ), a seconda del requisito dell'applicazione.

![Opzioni di download del certificato di firma attiva SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

È anche possibile scaricare un certificato attivo o inattivo selezionando l'icona di **modifica** dell'intestazione del **certificato di firma SAML** (matita), che consente di visualizzare la pagina **certificato di firma SAML** . Selezionare i puntini di sospensione (**...**) accanto al certificato che si desidera scaricare e quindi scegliere il formato del certificato desiderato. Si dispone dell'opzione aggiuntiva per scaricare il certificato in formato PEM (Privacy-Enhanced Mail). Questo formato è identico a Base64 ma con estensione di file **PEM** , che non è riconosciuto in Windows come formato di certificato.

![Opzioni di download del certificato di firma SAML (attivo e inattivo)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover di un nuovo certificato

Per impostazione predefinita, Azure configura un certificato in modo che scada dopo tre anni quando viene creato automaticamente durante la configurazione di SAML Single Sign-On. Poiché non è possibile modificare la data di un certificato dopo averlo salvato, è necessario:

1. Creare un nuovo certificato con la data desiderata.
1. Salvare il nuovo certificato.
1. Scaricare il nuovo certificato nel formato corretto.
1. Caricare il nuovo certificato nell'applicazione.
1. Rendere attivo il nuovo certificato nel portale di Azure Active Directory.

Le due sezioni seguenti consentono di eseguire questi passaggi.

### <a name="create-a-new-certificate"></a>Creare un nuovo certificato

Prima di tutto, creare e salvare un nuovo certificato con una data di scadenza diversa:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzata la pagina **centro di amministrazione Azure Active Directory** .
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.
1. Selezionare l'applicazione interessata. Viene visualizzata una pagina di panoramica per l'applicazione.
1. Nel riquadro sinistro della pagina Panoramica applicazione selezionare **Single Sign-on**.
1. Se viene visualizzata la pagina **selezionare un metodo di Single Sign-on** , selezionare **SAML**.
1. Nella pagina **Configura accesso Single Sign-on con SAML-Preview** trovare l'intestazione **certificato di firma SAML** e selezionare l'icona di **modifica** (matita). Viene visualizzata la pagina **certificato di firma SAML** , che visualizza lo stato (**attivo** o **inattivo**), la data di scadenza e l'identificazione personale (stringa hash) di ogni certificato.
1. Selezionare **nuovo certificato**. Viene visualizzata una nuova riga sotto l'elenco dei certificati, in cui la data di scadenza viene impostata per impostazione predefinita esattamente su tre anni dopo la data corrente. Le modifiche non sono state ancora salvate, quindi è comunque possibile modificare la data di scadenza.
1. Nella riga nuovo certificato passare il puntatore del mouse sulla colonna Data di scadenza e selezionare l'icona **Seleziona data** (un calendario). Viene visualizzato un controllo calendario che Visualizza i giorni del mese della data di scadenza corrente della nuova riga.
1. Usare il controllo Calendar per impostare una nuova data. È possibile impostare una data compresa tra la data corrente e tre anni dopo la data corrente.
1. Selezionare **Salva**. Il nuovo certificato viene ora visualizzato con lo stato **inattivo**, la data di scadenza scelta e un'identificazione personale.
1. Selezionare la **X** per tornare alla pagina **Configura accesso Single Sign-on con SAML-Preview** .

### <a name="upload-and-activate-a-certificate"></a>Caricare e attivare un certificato

Successivamente, scaricare il nuovo certificato nel formato corretto, caricarlo nell'applicazione e renderlo attivo nel Azure Active Directory:

1. Visualizzare le istruzioni aggiuntive per la configurazione dell'accesso SAML dell'applicazione in uno dei seguenti casi:

   - Selezione del collegamento alla **Guida alla configurazione** da visualizzare in una finestra o in una scheda del browser separata oppure
   - Passare all'intestazione di **configurazione** e selezionare **Visualizza istruzioni dettagliate** da visualizzare in una barra laterale.

1. Nelle istruzioni prendere nota del formato di codifica necessario per il caricamento del certificato.
1. Seguire le istruzioni riportate in precedenza nella sezione [certificato generato automaticamente per la raccolta e le applicazioni non](#auto-generated-certificate-for-gallery-and-non-gallery-applications) incluse nella raccolta. Questo passaggio Scarica il certificato nel formato di codifica richiesto per il caricamento da parte dell'applicazione.
1. Quando si desidera eseguire il rollover al nuovo certificato, tornare alla pagina certificato di **firma SAML** e nella riga certificato appena salvato selezionare i puntini di sospensione (**...**) e selezionare **Rendi attivo il certificato**. Lo stato del nuovo certificato diventa **attivo**e il certificato attivo precedentemente diventa uno stato **inattivo**.
1. Continuare seguendo le istruzioni di configurazione dell'accesso SAML dell'applicazione visualizzate in precedenza, in modo da poter caricare il certificato di firma SAML nel formato di codifica corretto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Aggiungi indirizzi di notifica tramite posta elettronica per la scadenza del certificato

Azure AD invierà una notifica di posta elettronica 60, 30 e 7 giorni prima della scadenza del certificato SAML. È possibile aggiungere più di un indirizzo di posta elettronica per ricevere le notifiche. Per specificare gli indirizzi di posta elettronica a cui si desidera inviare le notifiche:

1. Nella pagina **certificato di firma SAML** passare all'intestazione degli **indirizzi di posta elettronica di notifica** . Per impostazione predefinita, questa intestazione usa solo l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione.
1. Al di sotto dell'indirizzo di posta elettronica finale, digitare l'indirizzo di posta elettronica che deve ricevere la notifica di scadenza del certificato, quindi premere INVIO.
1. Ripetere il passaggio precedente per ogni indirizzo di posta elettronica che si desidera aggiungere.
1. Per ogni indirizzo di posta elettronica che si vuole eliminare, selezionare l'icona di **eliminazione** (un Garbage Can) accanto all'indirizzo di posta elettronica.
1. Selezionare **Salva**.

Si riceverà la notifica tramite posta elettronica da aadnotification@microsoft.com. Per evitare che il messaggio di posta elettronica venga inviato al tuo indirizzo di posta indesiderata, Aggiungi questo messaggio ai tuoi contatti.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Rinnovare un certificato con scadenza imminente

Se un certificato sta per scadere, è possibile rinnovarlo utilizzando una procedura che non comporta tempi di inattività significativi per gli utenti. Per rinnovare un certificato in scadenza:

1. Seguire le istruzioni riportate in precedenza nella sezione [creare un nuovo certificato](#create-a-new-certificate) , usando una data che si sovrappone al certificato esistente. Tale data limita la quantità di tempo di inattività causata dalla scadenza del certificato.
1. Se l'applicazione è in grado di eseguire automaticamente il rollover di un certificato, impostare il nuovo certificato su attivo attenendosi alla procedura seguente:
   1. Tornare alla pagina **certificato di firma SAML** .
   1. Nella riga certificato appena salvato selezionare i puntini di sospensione (**...**) e quindi selezionare **Rendi attivo il certificato**.
   1. Ignorare i due passaggi successivi.

1. Se l'app è in grado di gestire un solo certificato alla volta, selezionare un intervallo di inattività per eseguire il passaggio successivo. In caso contrario, se l'applicazione non preleva automaticamente il nuovo certificato ma può gestire più di un certificato di firma, è possibile eseguire il passaggio successivo in qualsiasi momento.
1. Prima della scadenza del certificato precedente, seguire le istruzioni riportate nella sezione [caricare e attivare un certificato](#upload-and-activate-a-certificate) in precedenza.
1. Accedere all'applicazione per assicurarsi che il certificato funzioni correttamente.

## <a name="related-articles"></a>Articoli correlati

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gestione delle applicazioni con Azure Active Directory](what-is-application-management.md)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
