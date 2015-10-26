<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con PolicyStat | Microsoft Azure" description="Informazioni su come usare PolicyStat con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con PolicyStat
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=616313).
  
In questa esercitazione viene illustrata l'integrazione di Azure e PolicyStat. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant PolicyStat
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a PolicyStat saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di PolicyStat (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per PolicyStat
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per PolicyStat
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per PolicyStat.

###Per abilitare l'integrazione dell'applicazione per PolicyStat, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-policystat-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-policystat-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **PolicyStat**.

    ![Raccolta di applicazioni](./media/active-directory-saas-policystat-tutorial/IC808627.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **PolicyStat**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a PolicyStat tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML. L'applicazione PolicyStat prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**. La schermata seguente mostra un esempio relativo a questa operazione.

![Attributi](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributi")

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **PolicyStat** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a PolicyStat** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configurare le impostazioni dell'app** digitare nella casella di testo **URL di accesso** l'URL usato dagli utenti per accedere all'applicazione PolicyStat (ad esempio, *“https://demo-azure.policystat.com”*), quindi fare clic su **Avanti**.

    ![Configurare le impostazioni dell'app](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurare le impostazioni dell'app")

4.  Nella pagina **Configura accesso Single Sign-On in PolicyStat** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di PolicyStat come amministratore.

6.  Scegliere la scheda **Admin**, quindi fare clic su **Configurazione di Single Sign-On** nel riquadro di spostamento a sinistra.

    ![Menu Amministratore](./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu Amministratore")

7.  Nella sezione **Impostazione** selezionare **Abilitare integrazione di Single Sign-On**.

    ![Configurazione di Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808634.png "Configurazione di Single Sign-On")

8.  Fare clic su **Configura attributi** e nella sezione **Configura attributi**, eseguire la procedura seguente:

    ![Configurazione di Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione di Single Sign-On")

    1.  Nella casella di testo **Attributo nome utente** digitare **uid**.
    2.  Nella casella di testo **Attributo nome** digitare **firstname**.
    3.  Nella casella di testo **Attributo cognome** digitare **lastname**.
    4.  Nella casella di testo **Attributo posta elettronica** digitare **emailaddress**.
    5.  Fare clic su **Salva modifiche**.

9.  Fare clic su **Metadati del provider di identità** quindi nella sezione **Metadati del provider di identità**, eseguire la procedura seguente:

    ![Configurazione di Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione di Single Sign-On")

    1.  Aprire il file dei metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Metadati del provider di identità**.
    2.  Fare clic su **Salva modifiche**.

10. Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configura accesso Single Sign-On")

11. 12. Nel menu in alto fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.

    ![Attributi](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributi")

13. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    ![Attributi](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributi")

    1.  Fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo**, digitare **uid**.
    3.  Nella casella di testo **Valore attributo**, selezionare **ExtractMailPrefix()**.
    4.  Dall’elenco **Posta**, selezionare **User.mail**.
    5.  Fare clic su **Complete**.
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a PolicyStat, è necessario eseguirne il provisioning in PolicyStat. PolicyStat supporta solo il provisioning JIT dell'utente. Non è pertanto è necessario aggiungere gli utenti manualmente a PolicyStat. Gli utenti verranno vengono aggiunti automaticamente al primo accesso tramite il Single Sign-On.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da PolicyStat per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a PolicyStat eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **PolicyStat** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-policystat-tutorial/IC808636.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-policystat-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->