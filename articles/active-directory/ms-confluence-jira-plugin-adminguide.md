---
title: Guida dell'amministratore per il plug-in Single Sign-On di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jira/Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Guida dell'amministratore per il plug-in Single Sign-On di Azure Active Directory

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

## <a name="prerequisites"></a>prerequisiti

Prima di installare il plug-in, verificare i prerequisiti seguenti:

* Jira e Confluence sono installati in una versione a 64 bit di Windows.
* Le versioni di Jira e Confluence sono abilitate per HTTPS.
* Jira e Confluence sono disponibili in Internet.
* Sono disponibili le credenziali di amministratore per Jira e Confluence.
* Sono disponibili le credenziali di amministratore per Azure AD.
* In Jira e Confluence WebSudo è disabilitato.

## <a name="supported-versions-of-jira-and-confluence"></a>Versioni supportate di Jira e Confluence

Il plug-in supporta le versioni seguenti di Jira e Confluence:

* Jira Core e Software: da 6.0 a 7.2.0
* Jira Service Desk: da 3.0 a 3.2
* Confluence: da 5.0 a 5.10

## <a name="installation"></a>Installazione

Per installare il plug-in, seguire questa procedura:

1. Accedere all'istanza di Jira o Confluence come amministratore.
    
2. Passare alla console di amministrazione di Jira/Confluence e selezionare **Add-ons** (Componenti aggiuntivi).
    
3. Nel marketplace di Atlassian cercare **Microsoft SAML SSO Plugin** (Plug-in SSO SAML Microsoft).
 
   Nei risultati della ricerca viene visualizzata la versione appropriata del plug-in.
 
5. Selezionare il plug-in, che viene installato dallo strumento Universal Plug-in Manager (UPM).
 
Dopo aver installato il plug-in, questo viene visualizzato nella sezione **User Installed Add-ons** (Componenti aggiuntivi installati dall'utente) di **Manage Add-ons** (Gestisci componenti aggiuntivi).
    
## <a name="plug-in-configuration"></a>Configurazione del plug-in

Prima di iniziare a usare il plug-in, è necessario configurarlo. Selezionare il plug-in, selezionare il pulsante **Configure** (Configura) e fornire i dettagli di configurazione.

L'immagine seguente mostra la schermata di configurazione in Jira e Confluence:
    
![Schermata di configurazione del plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Metadata URL** (URL dei metadati): URL per ottenere i metadati di federazione da Azure Active Directory.
 
*   **Identifiers** (Identificatori): URL usato da Azure Active Directory per convalidare l'origine della richiesta. È associato all'elemento **Identificatore** di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://*< dominio: porta >*/.
 
*   **Reply URL** (URL di risposta): URL di risposta nel provider di identità che avvia la procedura di accesso a SAML. È associato all'elemento **URL di risposta** di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://*<dominio:porta>*/plugins/servlet/saml/auth.
 
*   **Sign On URL** (URL di accesso): URL di accesso nel provider di identità che avvia la procedura di accesso a SAML. È associato all'elemento **Accesso** di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://*<dominio:porta>*/plugins/servlet/saml/auth.
 
*   **IdP Entity ID** (ID entità provider di identità): l'ID entità usato dal provider di identità. Questo campo viene popolato quando viene risolto l'URL dei metadati.
 
*   **Login URL** (URL di accesso): l'URL di accesso fornito dal provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.
 
*   **Log out URL** (URL disconnessione): l'URL di disconnessione fornito dal provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.
 
*   **X.509 Certificate** (Certificato X.509): il certificato X.509 del provider di identità. Questo campo viene popolato da Azure AD quando viene risolto l'URL dei metadati.
 
*   **Login Button Name**(Nome pulsante di accesso): testo visualizzato dagli utenti dell'organizzazione sul pulsante di accesso nella schermata di accesso.
 
*   **SAML User ID Locations**(Percorsi ID utente SAML): la posizione in cui è previsto l'ID utente di Jira o Confluence nella risposta SAML. Può essere in **NameID** o in un nome di attributo personalizzato.
 
*   **Attribute name** (Nome attributo): il nome dell'attributo in cui è previsto un ID utente.
 
*   **Enable Home Realm Discovery**(Abilita individuazione dell'area di autenticazione principale): selezionare questa opzione se l'azienda usa l'accesso basato su Active Directory Federation Services (AD FS).
 
*   **Domain Name** (Nome dominio): nome di dominio in caso di accesso basato su AD FS.
 
*   **Enable Single Signout**(Abilita Single Sign-Out): selezionare questa opzione se si vuole eseguire la disconnessione da Azure Active Directory quando un utente si disconnette da Jira o Confluence.

## <a name="troubleshooting"></a>risoluzione dei problemi

* **Si ricevono più errori relativi ai certificati**: accedere ad Azure AD e rimuovere i vari certificati disponibili per l'app. Verificare che sia presente un solo certificato.

* **Un certificato sta per scadere in Azure AD**: il rollover automatico del certificato è gestito da componenti aggiuntivi. Quando un certificato sta per scadere, è necessario che un nuovo certificato venga contrassegnato come attivo e che i certificati inutilizzati vengano eliminati. Quando un utente tenta di eseguire l'accesso a Jira in questo scenario, il plug-in recupera e salva il nuovo certificato.

* **Come disabilitare WebSudo (disabilitare la sessione amministratore protetta)**:
    
  * Per Jira, le sessioni amministratore protette (ovvero la conferma della password prima di accedere alle funzioni di amministrazione) sono abilitate per impostazione predefinita. Se si vuole disabilitare questa funzionalità nell'istanza di Jira, specificare la riga seguente nel file jira-config.properties: `ira.websudo.is.disabled = true`
    
  * Per Confluence, seguire la procedura specificata nel [sito del supporto di Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **I campi che avrebbero dovuto essere popolati dall'URL dei metadati non risultano compilati**:
    
  * Controllare che l'URL sia corretto. Verificare di aver mappato il tenant e l'ID app corretti.
    
  * Immettere l'URL in un browser e controllare se si riceve il file XML dei metadati della federazione.

* **Si verifica un errore interno del server**: esaminare i log presenti nella directory dei log dell'installazione. Se questo errore viene visualizzato mentre l'utente sta tentando di accedere con il plug-in Single Sign-On di Azure Active Directory, è possibile condividere i log con il team di supporto.

* **Si verifica un errore di ID utente non trovato mentre l'utente tenta di accedere**: creare l'ID utente in Jira o Confluence.

* **Si verifica un errore di app non trovata in Azure Active Directory**: verificare che all'app in Azure Active Directory sia stato associato l'URL appropriato.

* **Occorre supporto**: contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Il team risponde in 24-48 ore.
    
  È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure.