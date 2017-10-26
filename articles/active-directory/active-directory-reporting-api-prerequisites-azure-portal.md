---
title: Prerequisiti di accesso all'API di creazione report di Azure AD | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e233547ce2add2d3eab1e9c46c4d7e701df72495
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web. 

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza
- Amministrazione della protezione
- Amministratore globale


Per preparare l'accesso all'API di creazione report, è necessario:

1. Registrare un'applicazione 
2. Concedere le autorizzazioni 
3. Ottenere le impostazioni di configurazione 

Per domande, problemi o suggerimenti, [inviare un ticket di supporto](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Registrare un'applicazione Azure Active Directory

È necessario registrare un'app, anche se si accede all'API di creazione dei report tramite uno script. Questo consente di avere un **ID applicazione** necessario per una chiamata di autorizzazione e consente al codice di ricevere i token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al portale di Azure con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi "admin" come questa possono essere molto potenti, quindi assicurarsi di proteggere l'ID e il segreto dell'applicazione.
> 


**Per registrare un'applicazione Azure Active Directory:**

1. Nel [portale di Azure ](https://portal.azure.com)fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nel pannello **Azure Active Directory** fare clic su **Registrazioni per l'app**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Nel pannello **Registrazioni per l'app** nella barra degli strumenti nella parte superiore fare clic su **Registrazione nuova applicazione**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Nel pannello **Crea** seguire questa procedura:

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **Tipo di applicazione** selezionare **App Web/API**.

    c. Nella casella di testo **URL di accesso** digitare `https://localhost`.

    d. Fare clic su **Crea**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

L'obiettivo di questo passaggio è concedere le autorizzazioni **Legge i dati della directory** dell'applicazione all'API di **Windows Azure Active Directory**.

![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Per concedere l'autorizzazione dell'applicazione per l'uso dell'API:**

1. Nel pannello **Registrazioni per l'app** dell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).

2. Nel pannello **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Impostazioni** nella barra degli strumenti nella parte superiore. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Nel pannello delle **Impostazioni** fare clic su **Autorizzazioni necessarie**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Nel pannello **Autorizzazioni necessarie** fare clic su Windows **Azure Active Directory** nell'elenco delle **API**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Nel pannello **Abilita accesso** selezionare **Legge i dati della directory**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Nel barra degli strumenti in alto fare clic su **Salva**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 
Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

* Nome di dominio
* ID client
* Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure ](https://portal.azure.com)fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nel pannello **Azure Active Directory** fare clic su **Nomi di dominio**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure ](https://portal.azure.com)fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nel pannello **Registrazioni per l'app** dell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).

3. Nel pannello **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Fare clic per copiare** nell'**ID applicazione**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
Per ottenere il segreto client dell'applicazione, è necessario creare una nuova chiave e salvare il relativo valore durante il salvataggio della nuova chiave perché non è più possibile recuperare il valore in un secondo momento.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure ](https://portal.azure.com)fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nel pannello **Registrazioni per l'app** dell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).


3. Nel pannello **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Impostazioni** nella barra degli strumenti nella parte superiore. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Nel pannello **Impostazioni**fare clic su **Chiavi**  nella sezione **APIR Access** (Accesso APIR). 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Nel pannello **Chiavi** seguire questa procedura:

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Salva**.

    d. Copiare il valore della chiave.


## <a name="next-steps"></a>Passaggi successivi
* Si desidera accedere ai dati dall'API di creazione report di Azure AD mediante il codice? Vedere [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).  

