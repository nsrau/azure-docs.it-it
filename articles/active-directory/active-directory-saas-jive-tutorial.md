<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jive | Microsoft Azure" 
    description="Informazioni su come usare Jive con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con Jive

  
Questa esercitazione descrive l'integrazione di Azure e Jive. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant in Jive
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Jive
2.  Configurazione del provisioning utente

##Abilitazione dell'integrazione dell'applicazione per Jive
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Jive.

###Per abilitare l'integrazione dell'applicazione per Jive, seguire questa procedura:

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-jive-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app**, quindi fare clic su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.

    ![Come procedere](./media/active-directory-saas-jive-tutorial/IC700995.png "Come procedere")

5.  Nella **casella di ricerca** digitare **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  Nel riquadro dei risultati selezionare **Jive** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Configurazione del provisioning utente
  
Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Jive. Come parte di questa procedura, specificare un token di sicurezza utente da richiedere a Jive.com.
  
La schermata seguente mostra un esempio della finestra di dialogo correlata in Azure AD:

![Configura provisioning utenti](./media/active-directory-saas-jive-tutorial/IC698794.png "Configura provisioning utenti")

###Per configurare il provisioning utente, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Jive** nel portale di Azure classico fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.

2.  Nella pagina **Immettere le credenziali Jive per abilitare il provisioning utenti automatico** specificare le impostazioni di configurazione seguenti:

    1.  Nella casella di testo **Nome utente amministratore Jive** digitare un nome di account Jive che abbia il profilo **Amministratore di sistema** assegnato in Jive.com.

    2.  Nella casella di testo **Password amministratore Jive** digitare la password per questo account.

    3.  Nella casella di testo **URL tenant di Jive** digitare l'URL del tenant di Jive.

        >[AZURE.NOTE] L'URL del tenant di Jive è l'URL usato dall'organizzazione per accedere a Jive. L'URL in genere ha il formato seguente: **www.<organizzazione>.jive.com**.

    4.  Fare clic su **Convalida** per verificare la configurazione.

    5.  Fare clic sul pulsante **Avanti** per aprire la pagina **Conferma**.

3.  Nella pagina **Conferma** fare clic sul segno di spunta per salvare la configurazione.
  
È ora possibile creare un account di test. Attendere 10 minuti e quindi verificare che l'account sia stato sincronizzato con Jive.com.

<!---HONumber=AcomDC_0713_2016-->