<properties 
    pageTitle="Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso | Microsoft Azure" 
    description="Informazioni su come usare la sincronizzazione in entrata con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso
>[AZURE.NOTE]Azure Active Directory (AD) Premium sono disponibili per i clienti in Cina che usano l'istanza globale di Azure AD. Azure AD Premium non è attualmente supportato nel servizio Microsoft Azure gestito da 21Vianet in Cina.

Questa esercitazione descrive i passaggi da eseguire in Workday e Microsoft Azure AD per importare utenti da Workday in Microsoft Azure AD. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure valida
-   Tenant di Workday

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1.  Abilitazione dell'integrazione dell'applicazione per Workday
2.  Creazione di un utente del sistema di integrazione
3.  Creazione di un gruppo di sicurezza
4.  Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza
5.  Configurazione delle opzioni del gruppo di sicurezza
6.  Attivazione delle modifiche apportate ai criteri di sicurezza
7.  Configurazione dell'importazione di utenti in Microsoft Azure AD

##Abilitazione dell'integrazione dell'applicazione per Workday

Questa sezione descrive come abilitare l'integrazione dell'applicazione per Salesforce.

###Per abilitare l'integrazione dell'applicazione per Workday, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel pannello di navigazione sinistro.

	![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app**, quindi fare clic su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.

	![Come procedere](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "Come procedere")

5.  Nella **casella di ricerca** digitare **Workday**.

	![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")

6.  Nel riquadro dei risultati selezionare **Workday** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")

##Creazione di un utente del sistema di integrazione

1.  In **Workbench Workday** immettere **crea utente** nella casella di ricerca, quindi fare clic sul collegamento **Crea utente del sistema di integrazione**.

	![crea utente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "crea utente")

2.  Completare l'attività Crea utente del sistema di integrazione specificando un nome utente e una password per un nuovo utente del sistema di integrazione. Lasciare l'opzione Richiedi nuova password al prossimo accesso non selezionata, perché l'accesso dell'utente verrà eseguito a livello di codice. Lasciare l'opzione Minuti di timeout della sessione impostata sul valore predefinito 0, in modo da evitare un timeout prematuro delle sessioni dell'utente.

	![Crea utente del sistema di integrazione](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Create Integration System User")

##Creazione di un gruppo di sicurezza

Per lo scenario descritto in questa esercitazione, è necessario creare un gruppo di sicurezza del sistema di integrazione non vincolato e assegnare l'utente a tale gruppo.

1.  Immettere "crea gruppo di sicurezza" nella casella di ricerca, quindi fare clic sul collegamento Crea gruppo di sicurezza.

	![Create Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Create Security Group")

2.  Completare l'attività Crea gruppo di sicurezza. Selezionare Gruppo di sicurezza del sistema di integrazione - Non vincolato dall'elenco a discesa Tipo di gruppo di sicurezza con tenant per creare un gruppo di sicurezza a cui i membri verranno aggiunti esplicitamente.

	![Create Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Create Security Group")

##Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza

1.  Immettere "modifica gruppo di sicurezza" nella casella di ricerca, quindi fare clic sul collegamento **Modifica gruppo di sicurezza**.

	![Modifica gruppo di sicurezza](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Edit Security Group")

2.  Eseguire la ricerca e selezionare il nuovo gruppo di sicurezza di integrazione in base al nome.

	![Edit Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Modifica gruppo di sicurezza")

3.  Aggiungere il nuovo utente del sistema di integrazione al nuovo gruppo di sicurezza.

	![System Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System Security Group")

##Configurazione delle opzioni del gruppo di sicurezza

In questo passaggio, al nuovo gruppo di sicurezza vengono concesse le autorizzazioni per le operazioni Get e Put sugli oggetti protetti dai seguenti criteri di sicurezza del dominio:

-   Provisioning account esterno
-   Worker Data: Public Worker Reports
-   Worker Data: All Positions
-   Worker Data: Current Staffing Information
-   Dati lavoratore - Qualifica riportata sul profilo

&nbsp;

1.  Immettere "criteri di sicurezza del dominio" nella casella di ricerca, quindi fare clic sul collegamento Criteri di sicurezza del dominio per area funzionale.

	![Domain Security Policies](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domain Security Policies")

2.  Cercare "sistema" e selezionare Area funzionale sistema. Fare clic sul pulsante OK.

	![Domain Security Policies](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domain Security Policies")

3.  Nell'elenco dei criteri di sicurezza relativi ad Area funzionale sistema espandere Amministrazione sicurezza e selezionare il criterio di sicurezza del dominio Provisioning account esterno.

	![Domain Security Policies](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domain Security Policies")

4.  Fare clic sul pulsante Modifica autorizzazioni quindi, nella schermata Modifica autorizzazioni, aggiungere il nuovo gruppo di sicurezza all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione Get e Put.

	![Edit Permissions](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Edit Permissions")

5.  Ripetere il passaggio 1 sopra descritto per tornare alla schermata di selezione delle aree funzionali, cercare "personale", selezionare l'area funzionale Personale e fare clic sul pulsante OK.

	![Domain Security Policies](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domain Security Policies")

6.  Nell'elenco dei criteri di sicurezza relativi all'area funzionale Personale espandere Dati lavoratore - Personale e ripetere il passaggio 4 sopra descritto per ciascuno dei rimanenti criteri di sicurezza:

	-   Worker Data: Public Worker Reports
    -   Worker Data: All Positions
    -   Worker Data: Current Staffing Information
    -   Dati lavoratore - Qualifica riportata sul profilo

	![Domain Security Policies](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domain Security Policies")

##Attivazione delle modifiche apportate ai criteri di sicurezza

1.  Immettere "attiva" nella casella di ricerca, quindi fare clic sul collegamento Attiva modifiche in sospeso ai criteri di sicurezza.

	![Activate](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activate")

2.   Avviare l'attività Attiva modifiche in sospeso ai criteri di sicurezza immettendo un commento a scopo di controllo, quindi fare clic sul pulsante OK.

	![Activate Pending Security](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activate Pending Security")

3.  Completare l'attività nella schermata successiva selezionando la casella di controllo Conferma e facendo clic sul pulsante OK.

	![Activate Pending Security](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activate Pending Security")

##Configurazione dell'importazione di utenti in Microsoft Azure AD

Questa sezione descrive come configurare Microsoft Azure AD per importare utenti da Workday.

###Per configurare l'importazione di utenti in Microsoft Azure AD, seguire questa procedura:

1.  Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Configura importazione utenti** per aprire la finestra di dialogo **Configura provisioning**.

2.  Nella pagina **Impostazioni e credenziali amministratore** seguire questa procedura e quindi fare clic su Avanti:

	![Impostazioni e credenziali di amministratore](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Impostazioni e credenziali di amministratore")

	1.  Nella casella di testo **Nome utente amministratore Workday** digitare il nome dell'utente creato nella sezione [Creazione di un utente del sistema di integrazione](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser).
    2.  Nella casella di testo **Password amministratore Workday** digitare la password dell'utente creato nella sezione [Creazione di un utente del sistema di integrazione](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser).
    3.  Nella casella di testo **URL tenant Workday** digitare l'URL o il tenant di Workday.

3.  Nella pagina **Connessione di test** fare clic su **Avvia test** per verificare la connettività e quindi fare clic su **Avanti**.

	![Connessione di test](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Connessione di test")

4.  Nella pagina **Opzioni di provisioning** fare clic su **Avanti**.

	![Opzioni di provisioning](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Opzioni di provisioning")

5.  Nella finestra di dialogo **Avvia il provisioning** fare clic su **Completa**.

	![Avvia provisioning](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Avvia provisioning")

È ora possibile passare alla sezione **Utenti** e verificare se l'utente Workday è stato importato.

<!---HONumber=AcomDC_0810_2016-->