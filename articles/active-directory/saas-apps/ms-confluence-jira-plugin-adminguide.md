---
title: Guida dell'amministratore per Atlassian Jira/Confluence - Azure Active Directory | Microsoft Docs
description: Guida dell'amministratore all'uso di Atlassian Jira e Confluence con Azure Active Directory (Azure AD).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2f06b884cb1213e9d2cabff4e6e2b97a60339a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60935779"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guida dell'amministratore per Atlassian Jira e Confluence per Azure Active Directory

## <a name="overview"></a>Panoramica

Il plug-in Single Sign-On (SSO) di Azure Active Directory (Azure AD) consente ai clienti di Microsoft Azure AD di usare l'account aziendale o dell'istituto di istruzione per accedere ai prodotti basati sui server Atlassian Jira e Confluence. Implementa l'accesso Single Sign-On basato su SAML 2.0.

## <a name="how-it-works"></a>Funzionamento

Quando gli utenti vogliono accedere all'applicazione Atlassian Jira o Confluence, nella pagina di accesso viene visualizzato il pulsante **Accedi con Azure AD**. Dopo aver fatto clic sul pulsante, viene chiesto loro di accedere tramite la pagina di accesso dell'organizzazione ad Azure Active Directory (ovvero usando l'account aziendale o dell'istituto di istruzione).

Una volta autenticati, gli utenti possono eseguire l'accesso all'applicazione. Se sono già stati autenticati con l'ID e la password dell'account aziendale o dell'istituto di istruzione, possono accedere direttamente all'applicazione. 

La procedura di accesso è valida sia per Jira sia per Confluence. Se gli utenti sono connessi all'applicazione Jira e nella stessa finestra del browser è aperto anche Confluence, non è necessario specificare le credenziali per l'altra app. 

Gli utenti possono accedere al prodotto Atlassian anche tramite App personali nell'account aziendale o dell'istituto di istruzione, senza che venga chiesto loro di inserire nuove credenziali.

> [!NOTE]
> Il provisioning dell'utente non viene eseguito mediante il plug-in.

## <a name="audience"></a>Audience

Amministratori di Jira e Confluence che prevedono di usare questo plug-in per abilitare l'accesso Single Sign-On tramite Azure Active Directory.

## <a name="assumptions"></a>Presupposti

* Le istanze di Jira e Confluence sono abilitate per HTTPS.
* Gli utenti sono già stati creati in Jira o Confluence.
* Agli utenti è stato assegnato un ruolo in Jira o Confluence.
* Gli amministratori hanno accesso alle informazioni necessarie per configurare il plug-in.
* Jira o Confluence sono disponibili anche all'esterno della rete aziendale.
* Il plug-in funziona solo con le versioni locali di Jira e Confluence.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare il plug-in, verificare i prerequisiti seguenti:

* Jira e Confluence sono installati in una versione a 64 bit di Windows.
* Le versioni di Jira e Confluence sono abilitate per HTTPS.
* Jira e Confluence sono disponibili in Internet.
* Sono disponibili le credenziali di amministratore per Jira e Confluence.
* Sono disponibili le credenziali di amministratore per Azure AD.
* In Jira e Confluence WebSudo è disabilitato.

## <a name="supported-versions-of-jira-and-confluence"></a>Versioni supportate di Jira e Confluence

Il plug-in supporta le versioni seguenti di Jira e Confluence:

* JIRA Core e Software: da 6.0 a 7.12
* Jira Service Desk: da 3.0.0 a 3.5.0
* JIRA supporta anche la versione 5.2. Per altre informazioni, fare clic su [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: da 5.0 a 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Installazione

Per installare il plug-in, seguire questa procedura:

1. Accedere all'istanza di Jira o Confluence come amministratore.

2. Passare alla console di amministrazione di Jira/Confluence e selezionare **Add-ons** (Componenti aggiuntivi).

3. Dall'Area download Microsoft scaricare il [plug-in Microsoft SAML SSO per Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [plug-in Microsoft SAML SSO per Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Nei risultati della ricerca viene visualizzata la versione appropriata del plug-in.

4. Selezionare il plug-in, che viene installato dallo strumento Universal Plug-in Manager (UPM).

Dopo aver installato il plug-in, questo viene visualizzato nella sezione **User Installed Add-ons** (Componenti aggiuntivi installati dall'utente) di **Manage Add-ons** (Gestisci componenti aggiuntivi).

## <a name="plug-in-configuration"></a>Configurazione del plug-in

Prima di iniziare a usare il plug-in, è necessario configurarlo. Selezionare il plug-in, selezionare il pulsante **Configure** (Configura) e fornire i dettagli di configurazione.

L'immagine seguente mostra la schermata di configurazione in Jira e Confluence:

![Schermata di configurazione del plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metadata URL** (URL metadata): URL per ottenere i metadati di federazione da Azure AD.

* **Identifiers** (Identificatori): URL usato da Azure AD per convalidare l'origine della richiesta. È associato all'elemento **Identificatore** di Azure Active Directory. Il plug-in viene generato automaticamente questo URL come https:// *\<dominio: porta >* /.

* **Reply URL** (URL di risposta): URL di risposta nel provider di identità che avvia la procedura di accesso a SAML. È associato all'elemento **URL di risposta** di Azure Active Directory. Il plug-in viene generato automaticamente questo URL come https:// *\<dominio: porta >* /plugins/servlet/saml/auth.

* **Sign On URL** (URL di accesso): URL di accesso nel provider di identità (IdP) che avvia la procedura di accesso a SAML. È associato all'elemento **Accesso** di Azure Active Directory. Il plug-in viene generato automaticamente questo URL come https:// *\<dominio: porta >* /plugins/servlet/saml/auth.

* **IdP Entity ID** (ID entità IdP): ID entità usato dal provider di identità. Questo campo viene popolato quando viene risolto l'URL dei metadati.

* **Login URL** (URL di accesso): URL di accesso fornito dal provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.

* **Logout URL** (URL di disconnessione): URL di disconnessione fornito dal provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.

* **X.509 Certificate** (Certificato X.509): certificato X.509 del provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.

* **Login Button Name**(Nome pulsante di accesso): testo visualizzato dagli utenti dell'organizzazione sul pulsante di accesso nella schermata di accesso.

* **SAML User ID Locations**(Posizioni ID utente SAML): posizione in cui è previsto l'ID utente di Jira o Confluence nella risposta SAML. Può essere in **NameID** o in un nome di attributo personalizzato.

* **Attribute Name** (Nome attributo): nome dell'attributo in cui è previsto un ID utente.

* **Enable Home Realm Discovery** (Abilita individuazione dell'area di autenticazione principale): selezionare questa opzione se l'azienda usa l'accesso basato su Active Directory Federation Services (AD FS).

* **Domain Name** (Nome dominio): nome di dominio in caso di accesso basato su AD FS.

* **Enable Single Signout** (Abilita Single Sign-Out): selezionare questa opzione se si vuole eseguire la disconnessione da Azure Active Directory quando un utente si disconnette da Jira o Confluence.

## <a name="troubleshooting"></a>risoluzione dei problemi

* **Si ricevono più errori relativi ai certificati**: accedere ad Azure AD e rimuovere i vari certificati disponibili per l'app. Verificare che sia presente un solo certificato.

* **Un certificato sta per scadere in Azure AD**: l'esecuzione dell'effetto di attivazione automatico del certificato è gestita da componenti aggiuntivi. Quando un certificato sta per scadere, è necessario che un nuovo certificato venga contrassegnato come attivo e che i certificati inutilizzati vengano eliminati. Quando un utente tenta di eseguire l'accesso a Jira in questo scenario, il plug-in recupera e salva il nuovo certificato.

* **Come disabilitare WebSudo (disabilitare la sessione amministratore protetta)** :

  * Per Jira, le sessioni amministratore protette (ovvero la conferma della password prima di accedere alle funzioni di amministrazione) sono abilitate per impostazione predefinita. Se si vuole disabilitare questa funzionalità nell'istanza di Jira, specificare la riga seguente nel file jira-config.properties: `ira.websudo.is.disabled = true`

  * Per Confluence, seguire la procedura specificata nel [sito del supporto di Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **I campi che avrebbero dovuto essere popolati dall'URL dei metadati non risultano compilati**:

  * Controllare che l'URL sia corretto. Verificare di aver mappato il tenant e l'ID app corretti.

  * Immettere l'URL in un browser e controllare se si riceve il file XML dei metadati della federazione.

* **Si verifica un errore interno del server**: esaminare i log presenti nella directory dei log dell'installazione. Se questo errore viene visualizzato mentre l'utente sta tentando di accedere con il plug-in Single Sign-On di Azure Active Directory, è possibile condividere i log con il team di supporto.

* **Si verifica un errore di ID utente non trovato mentre l'utente tenta di accedere**: creare l'ID utente in Jira o Confluence.

* **Si verifica un errore di app non trovata in Azure AD**: Verificare che all'app di Azure Active Directory sia stato mappato l'URL appropriato.

* **Serve assistenza**: Contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Il team risponde in 24-48 ore.

  È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure.

## <a name="plug-in-faq"></a>Domande frequenti sul plug-in

Fare riferimento alle domande frequenti di seguito nel caso di query relative a questo plug-in.

### <a name="what-does-the-plug-in-do"></a>A cosa serve il plug-in?

Il plug-in fornisce funzionalità di accesso Single Sign-On per il software locale Atlassian Jira (tra cui JIRA Core, JIRA Software, JIRA Service Desk) e Confluence. Il plug-in funziona con Azure Active Directory (Azure AD) come provider di identità.

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Con quali prodotti Atlassian si può usare il plug-in?

Il plug-in funziona solo con le versioni locali di Jira e Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Il plug-in funziona con le versioni cloud?

No. Il plug-in supporta solo le versioni locali di Jira e Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Quali sono le versioni di Jira e Confluence supportate dal plug-in?

Il plug-in supporta queste versioni:

* JIRA Core e Software: da 6.0 a 7.12
* Jira Service Desk: da 3.0.0 a 3.5.0
* JIRA supporta anche la versione 5.2. Per altre informazioni, fare clic su [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: da 5.0 a 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Il plug-in è gratuito o a pagamento?

È un componente aggiuntivo gratuito.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>È necessario riavviare Jira o Confluence dopo aver distribuito questo plug-in?

Il riavvio non è necessario. È possibile iniziare a usare il plug-in immediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Come è possibile ottenere assistenza per questo plug-in?

È possibile contattare il [Team di integrazione di Azure Active Directory SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) per qualsiasi assistenza relativa a questo plug-in. Il team risponde in 24-48 ore.

È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Il plug-in funziona anche con installazioni di Jira e Confluence su sistemi Mac e Ubuntu?

Questo plug-in è stato testato solo su installazioni di Jira e Confluence in Windows Server a 64 bit.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Il plug-in funziona con provider di identità diversi da Azure AD?

No. Funziona solo con Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Con quale versione di SAML funziona questo plug-in?

Funziona con SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Il plug-in esegue il provisioning degli utenti?

No. Il plug-in fornisce solo accesso Single Sign-On basato su SAML 2.0. È quindi necessario che venga eseguito il provisioning dell'utente nell'applicazione prima di poter effettuare l'accesso Single Sign-On.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Il plug-in supporta le versioni cluster di Jira e Confluence?

No. Il plug-in funziona solo con le versioni locali di Jira e Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Il plug-in funziona con le versioni HTTP di Jira e Confluence?

No. Questo plug-in funziona solo con installazioni abilitate per HTTPS.
