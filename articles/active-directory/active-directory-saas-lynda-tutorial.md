<properties pageTitle="Esercitazione: integrazione di Azure Active Directory con Lynda.com | Microsoft Azure" description="Informazioni su come utilizzare Lynda.com con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Esercitazione: Integrazione di Azure Active Directory con Lynda.com
>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=524768).
  
In questa esercitazione viene illustrata l'integrazione di Azure e Lynda.com. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant Lynda.com
  
Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Lynda.com saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Lynda.com (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586)
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Lynda.com
2.  Configurazione dell'accesso Single Sign-On
3.  Configurazione del provisioning utente
4.  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")
##Abilitazione dell'integrazione dell'applicazione per Lynda.com
  
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Lynda.com.

###Per abilitare l'integrazione dell'applicazione per Lynda.com, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

    ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-lynda-tutorial/IC749321.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-lynda-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **Lynda.com**.

    ![Raccolta di applicazioni](./media/active-directory-saas-lynda-tutorial/IC777524.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **Lynda.com**, quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##Configurazione dell'accesso Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Lynda.com tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

>[AZURE.IMPORTANT]Per poter configurare l’accesso Single Sign-On nel tenant di Lynda.com è necessario prima contattare il supporto tecnico di Lynda.com per abilitare questa funzionalità.

###Per configurare l'accesso Single Sign-On, eseguire la procedura seguente:

1.  Nella pagina di integrazione dell'applicazione **Lynda.com** del portale di Azure AD fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a Lynda.com** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configura accesso Single Sign-On")

3.  Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Lynda.com** digitare l'URL del tenant Lynda.com (ad esempio: **https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*)), quindi fare clic su **Avanti**.

    ![Configura URL app](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configura URL app")

4.  Nella pagina **Configura accesso Single Sign-On in Lynda.com** fare clic su **Scarica metadati** per scaricare i metadati, quindi salvare il file di certificato localmente nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configura accesso Single Sign-On")

5.  Inviare il file di metadati scaricato al team di supporto di Lynda.com. Il team di supporto di Lynda.com esegue la configurazione dell’accesso Single Sign-On per l'utente.

6.  Nel portale di Azure AD, selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configura accesso Single Sign-On")
##Configurazione del provisioning utente
  
Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lynda.com. Quando un utente assegnato tenta di accedere a Lynda.com utilizzando il pannello di accesso, Lynda.com controlla se l'utente esiste. Se l’account utente non è ancora disponibile, viene creato automaticamente da Lynda.com.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Lynda.com o le API fornite da Lynda.com per effettuare il provisioning degli account utente di AAD.

##Assegnazione degli utenti
  
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

###Per assegnare gli utenti a Lynda.com eseguire la procedura seguente:

1.  Nel portale di Azure AD creare un account di test.

2.  Nella pagina di integrazione dell'applicazione **Lynda.com** fare clic su **Assegna utenti**.

    ![Assegna utenti](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assegna utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**, quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì](./media/active-directory-saas-lynda-tutorial/IC767830.png "Sì")
  
Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->