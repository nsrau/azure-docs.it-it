---
title: Guida dell'amministratore per il plug-in Single Sign-On di Microsoft Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Microsoft Azure Active Directory single sign-on for JIRA.
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
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Guida dell'amministratore per il plug-in Single Sign-On di Microsoft Azure Active Directory

## <a name="table-of-contents"></a>Sommario

1. **[PANORAMICA](#overview)**
2. **[FUNZIONAMENTO](#how-it-works)**
3. **[DESTINATARI](#audience)**
4. **[PRESUPPOSTI](#assumptions)**
5. **[PREREQUISITI](#prerequisites)**
6. **[VERSIONI DI JIRA E CONFLUENCE SUPPORTATE](#supported-versions-of-jira-and-confluence)**
7. **[INSTALLAZIONE](#installation)**
8. **[CONFIGURAZIONE DEL PLUG-IN](#plugin-configuration)**
9. **[DESCRIZIONE DEI CAMPI DELLA SCHERMATA DI CONFIGURAZIONE DEL COMPONENTE AGGIUNTIVO:](#field-explanation-for-add---on-configuration-screen:)**
10. **[RISOLUZIONE DEI PROBLEMI](#troubleshooting)**

## <a name="overview"></a>Panoramica

Questi componenti aggiuntivi consentono ai clienti di Microsoft Azure Active Directory di usare il nome utente e la password dell'organizzazione per accedere ai prodotti basati sui server Atlassian Jira e Confluence. Implementano l'accesso Single Sign-On basato su SAML 2.0.

## <a name="how-it-works"></a>Funzionamento

Quando gli utenti vogliono accedere all'applicazione Atlassian Jira o Confluence, nella pagina di accesso viene visualizzato il pulsante **Accedi con Azure AD**. Dopo aver fatto clic sul pulsante, viene chiesto loro di accedere tramite la pagina di accesso dell'organizzazione ad Azure Active Directory.

Una volta autenticati, gli utenti possono eseguire l'accesso all'applicazione. Se sono già stati autenticati con l'ID e la password dell'organizzazione, possono accedere direttamente all'applicazione. La procedura di accesso è valida sia per JIRA sia per Confluence. Se gli utenti sono connessi all'applicazione JIRA e nella stessa finestra del browser è aperto anche Confluence, sarà sufficiente eseguire l'accesso una sola volta, senza dover specificare nuovamente le credenziali per l'altra app. Gli utenti possono accedere al prodotto Atlassian anche tramite myapps con l'account di Azure, senza che venga chiesto loro di inserire nuove credenziali.

> [!NOTE]
> Con questo componente aggiuntivo non viene eseguito il provisioning dell'utente.

## <a name="audience"></a>Destinatari

Amministratori di JIRA e Confluence che prevedono di usare questo plug-in per abilitare l'accesso Single Sign-On tramite Azure Active Directory.

## <a name="assumptions"></a>Presupposti

* L'istanza JIRA/Confluence è abilitata per HTTPS
* Gli utenti sono già stati creati in JIRA/Confluence
* Agli utenti è stato assegnato un ruolo in JIRA/Confluence
* Gli amministratori hanno accesso alle informazioni necessarie per configurare il plug-in
* JIRA/Confluence sono disponibili anche all'esterno della rete aziendale
* Il componente aggiuntivo funziona sola con le versioni locali di JIRA and Confluence

## <a name="prerequisites"></a>Prerequisiti

Prendere nota dei prerequisiti seguenti prima di procedere all'installazione del componente aggiuntivo:

* JIRA/Confluence sono installati in una versione a 64 bit di Windows
* Le versioni di JIRA/Confluence sono abilitate per HTTPS
* Verificare la versione supportata per il plug-in nella sezione "Versioni supportate" più in basso.
* JIRA/Confluence è disponibile in Internet.
* Le credenziali di amministratore per JIRA/Confluence
* Le credenziali di amministratore per Azure AD
* In JIRA e Confluence deve essere disabilitato WebSudo

## <a name="supported-versions-of-jira-and-confluence"></a>Versioni supportate di JIRA e Confluence

Attualmente sono supportate le versioni seguenti di JIRA e Confluence:

* Core e Software JIRA: da 6.0 a 7.2.0
* JIRA Service Desk: da 3.0 a 3.2
* Confluence: da 5.0 a 5.10

## <a name="installation"></a>Installazione

Per installare il plug-in l'amministratore deve seguire la procedura seguente:

1. Accedere all'istanza di JIRA/Confluence come amministratore
    
2. Passare a JIRA/Confluence Administration (Amministrazione di JIRA/Confluence) e fare clic su Add-ons (Componenti aggiuntivi).
    
3. Nel marketplace di Atlassian cercare **Microsoft SAML SSO Plugin** (Plug-in SSO SAML Microsoft)
 
4. Nella pagina di ricerca viene visualizzata la versione appropriata del componente aggiuntivo
 
5. Selezionare il plug-in, che viene installato dallo strumento di gestione dei profili utente.
 
6. Dopo aver installato il plug-in, questo verrà visualizzato nella sezione User Installed add-ons (Componenti aggiuntivi installati dall'utente) della sezione User Profile Manager (Gestisci componente aggiuntivo)
 
7. È necessario configurare il plug-in prima di iniziare a usarlo.
 
8. Fare clic sul plug-in per visualizzare un pulsante di configurazione.
 
9. Fare clic sul pulsante per specificare gli input di configurazione
    
## <a name="plugin-configuration"></a>Configurazione del plug-in

L'immagine seguente mostra la schermata di configurazione del componente aggiuntivo in JIRA e Confluence
    
![Configurazione del componente aggiuntivo](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Descrizione dei campi della schermata di configurazione del componente aggiuntivo:

1.   Metadata URL (URL dei metadati): URL per ottenere i metadati di federazione da Azure Active Directory
 
2.   Identifier (Identificatore): usato da Azure Active Directory per convalidare l'origine della richiesta. È associato all'elemento Identificatore di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://<domain:port>/
 
3.   Reply URL (URL di risposta): usare l'URL di risposta nel provider di identità per avviare la procedura di accesso a SAML. È associato all'elemento URL di risposta di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://<domain:port>/plugins/servlet/saml/auth
 
4.   Sign On URL (URL di accesso): usare l'URL di accesso nel provider di identità per avviare la procedura di accesso a SAML. È associato all'elemento Accesso di Azure Active Directory. Questo valore viene generato automaticamente dal plug-in come https://<domain:port>/plugins/servlet/saml/auth
 
5.   IdP Entity ID (ID entità provider di identità): l'ID entità usato dal provider di identità. Questo campo viene popolato quando viene risolto l'URL dei metadati.
 
6.   Login URL (URL di accesso): l'URL di accesso fornito dal provider di identità. Questo campo viene popolato da Azure Active Directory quando viene risolto l'URL dei metadati.
 
7.   Log out URL (URL disconnessione): l'URL di disconnessione fornito dal provider di identità. Questo campo viene popolato da Azure Active Directory quando viene risolto l'URL dei metadati.
 
8.   X.509 Certificate (Certificato X.509): il certificato X.509 del provider di identità. Questo campo viene popolato da Azure Active Directory quando viene risolto l'URL dei metadati.
 
9.   Login Button Name (Nome pulsante di accesso): assegnare al pulsante di accesso il nome desiderato dall'organizzazione. Questo testo viene visualizzato dagli utenti sul pulsante di accesso nella schermata di accesso.
 
10.   SAML User ID Locations (Percorsi ID utente SAML): la posizione in cui è previsto l'ID utente nella risposta SAML. Può essere in NameID o in un nome di attributo personalizzato. Questo ID deve essere l'ID utente di JIRA/Confluence.
 
11.   Attribute name (Nome attributo): il nome dell'attributo in cui può essere previsto un ID utente.
 
12.   Enable Home Realm Discovery (Abilita individuazione dell'area di autenticazione principale): selezionare questo flag se l'azienda usa l'accesso basato sul file system distribuito di Azure.
 
13.   Domain Name (Nome dominio): specificare qui il nome del dominio in caso di accesso basato sul file system distribuito di Azure
 
14.   Enable Single Sign out (Abilita Single Sign-Out): selezionare questa casella se si vuole disconnettersi da Azure Active Directory quando si esegue la disconnessione da JIRA/Confluence.

### <a name="troubleshooting"></a>Risoluzione dei problemi

* Se si ricevono più errori di certificati
    
    * Accedere ad Azure AD e rimuovere i vari certificati disponibili per l'app. Verificare che sia presente un solo certificato.

* Il certificato sta per scadere in Azure AD.
    
    * L'esecuzione del rollover automatico del certificato è gestita da componenti aggiuntivi. Quando un certificato sta per scadere, è necessario che un nuovo certificato venga contrassegnato come attivo e che il certificato inutilizzato venga eliminato. Quando un utente tenta di eseguire l'accesso a JIRA in questo scenario, un componente aggiuntivo recupera il nuovo certificato e salva il plug-in.

* Come disabilitare WebSudo (disabilitare la sessione amministratore protetta)
    
    * JIRA: le sessioni amministratore protette (ovvero la conferma della password prima di accedere alle funzioni di amministrazione) sono abilitate per impostazione predefinita. Se si preferisce disabilitarle nell'istanza JIRA, è possibile disabilitare questa funzionalità specificando la riga seguente nel file jira-config.properties: "ira.websudo.is.disabled = true"
    
    * Confluence: seguire la procedura specificata all'indirizzo seguente https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* I campi che avrebbero dovuto essere popolati dall'URL dei metadati non risultato compilati
    
    * Controllare che l'URL sia corretto. Verificare di aver mappato il tenant e l'ID app corretti.
    
    * Raggiungere l'URL dal browser e controllare se si sta ricevendo il file XML dei metadati della federazione.

* Errore interno del server:
    
    * Esaminare i log presenti nella directory dei log dell'installazione. Se questo errore viene visualizzato mentre l'utente sta tentando di accedere con il plug-in Single Sign-On di Active Directory, è possibile condividere i log con le informazioni di supporto specificate di seguito in questo documento.

* Errore di ID utente non trovato mentre l'utente tenta di accedere
    
    * L'utente non è stato creato in JIRA/Confluence e deve quindi essere creato.

* Errore di app non trovata in Azure Active Directory
    
    * Verificare che all'app di Azure Active Directory sia stato mappato l'URL appropriato.

* Informazioni di supporto: contattare il [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Verrà fornita una risposta entro 24-48 ore.
    
    * È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure.