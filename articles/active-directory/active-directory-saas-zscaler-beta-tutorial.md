<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta | Microsoft Azure" description="Informazioni su come usare Zscaler Beta con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=614877).
  
Questa esercitazione descrive l'integrazione di Azure e Zscaler Beta. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Zscaler Beta abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zscaler Beta potranno accedere all'applicazione tramite il sito aziendale di Zscaler Beta (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Zscaler Beta
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione delle impostazioni proxy
4.  Configurazione del provisioning utente
5.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "Scenario")

##Abilitazione dell'integrazione dell'applicazione per Zscaler Beta
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zscaler Beta.

###Per abilitare l'integrazione dell'applicazione per Zscaler Beta, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione](./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Zscaler Beta**.

    ![Raccolta di applicazioni](./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Zscaler Beta** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Zscaler One](./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "Zscaler One")

##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zscaler Beta tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 sul proprio tenant Zscaler Beta. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Zscaler Beta** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Zscaler Beta** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL di accesso a Zscaler Beta** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all’applicazione Zscaler Beta e quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "Configura URL app")

    >[AZURE.NOTE]Se necessario, è possibile ottenere il valore effettivo per l'ambiente in uso dal team di supporto Zscaler Beta.

4.  Nella pagina **Configura accesso Single Sign-On in Zscaler Beta** fare clic su **Scarica certificato** e quindi salvare il file di certificato sul computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Zscaler Beta come amministratore.

6.  Scegliere **Administration** dal menu disponibile nella parte superiore.

    ![Amministrazione](./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "Amministrazione")

7.  In **Manage Administrators & Roles** fare clic su **Manage Users & Authentication**.

    ![Manage Users & Authentication](./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "Manage Users & Authentication")

8.  Nella sezione **Choose Authentication Options for your Organization** seguire questa procedura:

    ![Autenticazione](./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "Autenticazione")

    1.  Selezionare **Authenticate using SAML Single Sign-On**.
    2.  Fare clic su **Configure SAML Single Sign-On Parameters**.

9.  Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** procedere come descritto di seguito e quindi fare clic su **Done**:

    ![Single Sign-On](./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "Single Sign-On")

    1.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Zscaler Beta** del portale di Azure, copiare il valore **URL richiesta di autenticazione** e quindi incollarlo nella casella di testo **URL of the SAML Portal to which users are sent for authentication**.
    2.  Nella casella di testo **Attribute containing Login Name** digitare **NameID**.
    3.  Per caricare il certificato scaricato fare clic su **Zscaler pem**.
    4.  Selezionare **Enable SAML Auto-Provisioning**.

10. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Amministrazione](./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "Amministrazione")

    1.  Fare clic su **Save**.
    2.  Fare clic su **Activate Now**.

11. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Zscaler Beta** del portale di Azure selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Configura accesso Single Sign-On")

##Configurazione delle impostazioni proxy

###Per configurare le impostazioni proxy in Internet Explorer

1.  Avviare **Internet Explorer**.

2.  Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.

    ![Opzioni Internet](./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "Opzioni Internet")

3.  Fare clic sulla scheda **Connessioni**.

    ![Connessioni](./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "Connessioni")

4.  Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5.  Nella sezione del server proxy seguire questa procedura:

    ![Server proxy](./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "Server proxy")

    1.  Selezionare Usa un server di proxy per la rete LAN.
    2.  Nella casella di testo Indirizzo digitare **gateway.zscalerBeta.net**.
    3.  Nella casella di testo Porta digitare **80**.
    4.  Selezionare **Ignora server proxy per indirizzi locali**.
    5.  Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

6.  Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Zscaler Beta, è necessario eseguirne il provisioning in Zscaler Beta. Nel caso di Zscaler Beta, il provisioning è un'attività manuale.

###Per configurare il provisioning utente, seguire questa procedura:

1.  Accedere al tenant **Zscaler**.

2.  Fare clic su **Administration**.

    ![Amministrazione](./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "Amministrazione")

3.  Fare clic su **User Management**.

    ![Aggiungi](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Aggiungi")

4.  Nella scheda **Users** fare clic su **Add**.

    ![Add](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Add")

5.  Nella sezione Add User seguire questa procedura:

    ![Add User](./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "Add User")

    1.  Digitare **UserID**, **User Display Name**, **Password** e **Confirm Password** e quindi selezionare **Groups** e **Department** di un account Azure AD valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zscaler Beta per eseguire il provisioning degli account utente Azure AD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

###Per assegnare gli utenti a Zscaler Beta, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell’applicazione **Zscaler Beta** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "Sì")
  
Se si desidera testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->