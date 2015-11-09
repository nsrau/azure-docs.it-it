<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software | Microsoft Azure" 
    description="Informazioni su come utilizzare OfficeSpace Software con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Esercitazione: Integrazione di Azure Active Directory con OfficeSpace Software
  
In questa esercitazione viene illustrata l'integrazione di Azure e OfficeSpace Software. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di OfficeSpace Software abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a OfficeSpace Software saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di OfficeSpace Software (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per OfficeSpace Software
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per OfficeSpace Software
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per OfficeSpace Software.

###Per abilitare l'integrazione dell'applicazione per OfficeSpace Software, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **OfficeSpace Software**.

    ![Raccolta di applicazioni](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **OfficeSpace Software**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a OfficeSpace Software tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per OfficeSpace Software richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **OfficeSpace Software** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign=On](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configura accesso Single Sign=On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a OfficeSpace Software** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a OfficeSpace Software** digitare l'URL utilizzato dagli utenti per accedere all’applicazione OfficeSpace Software (ad esempio: "**https://company.officespacesoftware.com*")), quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in OfficeSpace Software**, fare clic su **Download certificato** per scaricare il file del certificato, quindi salvarlo localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di OfficeSpace Software come amministratore.

6.  Passare a **Admin > Connettori**.

    ![Amministratore](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Amministratore")

7.  Fare clic su **Autorizzazione SAML**.

    ![Connettori](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connettori")

8.  Nella sezione **Autorizzazione SAML** eseguire la procedura seguente:

    ![Configurazione SAML](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configurazione SAML")

    1.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in OfficeSpace Software** del portale di Azure, copiare il valore **URL accesso remoto** e incollarlo nella casella di testo **URL provider disconnessione**.
    2.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in OfficeSpace Software** del portale di Azure copiare il valore **URL disconnessione remota** e incollarlo nella casella di testo **URL di destinazione client IDP**.
    3.  Copiare il valore di **Identificazione personale** dal certificato esportato e incollarlo nella casella di testo relativa all'**impronta digitale del certificato client IDP**.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

    4.  Fare clic su **Salva impostazioni**.

9.  Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a OfficeSpace Software, è necessario eseguirne il provisioning in OfficeSpace Software. Nel caso di OfficeSpace Software, il provisioning è un'attività automatica. Non è necessario eseguire alcuna operazione. Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di OfficeSpace Software o le API fornite da OfficeSpace Software per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a OfficeSpace Software eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **OfficeSpace Software** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->