<properties 
  pageTitle="Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso | Microsoft Azure" 
  description="Informazioni su come usare Workday come origine dei dati sull'identità per Azure Active Directory." 
  services="active-directory" 
  authors="MarkusVi"  
  documentationCenter="na" 
  manager="msStevenPo"/>
<tags 
  ms.service="active-directory" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="na" 
  ms.workload="identity" 
  ms.date="08/01/2015" 
  ms.author="markvi" />


#Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso


>[AZURE.TIP]Per inviare commenti e suggerimenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=521880).
  
Questa esercitazione descrive la procedura da eseguire in Workday e Azure AD per importare utenti da Workday in Azure AD.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

-   Sottoscrizione di Azure AD Premium valida
-   Tenant di Workday
  
Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Workday 


2. Creazione di un utente del sistema di integrazione


3. Creazione di un gruppo di sicurezza


4. Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza


5. Configurazione delle opzioni del gruppo di sicurezza


6. Attivazione delle modifiche apportate ai criteri di sicurezza


7. Configurazione dell'importazione di utenti in Azure AD



##Abilitazione dell'integrazione dell'applicazione per Workday
  
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Workday.

###Per abilitare l'integrazione dell'applicazione per Workday, seguire questa procedura:

1.  Nel portale di gestione di Azure fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Aggiunta di un'applicazione")

  
5. Nella casella di ricerca digitare **Workday**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Aggiungere un'applicazione dalla raccolta")

6. Nel riquadro dei risultati selezionare Workday e quindi fare clic su Completa per aggiungere l'applicazione.

    ![Raccolta di applicazioni](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Raccolta di applicazioni")





## Creazione di un utente del sistema di integrazione

1. In **Workday Workbench** immettere create user nella casella di ricerca e quindi fare clic su **Create Integration System User**. <br><br> ![Create user](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Create user")



2. Completare l'attività **Create Integration System User** specificando un nome utente e una password per un nuovo utente del sistema di integrazione. Lasciare deselezionata l'opzione Require New Password at Next Sign In perché questo utente eseguirà l'accesso a livello di codice. <br>Lasciare l'opzione Session Timeout Minutes impostata sul valore predefinito 0, per evitare un timeout prematuro delle sessioni dell'utente. <br><br> ![Create Integration System User](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Create Integration System User")
 




## Creazione di un gruppo di sicurezza

Per lo scenario descritto in questa esercitazione, è necessario creare un gruppo di sicurezza del sistema di integrazione senza vincoli e assegnare l'utente a quel gruppo.


1. Immettere create security group nella casella di ricerca e quindi fare clic su **Create Security Group**. <br><br> ![Create Security Group](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Create Security Group")
 

2. Completare l'attività Create Security Group. Selezionare System Security Group-Unconstrained dall'elenco a discesa Type of Tenanted Security Group per creare un gruppo di sicurezza a cui si aggiungeranno i membri in modo esplicito. <br><br> ![Create Security Group](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Create Security Group")
 



## Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza

1. Immettere edit security group nella casella di ricerca e quindi fare clic su **Edit Security Group**. <br><br> ![Edit Security Group](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edit Security Group")
 
 

2. Eseguire la ricerca del nuovo gruppo di sicurezza di integrazione e selezionarlo in base al nome. <br><br> ![Edit Security Group](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifica gruppo di sicurezza") Edit Security Group

3. Aggiungere il nuovo utente del sistema di integrazione al nuovo gruppo di sicurezza. <br><br> ![System Security Group](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System Security Group")




## Configurazione delle opzioni del gruppo di sicurezza

In questo passaggio si concedono al nuovo gruppo di sicurezza le autorizzazioni per le operazioni **Get** e **Put** sugli oggetti protetti dai criteri di sicurezza di dominio seguenti:

- External Account Provisioning

- Worker Data: Public Worker Reports

- Worker Data: All Positions

- Worker Data: Current Staffing Information

- Worker Data: Business Title on Worker Profile
 
   

1. Immettere domain security policies nella casella di ricerca e quindi fare clic sul collegamento Domain Security Policies for Functional Area. <br><br> ![Domain Security Policies](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domain Security Policies")  
 

2. Cercare system e selezionare **System** come area funzionale. Fare clic su **OK**. <br><br> ![Domain Security Policies](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domain Security Policies")


3. Nell'elenco dei criteri di sicurezza relativi all'area funzionale System espandere Security Administration e selezionare i criteri di sicurezza del dominio External Account Provisioning. <br><br> ![Domain Security Policies](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domain Security Policies")


4. Fare clic sul pulsante **Edit Permissions** e quindi nella finestra di dialogo **Edit Permissions** aggiungere il nuovo gruppo di sicurezza all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione **Get** e **Put**. <br><br> ![Edit Permissions](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Edit Permissions")

 

5. Ripetere il passaggio 1 precedente per tornare alla schermata di selezione delle aree funzionali, cercare staffing, selezionare l'area funzionale Staffing e fare clic su **OK**.<br><br> ![Domain Security Policies](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domain Security Policies")
 

6. Nell'elenco dei criteri di sicurezza relativi all'area funzionale Staffing espandere Worker Data: Staffing e ripetere il passaggio 4 precedente per ognuno dei criteri di sicurezza rimanenti:

     - Worker Data: Public Worker Reports

     - Worker Data: All Positions

     - Worker Data: Current Staffing Information

     - Worker Data: Business Title on Worker Profile


<br><br> ![Domain Security Policies](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domain Security Policies")







## Attivazione delle modifiche apportate ai criteri di sicurezza


1. Immettere activate nella casella di ricerca e quindi fare clic sul collegamento Activate Pending Security Policy Changes. <br><br> ![Activate](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activate") 
 

2. Avviare l'attività Activate Pending Security Policy Changes immettendo un commento a scopo di controllo e quindi fare clic su **OK**. <br><br> ![Activate Pending Security](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activate Pending Security")
 

3. Completare l'attività nella schermata successiva selezionando la casella di controllo Confirm e quindi facendo clic su **OK**. <br><br> ![Activate Pending Security](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activate Pending Security")





## Configurazione dell'importazione di utenti in Azure AD

Questa sezione descrive come configurare Azure AD per importare utenti da Workday.


### Per configurare l'importazione di utenti in Azure AD, seguire questa procedura:


1. Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Configura importazione utenti** per aprire la finestra di dialogo **Configura provisioning**.


2\.Nella pagina **Impostazioni e credenziali amministratore** seguire questa procedura e quindi fare clic su **Avanti**: <br><br> ![Impostazioni e credenziali di amministratore](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Impostazioni e credenziali di amministratore")
 
     2.1. In the Workday admin user name textbox, type the name of the user you have created in the Creating an integration system user section.

     2.2. In the Workday admin password textbox, type the password of the user you have created in the Creating an integration system user section.

     2.3. In the Workday tenant URL textbox, type the URL or your Workday tenant.


3. Nella pagina **Connessione di test** fare clic su **Avvia test** per verificare la connettività e quindi fare clic su **Avanti**. <br><br> ![Connessione di test](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Connessione di test")  
 

4. Nella pagina **Opzioni di provisioning** fare clic su **Avanti**. <br><br> ![Opzioni di provisioning](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opzioni di provisioning")


5. Nella finestra di dialogo **Avvia il provisioning** fare clic su **Completa**. <br><br> ![Avvia provisioning](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Avvia provisioning")
 

È ora possibile passare alla sezione **Utenti** e verificare se l'utente Workday è stato importato.



## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->