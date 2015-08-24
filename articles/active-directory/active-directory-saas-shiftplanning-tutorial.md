<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con ShiftPlanning | Microsoft Azure" description="Informazioni su come usare ShiftPlanning con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con ShiftPlanning
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=526797).
  
In questa esercitazione viene illustrata l'integrazione di Azure e ShiftPlanning. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di ShiftPlanning abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ShiftPlanning saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di ShiftPlanning (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per ShiftPlanning
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per ShiftPlanning
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per ShiftPlanning.

###Per abilitare l'integrazione dell'applicazione per ShiftPlanning, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **ShiftPlanning**.

    ![Raccolta di applicazioni](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **ShiftPlanning**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a ScreenSteps tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **ShiftPlanning** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a ShiftPlanning** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso a ShiftPlanning** usando il modello seguente "**https://company.shiftplanning.com/includes/saml/*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in ShiftPlanning** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di **ShiftPlanning** come amministratore.

6.  Nel menu in alto fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Amministratore")

7.  In **Integrazione** fare clic su **Single Sign-On**.

    ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  Nella sezione **Single Sign-On**, eseguire la procedura seguente:

    ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  Selezionare **Abilitato SAML**.
    2.  Selezionare **Consenti accesso tramite password**
    3.  Nella finestra di dialogo **Configura accesso Single Sign-On in ShiftPlanning** del portale di Azure copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **URL autorità di certificazione SAML**.
    4.  Nella finestra di dialogo **Configura accesso Single Sign-On in ShiftPlanning** del portale di Azure copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **URL disconnessione remota**.
    5.  Creare un file **con codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
    7.  Fare clic su **Salva impostazioni**.

9.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a ShiftPlanning, è necessario eseguirne il provisioning in ShiftPlanning. Nel caso di ShiftPlanning, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, eseguire la procedura seguente:

1.  Accedere al sito aziendale di **ShiftPlanning** come amministratore.

2.  Fare clic su **Admin**.

    ![Amministratore](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Amministratore")

3.  Fare clic su **Personale**.

    ![Personale](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Personale")

4.  In **Azioni** fare clic su **Aggiungi dipendente**.

    ![Aggiungi dipendenti](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Aggiungi dipendenti")

5.  Nella sezione **Aggiungi dipendenti**, eseguire la procedura seguente:

    ![Salva dipendenti](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Salva dipendenti")

    1.  Digitare il **nome**, il **cognome** e l’**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
    2.  Fare clic su **Salva dipendenti**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da ShiftPlanning per eseguire il provisioning degli account utente di Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a ShiftPlanning, eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **ShiftPlanning** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->