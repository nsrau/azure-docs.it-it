---
title: Informazioni su Azure Single Sign-On
description: Informazioni sul funzionamento dell'accesso Single Sign-On (SSO) con Azure Active Directory. Usare l'accesso SSO in modo che gli utenti non debbano ricordare le password per ogni applicazione. L'accesso SSO consente anche di semplificare l'amministrazione della gestione degli account.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperfq1
ms.openlocfilehash: 1eaef57f46bf6373fdd2a73575bb028904ef108b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90561651"
---
# <a name="what-is-single-sign-on-sso"></a>Informazioni sull'accesso Single Sign-On (SSO)

Single Sign-on, ovvero accesso singolo, significa che un utente non deve eseguire l'accesso a ogni applicazione usata. L'utente esegue l'accesso una sola volta e le credenziali vengono usate anche per le altre app.

A un utente finale probabilmente i dettagli dell'accesso SSO non interessano molto. L'unico desiderio è usare le app per la produttività senza dover digitare ogni volta la password. Le app sono disponibili in: https://myapps.microsoft.com.
 
Se si è un amministratore o un professionista IT, le informazioni seguenti consentono di saperne di più sull'accesso SSO e su come viene implementato in Azure.

## <a name="single-sign-on-basics"></a>Nozioni di base sull'accesso Single Sign-On
L'accesso Single Sign-On offre notevoli vantaggi per quanto riguarda le modalità di accesso alle applicazioni e il loro utilizzo da parte degli utenti. Per i sistemi di autenticazione basati su Single Sign-on si parla spesso di "autenticazione moderna". L'autenticazione moderna e l'accesso Single Sign-On rientrano in una categoria di funzionalità di elaborazione denominata gestione delle identità e degli accessi (IAM). Per comprendere ciò che rende possibile l'accesso Single Sign-On, vedere questo video.

Concetti fondamentali sull'autenticazione: nozioni di base | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Accesso Single Sign-On con applicazioni Web
Le applicazioni Web sono estremamente diffuse. Le app Web sono ospitate da diverse aziende e rese disponibili come servizio. Alcuni esempi comuni di app Web sono Microsoft 365, GitHub e Salesforce e ne sono disponibili migliaia di altre. Gli utenti accedono alle app Web usando un Web browser sul computer. L'accesso Single Sign-On consente agli utenti di spostarsi tra le varie app Web senza dover eseguire l'accesso più volte.

Per informazioni sul funzionamento dell'accesso Single Sign-On con le app Web, vedere questi due video.

Concetti fondamentali sull'autenticazione: applicazioni Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Concetti fondamentali sull'autenticazione: Single Sign-On Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>App ospitate in locale o nel cloud
Il modo in cui si implementa l'accesso Single Sign-On dipende da dove è ospitata l'app. L'hosting è importante perché influisce sul modo in cui il traffico di rete viene instradato per accedere all'app. Se un'app è ospitata nella rete locale e vi si accede da tale rete, ovvero se è un'app locale, non è necessario che gli utenti accedano a Internet per usare l'app. Se l'app è ospitata in un'altra posizione, ovvero è un'app ospitata nel cloud, gli utenti devono accedere a Internet per usarla.

> [!TIP]
> Le app ospitate nel cloud sono dette anche app SaaS (Software as a Service). 

L'accesso Single Sign-on per le app ospitate nel cloud è semplice. Si indica al provider di identità che verrà usato per l'app e quindi si configura l'app in modo che consideri attendibile il provider di identità. Per informazioni su come usare Azure AD come provider di identità per un'app, vedere la [serie di guide di avvio rapido sulla gestione delle applicazioni](add-application-portal.md).

> [!TIP]
> I termini cloud e Internet vengono spesso usati in modo interscambiabile. Il motivo ha a che fare con i diagrammi di rete. È comune denotare le reti di computer di grandi dimensioni con una forma di nuvoletta in un diagramma perché non è possibile disegnare ogni componente. Internet è la rete più conosciuta ed è quindi facile usare i termini in modo interscambiabile. Tuttavia, qualsiasi rete di computer può essere trasformata in un cloud.

È anche possibile usare l'accesso Single Sign-On per le app locali. La tecnologia per l'accesso SSO in locale è denominata Application Proxy. Per altre informazioni, vedere [Opzioni per l'accesso Single Sign-On](sso-options.md).

## <a name="multiple-identity-providers"></a>Più provider di identità
La configurazione dell'accesso Single Sign-On per l'uso tra più provider di identità è denominata federazione. Per informazioni sul funzionamento della federazione, vedere questo video.

Concetti fondamentali sull'autenticazione: federazione | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Opzioni di accesso Single Sign-On](sso-options.md)
