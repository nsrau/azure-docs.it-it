---
title: Prerequisiti di accesso all'API di creazione report di Azure AD | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ab05907f1f23c3856b41a941c1b95992ed5a79a4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929983"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web.

Per preparare l'accesso all'API di creazione report, è necessario:

1. Assegnare ruoli
2. Registrare un'applicazione
3. Concedere le autorizzazioni
4. Ottenere le impostazioni di configurazione



## <a name="assign-roles"></a>Assegnare ruoli

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza

- Amministrazione della protezione

- Amministratore globale




## <a name="register-an-application"></a>Registrare un'applicazione

È necessario registrare un'app, anche se si accede all'API di creazione dei report tramite uno script. Questo consente di avere un **ID applicazione** necessario per una chiamata di autorizzazione e consente al codice di ricevere i token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al portale di Azure con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi "admin" come questa possono essere molto potenti, quindi assicurarsi di proteggere l'ID e il segreto dell'applicazione.
> 


**Per registrare un'applicazione Azure Active Directory:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nella pagina **Azure Active Directory** fare clic su **Registrazioni per l'app**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Nella pagina **Registrazioni per l'app**, nella barra degli strumenti nella parte superiore, fare clic su **Registrazione nuova applicazione**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Nella pagina **Crea** seguire la procedura seguente:

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **Tipo di applicazione** selezionare **App Web/API**.

    c. Nella casella di testo **URL di accesso** digitare `https://localhost`.

    d. Fare clic su **Crea**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

A seconda delle API a cui si desidera accedere, è necessario concedere all'app le autorizzazioni seguenti:  

| API | Autorizzazione |
| --- | --- |
| Microsoft Azure Active Directory | Leggi i dati della directory |
| Microsoft Graph | Leggere tutti i dati dei log di controllo |


![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


Nella sezione seguente viene elencata la procedura per entrambe le API. Se non si desidera accedere a una delle API, è possibile ignorare le procedure relative.
 

**Per concedere l'autorizzazione dell'applicazione per l'uso delle API:**

1. Nella pagina **Registrazioni per l'app** nell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).

2. Nella pagina **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Impostazioni** nella barra degli strumenti nella parte superiore. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Nella pagina **Impostazioni** fare clic su **Autorizzazioni necessarie**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Nella pagina **Autorizzazioni necessarie** fare clic su Windows **Azure Active Directory** nell'elenco delle **API**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Nella pagina **Abilita l'accesso**, selezionare **Lettura dati directory** e deselezionare **Accedi e leggi il profilo di un altro utente**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Nel barra degli strumenti in alto fare clic su **Salva**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Nella pagina **Autorizzazioni necessarie** fare clic su **Aggiungi** sulla barra degli strumenti in alto.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Nella pagina **Selezionare un'API** fare clic su **Microsoft Graph** e quindi fare clic su **Seleziona**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Nella pagina **Abilita l'accesso**, selezionare **Leggere tutti i dati del log di controllo**, quindi fare clic su **Seleziona**.  

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Nella pagina **Aggiungi accesso all'API** fare clic su **Fatto**.  

12. Nella pagina **Autorizzazioni necessarie**, sulla barra degli strumenti in alto. fare clic su **Concedi autorizzazioni** e quindi su **Sì**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 

Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nella pagina **Azure Active Directory** fare clic su **Nomi di dominio personalizzati**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nella pagina **Registrazioni per l'app** nell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).

3. Nella pagina **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Fare clic per copiare** nell'**ID applicazione**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
Per ottenere il segreto client dell'applicazione, è necessario creare una nuova chiave e salvare il relativo valore durante il salvataggio della nuova chiave perché non è più possibile recuperare il valore in un secondo momento.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Nella pagina **Registrazioni per l'app** nell'elenco delle app fare clic su **Reporting API application** (Creazione di report per l'applicazione dell'API).


3. Nella pagina **Reporting API application** (Creazione di report per l'applicazione dell'API) fare clic su **Impostazioni** nella barra degli strumenti nella parte superiore. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Nella pagina **Impostazioni**fare clic su **Chiavi**  nella sezione **APIR Access** (Accesso APIR). 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Nel pagina **Chiavi** seguire questa procedura:

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Save**.

    d. Copiare il valore della chiave.


## <a name="next-steps"></a>Passaggi successivi

- [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](active-directory-reporting-api-with-certificates.md).

- [Ottenere una prima impressione delle API di creazione report](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Creare una soluzione personalizzata](active-directory-reporting-api-getting-started-azure-portal.md#customize)

