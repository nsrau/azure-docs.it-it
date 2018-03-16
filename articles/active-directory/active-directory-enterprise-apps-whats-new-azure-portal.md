---
title: "Novità della gestione delle applicazioni aziendali in Azure Active Directory | Microsoft Docs"
description: "Informazioni sulle novità della gestione delle applicazioni aziendali in Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 949eae51c469205489153c271a7c20fcc843ad46
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Novità della gestione delle applicazioni aziendali in Azure Active Directory 

Azure Active Directory (Azure AD) dispone di strumenti avanzati per la gestione delle applicazioni che includono nuove caratteristiche e funzionalità mirate a semplificare e a rendere più efficiente la gestione delle app.

Di seguito sono descritti alcuni dei miglioramenti apportati per Azure AD nel [portale di Azure](https://portal.azure.com).

- Raccolta di applicazioni migliorata, con un modello semplificato per la creazione di applicazioni e il supporto per tutti i tipi di applicazione già disponibili nel portale classico. 
- Esperienza di avvio rapido completamente nuova che consente di creare rapidamente un progetto pilota dell'applicazione. 
- Possibilità di configurare i criteri self-service con pochi clic. 
- Miglioramenti significativi al proxy dell'applicazione, alla configurazione Single Sign-On e all'esperienza "Bring Your Own Applications", per sfruttare al massimo le potenzialità del nuovo portale.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Miglioramenti alla raccolta di applicazioni di Azure Active Directory

È possibile aggiungere le applicazioni preferite a prescindere che provengano dalla [raccolta di applicazioni](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), sia applicazioni personalizzate da estendere al cloud o nuove applicazioni in sviluppo.  È possibile iniziare a usare questa nuova esperienza facendo clic su **Aggiungi** nella panoramica **Applicazioni aziendali** o nel riquadro **Tutte le applicazioni**.
 
  ![Aggiunta di un'applicazione](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

All'interno della raccolta verranno visualizzate al centro tutte le applicazioni che supportano il provisioning utenti.  È possibile sfogliare tutte le categorie per trovare le applicazioni di interesse oppure usare l'esperienza di ricerca per trovare rapidamente le applicazioni da integrare.

  ![Raccolta di applicazioni](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Aggiungere applicazioni personalizzate da un'unica posizione

Oltre all'aggiunta di applicazioni preintegrate dalla raccolta, nel nuovo portale sono ora disponibili tutte le esperienze di configurazione delle applicazioni personalizzate già presenti nel portale di gestione classico. Nel nuovo portale è possibile eseguire tutte le operazioni per estendere un'applicazione dall'ambiente locale tramite il proxy dell'applicazione, integrare un'applicazione con Single Sign-On federato o basato su password e creare un'applicazione completamente nuova usando il Registro applicazioni.

  ![Aggiunta di una propria applicazione](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Per iniziare ad aggiungere la propria applicazione**:

1. Fare clic sul collegamento **Aggiungere la propria applicazione** nella parte superiore della raccolta di applicazioni. 
2. Verranno visualizzate due opzioni: **Distribuzione di un'applicazione esistente** o **Sviluppo di una nuova applicazione**. Di seguito viene illustrata la differenza tra le due opzioni e come usarle.

### <a name="deploying-existing-applications"></a>Distribuzione di applicazioni esistenti

1. Se un'applicazione è già in esecuzione e si vuole semplicemente integrarla in Azure AD per l'accesso Single Sign-On o il provisioning, scegliere l'opzione **Distribuzione di un'applicazione esistente**. Scegliere un nome per l'applicazione, quindi fare clic su **Aggiungi**.
2. L'operazione è terminata. Non è più necessario conoscere tutti i dettagli dell'applicazione sin dall'inizio; ora è possibile definirne il funzionamento usando il menu a sinistra e configurando l'applicazione in base alle esigenze in qualsiasi momento.

  ![Aggiunta di un'applicazione esistente con un clic](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Sviluppo di nuove applicazioni

1. Se si sviluppa una nuova applicazione, esiste un modo semplice per accedere al Registro applicazioni direttamente dalla raccolta:
2. Fare clic su **Aggiungere la propria applicazione** nella raccolta di applicazioni, quindi scegliere **Sviluppo di una nuova applicazione**. Verrà visualizzato un collegamento rapido a destra dell'esperienza di aggiunta di applicazioni.

  ![Aggiunta di un'applicazione appena sviluppata in pochi clic](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Dopo averla aggiunta con il Registro applicazioni, l'applicazione sarà visualizzata nell'elenco di applicazioni aziendali nel quale sarà possibile configurare l'accesso Single Sign-On e gestire i criteri di accesso per la nuova applicazione.

  ![Gestione dell'accesso alla nuova applicazione in Applicazioni aziendali](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Avvio rapido: iniziare a usare subito la nuova applicazione 

Dopo aver aggiunto un'applicazione preintegrata o sviluppata dall'utente, è disponibile un'esperienza di avvio rapido personalizzata che consente di iniziare a usare subito l'applicazione. Se si segue sistematicamente ogni opzione, verranno illustrate l'interfaccia utente e la procedura per iniziare a usare un progetto pilota della nuova applicazione nel minor tempo possibile. 
 
  ![Esperienza di avvio rapido per la nuova applicazione](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 È possibile visitare questa nuova esperienza di avvio rapido in qualsiasi momento e per qualsiasi applicazione, facendo clic su **Avvio rapido** dal menu di navigazione a sinistra dell'applicazione.


## <a name="updated-application-proxy-configuration"></a>Aggiornamento della configurazione del proxy dell'applicazione
Si supponga che una delle nuove applicazioni aggiunte venga eseguita nell'ambiente locale e si voglia integrarla con Azure AD.  Una delle caratteristiche interessanti della nuova esperienza di configurazione dell'applicazione nel nuovo portale di Azure AD è che separando la modalità di accesso dell'applicazione dalla configurazione proxy è ora possibile esporre direttamente nel cloud applicazioni federate o con accesso Single Sign-On basato su password in esecuzione nella rete aziendale, senza dover creare più istanze dell'applicazione.

Ora è anche possibile configurare le nuove applicazioni aggiunte per l'uso con il proxy dell'applicazione di Azure AD direttamente dal nuovo portale, incluse le applicazioni che supportano esperienze di autenticazione di Windows nativa.

  ![Configurazione di un'applicazione per l'uso dell'opzione di autenticazione integrata di Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Per configurare un'applicazione con autenticazione di Windows nativa con il proxy dell'applicazione:
1. Fare clic sull'elemento di navigazione Single Sign-On e scegliere **Autenticazione di Windows integrata** dal pannello delle impostazioni di accesso, quindi configurare le impostazioni in base alle esigenze.
2. Oltre a supportare queste nuove modalità di autenticazione, ora è anche possibile caricare certificati da domini personalizzati per supportare le applicazioni in esecuzione in un endpoint sicuro all'interno dell'organizzazione.  
 
   ![Caricamento di un certificato da usare con il proxy dell'applicazione](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Per caricare un nuovo certificato per l'applicazione locale preferita, fare clic sull'opzione **Proxy dell'applicazione** nel menu di navigazione a sinistra, fare clic sul selettore **Certificato** e quindi caricare un file di certificato che è possibile usare per crittografare le richieste dall'endpoint del cloud Microsoft all'applicazione.

## <a name="advanced-federated-single-sign-on-configuration"></a>Configurazione dell'accesso Single Sign-On federato avanzato

Per coloro che usano applicazioni federate sono disponibili molte nuove funzionalità nel pannello di configurazione dell'accesso basato su SAML. Ora è possibile personalizzare, aggiungere, rimuovere e mappare completamente gli attributi utente esistenti emessi come attestazioni nei token SAML.
 
  ![Personalizzazione degli attributi utente del token SAML passati a un'applicazione federata](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Per verificare la nuova configurazione di SSO federato:
1. Aprire il pannello **Single Sign-On** di un'applicazione federata dal menu di navigazione a sinistra e assicurarsi che sia selezionata la modalità *Accesso basato su SAML**. 
2. Abilitare quindi la casella di controllo sotto l'intestazione **Attributi utente** per modificare tutti gli attributi inclusi nel token SAML passato all'applicazione.

È anche possibile creare e gestire i certificati usati per l'accesso Single Sign-On federato ed eseguirne il rollover, nonché modificare il destinatario della notifica di scadenza del certificato. Queste nuove opzioni sono disponibili nell'intestazione **Certificati** dello stesso pannello Single Sign-On.
 
  ![Creazione di un nuovo certificato, personalizzazione della notifica e-mail di scadenza e delle opzioni di firma del certificato](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Parametro Stato dell'inoltro
Il set di parametri URL SAML supportati include ora il parametro **Stato dell'inoltro**, ovvero la pagina che verrà visualizzata all'interno di un'applicazione federata al termine della procedura di accesso. Si tratta di un'impostazione molto utile per indirizzare gli utenti a una pagina specifica dell'applicazione per iniziare a usarla rapidamente.

  ![Impostazione del parametro Stato dell'inoltro SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Per impostare il parametro Stato dell'inoltro**:

1. Abilitare la casella di controllo **Mostra impostazioni URL avanzate** sotto l'intestazione **URL e dominio** nel pannello di configurazione dell'accesso Single Sign-On. 
2. Verrà quindi visualizzato un set di nuove caselle di input URL che consentono di definire queste impostazioni URL SAML.

## <a name="bring-your-own-password-sso-applications"></a>Applicazioni con Single Sign-On basato su password con funzionalità "Bring Your Own Applications"

Non tutte le applicazioni supportano la federazione per impostazione predefinita. È ad esempio possibile che una delle nuove applicazioni aggiunte abbia una schermata di accesso personalizzata per la quale si usano nome utente e password. È comunque possibile integrare questi tipi di applicazioni con Azure AD usando la funzionalità **Bring Your Own Applications** ora disponibile per la configurazione nel nuovo portale.
 
  ![Integrazione con Azure AD di applicazioni personalizzate con insieme di credenziali delle password](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Per verificare la funzionalità 'Bring Your Own Applications'**:

1. Dopo aver impostato la modalità Single Sign-On per la nuova applicazione personalizzata aggiunta ad **Accesso basato su password**, immettere l'URL in cui l'applicazione visualizza la schermata di accesso e fare clic su **Salva**.  
2. Dopo questa operazione, dall'URL verrà creata automaticamente una casella di input per nome utente e password e sarà possibile usare Azure AD per la trasmissione sicura delle password all'applicazione tramite l'estensione del browser del pannello di accesso.

## <a name="configure-self-service-application-access"></a>Configurare l'accesso alle applicazioni self-service

Dopo aver aggiunto un numero elevato di nuove applicazioni, può essere opportuno consentire agli utenti di esplorare e aggiungere tali applicazioni ai propri pannelli di accesso senza dover contattare l'amministratore. Questa nuova versione consente di configurare e gestire l'accesso alle applicazioni self-service direttamente dal nuovo portale.

  ![Configurazione dell'accesso alle applicazioni self-service per un'applicazione con Single Sign-On basato su password](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Per configurare e gestire l'accesso alle applicazioni self-service**:

1. Per iniziare è possibile selezionare l'opzione **Self-service** dal menu di navigazione a sinistra dell'applicazione e impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su '**Sì**'. 
2. Sarà così possibile definire i responsabili dell'approvazione dell'accesso all'applicazione e il gruppo al quale verranno aggiunti gli utenti self-service. Se l'applicazione è configurata per l'accesso Single Sign-On con password, verrà visualizzata anche un'altra opzione che consente eventualmente di autorizzare tali responsabili dell'approvazione alla gestione delle password assegnate all'applicazione.

##<a name="feedback"></a>Commenti e suggerimenti

Speriamo che gli utenti usino il nuovo Azure AD e inviino commenti e suggerimenti. È possibile inviare commenti e idee per apportare miglioramenti nella sezione **Portale di amministrazione** del [forum dei commenti](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Microsoft accoglie i suggerimenti degli utenti per definire nuove funzionalità ogni giorno.

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli, vedere [Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md).



