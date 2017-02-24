---
title: 'Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso | Documentazione Microsoft'
description: "Informazioni su come usare Workday come origine dei dati sull&quot;identità per Azure Active Directory."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9bf2e87353901a043f01ff7d634e1b174cd6a52a
ms.openlocfilehash: a0d86046b471dec473fd707eedae061f9d1b9902


---
# <a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Esercitazione: Configurazione di Workday per la sincronizzazione in ingresso
Questa esercitazione descrive la procedura da eseguire in Workday e Azure AD per importare utenti da Workday in Azure AD. 

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure AD Premium valida
* Tenant di Workday

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Workday 
2. Creazione di un utente del sistema di integrazione 
3. Creazione di un gruppo di sicurezza 
4. Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza 
5. Configurazione delle opzioni del gruppo di sicurezza 
6. Attivazione delle modifiche apportate ai criteri di sicurezza 
7. Configurazione dell'importazione di utenti in Azure AD 

## <a name="enabling-the-application-integration-for-workday"></a>Abilitazione dell'integrazione dell'applicazione per Workday
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Workday.

### <a name="steps"></a>Passaggi:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella casella di ricerca digitare **Workday**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Aggiungere un'applicazione dalla raccolta")
6. Nel riquadro dei risultati selezionare Workday e quindi fare clic su Completa per aggiungere l'applicazione.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Raccolta di applicazioni")

## <a name="creating-an-integration-system-user"></a>Creazione di un utente del sistema di integrazione
### <a name="steps"></a>Passaggi:
1. In **Workday Workbench** immettere "create user" nella casella di ricerca e quindi fare clic su **Create Integration System User** (Crea utente del sistema di integrazione). 
   
    ![Creare un utente](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Creare un utente")
2. Completare l'attività **Create Integration System User** specificando un nome utente e una password per un nuovo utente del sistema di integrazione.  Lasciare l'opzione Richiedi nuova password al prossimo accesso non selezionata, perché l'accesso dell'utente verrà eseguito a livello di codice. Lasciare l'opzione Minuti di timeout della sessione impostata sul valore predefinito 0, in modo da evitare un timeout prematuro delle sessioni dell'utente. 
   
    ![Creare un utente del sistema di integrazione](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Creare un utente del sistema di integrazione")

## <a name="creating-a-security-group"></a>Creazione di un gruppo di sicurezza
Per lo scenario descritto in questa esercitazione, è necessario creare un gruppo di sicurezza del sistema di integrazione non vincolato e assegnare l'utente a tale gruppo.

### <a name="steps"></a>Passaggi:
1. Immettere "create security group" nella casella di ricerca e quindi fare clic su **Create Security Group**(Crea gruppo di sicurezza). 
   
    ![Creare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Creare un gruppo di sicurezza")
2. Completare l'attività Crea gruppo di sicurezza.  Selezionare Gruppo di sicurezza del sistema di integrazione - Non vincolato dall'elenco a discesa Tipo di gruppo di sicurezza con tenant per creare un gruppo di sicurezza a cui i membri verranno aggiunti esplicitamente. 
   
    ![Creare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Creare un gruppo di sicurezza")

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Assegnazione dell'utente del sistema di integrazione al gruppo di sicurezza
### <a name="steps"></a>Passaggi:
1. Immettere "edit security group" nella casella di ricerca e quindi fare clic su **Edit Security Group**(Modifica gruppo di sicurezza). 
   
    ![Modificare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modificare un gruppo di sicurezza")
2. Eseguire la ricerca del nuovo gruppo di sicurezza di integrazione e selezionarlo in base al nome. 
   
    ![Modificare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modificare un gruppo di sicurezza")
3. Aggiungere il nuovo utente del sistema di integrazione al nuovo gruppo di sicurezza. 
   
    ![Gruppo di sicurezza del sistema](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Gruppo di sicurezza del sistema")  

## <a name="configuring-security-group-options"></a>Configurazione delle opzioni del gruppo di sicurezza
In questo passaggio, al nuovo gruppo di sicurezza vengono concesse le autorizzazioni per le operazioni **Get** e **Put** sugli oggetti protetti dai seguenti criteri di sicurezza del dominio:

* External Account Provisioning
* Worker Data: Public Worker Reports
* Worker Data: All Positions
* Worker Data: Current Staffing Information
* Dati lavoratore - Qualifica riportata sul profilo

### <a name="steps"></a>Passaggi:
1. Immettere "criteri di sicurezza del dominio" nella casella di ricerca, quindi fare clic sul collegamento Criteri di sicurezza del dominio per area funzionale.  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Criteri di sicurezza di dominio")  
2. Cercare system e selezionare **System** come area funzionale.  Fare clic su **OK**.  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Criteri di sicurezza di dominio")  
3. Nell'elenco dei criteri di sicurezza relativi ad Area funzionale sistema espandere Amministrazione sicurezza e selezionare il criterio di sicurezza del dominio Provisioning account esterno.  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Criteri di sicurezza di dominio")  
4. Fare clic sul pulsante **Edit Permissions** (Modifica autorizzazioni) e nella finestra di dialogo **Edit Permissions** (Modifica autorizzazioni) aggiungere il nuovo gruppo di sicurezza all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione **Get** e **Put**. 
   
    ![Modificare un'autorizzazione](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Modificare un'autorizzazione")  
5. Ripetere il passaggio 1 precedente per tornare alla schermata di selezione delle aree funzionali. Cercare "staffing", selezionare l'area funzionale Staffing (Personale) e fare clic su **OK**.
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Criteri di sicurezza di dominio")  
6. Nell'elenco dei criteri di sicurezza relativi all'area funzionale Personale espandere Dati lavoratore - Personale e ripetere il passaggio 4 sopra descritto per ciascuno dei rimanenti criteri di sicurezza:
   
   * Worker Data: Public Worker Reports
   * Worker Data: All Positions
   * Worker Data: Current Staffing Information
   * Dati lavoratore - Qualifica riportata sul profilo

    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Criteri di sicurezza di dominio")  







## <a name="activating-security-policy-changes"></a>Attivazione delle modifiche apportate ai criteri di sicurezza
### <a name="steps"></a>Passaggi:
1. Immettere "attiva" nella casella di ricerca, quindi fare clic sul collegamento Attiva modifiche in sospeso ai criteri di sicurezza. 
   
    ![Attivare](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Attivare") 
2. Avviare l'attività Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza) immettendo un commento a scopo di controllo e quindi fare clic su **OK**. 
   
    ![Attivare la sicurezza in sospeso](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Attivare la sicurezza in sospeso")   
3. Completare l'attività nella schermata successiva selezionando la casella di controllo Confirm (Conferma) e quindi fare clic su **OK**. 
   
    ![Attivare la sicurezza in sospeso](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Attivare la sicurezza in sospeso")  

## <a name="configuring-user-import-in-azure-ad"></a>Configurazione dell'importazione di utenti in Azure AD
Questa sezione descrive come configurare Azure AD per importare utenti da Workday.

### <a name="steps"></a>Passaggi:
1. Nella pagina di integrazione dell'applicazione **Workday** fare clic su **Configura importazione utenti** per aprire la finestra di dialogo **Configura provisioning**.
2. Nella pagina **Impostazioni e credenziali amministratore** seguire questa procedura e quindi fare clic su **Avanti**: 
   
    ![Impostazioni e credenziali amministratore](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Impostazioni e credenziali amministratore")  
   
    a. Nella casella di testo del nome utente amministratore di Workday digitare il nome dell'utente creato nella sezione Creazione di un utente del sistema di integrazione.
   
    b. Nella casella di testo della password amministratore di Workday digitare la password dell'utente creato nella sezione Creazione di un utente del sistema di integrazione.
   
    c. Nella casella di testo dell'URL tenant di Workday digitare l'URL o il tenant di Workday.
3. Nella pagina **Connessione di test** fare clic su **Avvia test** per verificare la connettività e quindi fare clic su **Avanti**. 
   
    ![Connessione di test](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Connessione di test")  
4. Nella pagina **Opzioni di provisioning** fare clic su **Avanti**. 
   
    ![Opzioni di provisioning](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opzioni di provisioning")
5. Nella finestra di dialogo **Avvia il provisioning** fare clic su **Completa**. 
   
    ![Avviare il provisioning](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Avviare il provisioning")

È ora possibile passare alla sezione **Utenti** e verificare se l'utente Workday è stato importato.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)




<!--HONumber=Jan17_HO4-->


