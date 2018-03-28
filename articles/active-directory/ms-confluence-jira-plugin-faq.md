---
title: Domande frequenti sul plug-in Single Sign-On di Microsoft Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Domande frequenti sul plug-in Single Sign-On di Microsoft Azure Active Directory 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Che cos'è il componente aggiuntivo Single Sign-On di Microsoft?

Questo componente aggiuntivo fornisce funzionalità di accesso Single Sign-On per applicazioni software JIRA (tra cui JIRA Core, JIRA Software, JIRA Service Desk) e Atlassian locali. Interagisce con Azure Active Directory come provider di identità.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Questo componente aggiuntivo funziona con i prodotti Atlassian?

Per il momento questo componente aggiuntivo funziona solo con versioni locali di JIRA and Confluence.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Questo componente aggiuntivo funziona con le versioni cloud?

No. Sono supportate solo le versioni locali di JIRA e Confluence.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Quali versioni di JIRA e Confluence sono supportate?

Di seguito è riportato l'elenco delle versioni supportate:

* JIRA Core e Software: da 6.0 a 7.2.2 
* JIRA Service Desk: da 3.0 a 3.2 
* Confluence: da 5.0 a 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Questo componente aggiuntivo è gratuito o a pagamento?

È un componente aggiuntivo gratuito che può essere installato dal marketplace di Atlassian.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. È necessario riavviare JIRA/Confluence dopo aver distribuito questo componente aggiuntivo?

Non è necessario eseguire il riavvio dopo aver distribuito questo componente aggiuntivo. Dopo averlo distribuito, è possibile iniziare subito a usarlo.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Come è possibile ottenere assistenza per questo componente aggiuntivo?

È possibile contattare: <email>. Verrà fornita una risposta entro <> ore. È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure. È possibile anche chiamare il numero : <Number> tra le <> e le <> durante i giorni feriali.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Questo componente aggiuntivo funziona anche con installazioni di JIRA e Confluence su sistemi Mac e Ubuntu?

Questo componente aggiuntivo è stato testato solo su installazioni di JIRA e Confluence in server Windows a 64 bit.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Questo componente aggiuntivo funziona anche con provider di identità diversi da Azure Active Directory?

No. Questo componente aggiuntivo funziona solo con Azure Active Directory.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Con quale versione di SAML funziona questo componente aggiuntivo?

Questo componente aggiuntivo funziona con SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. Questo componente aggiuntivo usa anche il provisioning?

No. Per il momento questo componente aggiuntivo fornisce solo accesso Single Sign-On basato su SAML 2.0. È quindi necessario che venga eseguito il provisioning dell'utente nell'applicazione prima di poter effettuare l'accesso Single Sign-On.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Sono supportate le versioni cluster di JIRA e Confluence dal componente aggiuntivo?

No. Questo componente aggiuntivo funziona solo con le versioni locali di JIRA and Confluence.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. Questo plug-in funziona con le versioni HTTP di JIRA e Confluence?

No. Questo componente aggiuntivo funziona solo con installazioni abilitate per HTTPS.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. È necessario acquistare una licenza del componente aggiuntivo?

No, è gratuito.