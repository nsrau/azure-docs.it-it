<properties pageTitle="Esercitazione: Integrazione di Azure Active Directory con Egnyte | Microsoft Azure" description="Informazioni su come usare Egnyte con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Egnyte
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=528188).
  
Questa esercitazione descrive l'integrazione di Azure ed Egnyte.
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Sottoscrizione di Egnyte abilitata per l'accesso Single Sign-On
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Egnyte potranno accedere all'applicazione tramite il sito aziendale di Egnyte (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Egnyte
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Egnyte
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Egnyte.

###Per abilitare l'integrazione dell'applicazione per Egnyte, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **egnyte**.

    ![Raccolta di applicazioni](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Egnyte** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##Configurazione dell'accesso Single Sign-On
  
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Egnyte tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###Per configurare l'accesso Single Sign-On, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Egnyte** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Egnyte** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configura accesso Single Sign-On")

3.  Nella casella di testo **URL accesso Egnyte** della pagina **Configura URL app** digitare l'URL usando il modello seguente "**https://company.egnyte.com*" e fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Egnyte** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configura accesso Single Sign-On")

5.  In un'altra finestra del Web browser accedere al sito aziendale di Egnyte come amministratore.

6.  Fare clic su **Settings**.

    ![Impostazioni](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Impostazioni")

7.  Scegliere **Settings** dal menu.

    ![Impostazioni](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Impostazioni")

8.  Fare clic sulla scheda **Configuration** e quindi fare clic su **Security**.

    ![Sicurezza](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Sicurezza")

9.  Nella sezione **Single Sign-On Authentication** seguire questa procedura:

    ![Single Sign On Authentication](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")

    1.  In **Single sign-on authentication** selezionare **SAML 2.0**.
    2.  In **Identity provider** selezionare **AzureAD**.
    3.  Nella pagina **Configura accesso Single Sign-On in Egnyte** nel portale di Azure copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity provider login URL**.
4.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Egnyte** nel portale di Azure copiare il valore di **ID entità** e quindi incollarlo nella casella di testo **Identity provider entity ID**.
    5.  Creare un file con **codifica Base 64** dal certificato scaricato.  

        >[AZURE.TIP]Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    6.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Identity provider certificate**.
    7.  In **Default user mapping** selezionare **Email address**.
    8.  In **Use domain-specific issuer value** selezionare **disabled**.
    9.  Fare clic su **Save**.

10. Nel portale di Azure AD selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Per consentire agli utenti di Azure AD di accedere a Egnyte, è necessario eseguirne il provisioning in Egnyte. Nel caso di Egnyte, il provisioning è un'attività manuale.

###Per eseguire il provisioning di un account utente, seguire questa procedura:

1.  Accedere al sito aziendale di **Egnyte** come amministratore.

2.  Passare a **Settings > Users & Groups**.

3.  Fare clic su **Add New User** e quindi selezionare il tipo di utente da aggiungere.

    ![Utenti](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Utenti")

4.  Nella sezione **New Standard User** seguire questa procedura:

    ![New Standard User](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")

    1.  Immettere i valori **Email**, **Username** e altri dettagli di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
    2.  Fare clic su **Save**.

    >[AZURE.NOTE]Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica di notifica.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Egnyte per eseguire il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Egnyte, seguire questa procedura:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Egnyte** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna** e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->