---
title: Domande frequenti sul plug-in Single Sign-On di Azure Active Directory | Microsoft Docs
description: Risposte alle domande frequenti sulla configurazione del Single Sign-On tra Azure Active Directory e Jira/Confluence.
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
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699207"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Domande frequenti sul plug-in Single Sign-On di Azure Active Directory

Fare riferimento alle domande frequenti di seguito nel caso di query relative a questo plug-in.

## <a name="what-does-the-plug-in-do"></a>A cosa serve il plug-in?

Il plug-in fornisce funzionalità di accesso Single Sign-On per il software locale Atlassian Jira (tra cui JIRA Core, JIRA Software, JIRA Service Desk) e Confluence. Il plug-in funziona con Azure Active Directory (Azure AD) come provider di identità.

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Con quali prodotti Atlassian si può usare il plug-in?

Il plug-in funziona solo con le versioni locali di Jira e Confluence.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Il plug-in funziona con le versioni cloud?

di serie Il plug-in supporta solo le versioni locali di Jira e Confluence.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Quali sono le versioni di Jira e Confluence supportate dal plug-in?

Il plug-in supporta queste versioni:

* Jira Core e Software: da 6.0 a 7.8
* Jira Service Desk: da 3.0 a 3.2
* Confluence: da 5.0 a 5.10

## <a name="is-the-plug-in-free-or-paid"></a>Il plug-in è gratuito o a pagamento?

È un componente aggiuntivo gratuito.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>È necessario riavviare Jira o Confluence dopo aver distribuito questo plug-in?

Il riavvio non è necessario. È possibile iniziare a usare il plug-in immediatamente.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Come è possibile ottenere assistenza per questo plug-in?

È possibile contattare il [Team di integrazione di Azure Active Directory SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) per qualsiasi assistenza relativa a questo plug-in. Il team risponde in 24-48 ore.

È possibile anche aprire un ticket di assistenza con Microsoft tramite il canale del portale di Azure.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Il plug-in funziona anche con installazioni di Jira e Confluence su sistemi Mac e Ubuntu?

Questo plug-in è stato testato solo su installazioni di Jira e Confluence in Windows Server a 64 bit.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Il plug-in funziona con provider di identità diversi da Azure AD?

di serie Funziona solo con Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Con quale versione di SAML funziona questo plug-in?

Funziona con SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Il plug-in esegue il provisioning degli utenti?

di serie Il plug-in fornisce solo accesso Single Sign-On basato su SAML 2.0. È quindi necessario che venga eseguito il provisioning dell'utente nell'applicazione prima di poter effettuare l'accesso Single Sign-On.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Il plug-in supporta le versioni cluster di Jira e Confluence?

di serie Il plug-in funziona solo con le versioni locali di Jira e Confluence.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Il plug-in funziona con le versioni HTTP di Jira e Confluence?

di serie Questo plug-in funziona solo con installazioni abilitate per HTTPS.