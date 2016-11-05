---
title: Prerequisiti di accesso all'API di creazione report di Azure AD. | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi

---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD
Le [API di creazione report di Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web. 

Per preparare l'accesso all'API di creazione report, è necessario:

1. Creare un'applicazione nel tenant di Azure AD 
2. Concedere le autorizzazioni appropriate all'applicazione per l'accesso ai dati di Azure AD
3. Raccogliere le impostazioni di configurazione dalla directory

Per domande, problemi o suggerimenti, contattare la [Guida per la creazione di report AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="create-an-azure-ad-application"></a>Creare un'applicazione Azure AD
Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al portale di gestione con un account di amministratore di sottoscrizione di Azure che è anche membro del ruolo di directory Amministratore globale nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi "admin" come questa possono essere molto potenti, quindi assicurarsi di proteggere l'ID e il segreto dell'applicazione.
> 
> 

1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Nell'elenco **Active Directory** selezionare la directory.
3. Scegliere **Applicazioni**dal menu in alto.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Nella barra inferiore fare clic su **Aggiungi**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/03.png) 
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**. 
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/04.png) 
6. Nella finestra di dialogo **Informazioni sull'applicazione** seguire questa procedura: 
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a. Nella casella di testo **Nome** digitare un nome (ad esempio: Applicazione dell'API di creazione report).
   
    b. Selezionare **Applicazione Web e/o API Web**.
   
    c. Fare clic su **Avanti**.
7. Nella finestra di dialogo **Proprietà dell'app** seguire questa procedura: 
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a. Nella casella di testo **URL di accesso** digitare `https://localhost`.
   
    b. Nella casella di testo **URI ID app** digitare ```https://localhost/ReportingApiApp```.
   
    c. Fare clic su **Completa**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere all'applicazione le autorizzazioni per l'uso dell'API
1. Nel [portale di Azure classico](https://manage.windowsazure.com/)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Nell'elenco **Active Directory** selezionare la directory.
3. Scegliere **Applicazioni**dal menu in alto.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png)
4. Nell'elenco delle applicazioni selezionare l'applicazione appena creata.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)
5. Nel menu in alto fare clic su **Configura**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)
6. Nella sezione **Autorizzazioni per altre applicazioni** per la risorsa **Azure Active Directory** fare clic sull'elenco a discesa **Autorizzazioni applicazione** e selezionare **Lettura dati directory**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/09.png)
7. Nella barra inferiore fare clic su **Salva**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>Raccogliere le impostazioni di configurazione dalla directory
Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

* Nome di dominio
* ID client
* Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio
1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Nell'elenco **Active Directory** selezionare la directory.
3. Nel menu in alto fare clic su **Domini**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/11.png) 
4. Nella colonna **Nome di dominio** copiare il nome di dominio.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-application's-client-id"></a>Ottenere l'ID client dell'applicazione
1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Nell'elenco **Active Directory** selezionare la directory.
3. Scegliere **Applicazioni**dal menu in alto.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Nell'elenco delle applicazioni selezionare l'applicazione appena creata.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)
5. Nel menu in alto fare clic su **Configura**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)
6. Copiare l' **ID client**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-application's-client-secret"></a>Ottenere il segreto client dell'applicazione
Per ottenere il segreto client dell'applicazione, è necessario creare una nuova chiave e salvare il relativo valore durante il salvataggio della nuova chiave perché non è più possibile recuperare il valore in un secondo momento.

1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Nell'elenco **Active Directory** selezionare la directory.
3. Scegliere **Applicazioni**dal menu in alto.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Nell'elenco delle applicazioni selezionare l'applicazione appena creata.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)
5. Nel menu in alto fare clic su **Configura**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)
6. Nella sezione **Chiavi** eseguire la procedura seguente: 
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a. Nell'elenco delle durate selezionare una durata
   
    b. Nella barra inferiore fare clic su **Salva**.
   
    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. Copiare il valore della chiave.

## <a name="next-steps"></a>Passaggi successivi
* Si desidera accedere ai dati dall'API di creazione report di Azure AD mediante il codice? Vedere [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).  

<!--HONumber=Oct16_HO2-->


