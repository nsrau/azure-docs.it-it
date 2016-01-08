<properties
   pageTitle="Procedura dettagliata di utilizzo dell'anteprima di Azure Active Directory B2B Collaboration| Microsoft Azure"
   description="Azure Active Directory B2B supporta le relazioni tra aziende abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Procedura dettagliata di utilizzo dell'anteprima di Azure Active Directory (Azure AD) B2B Collaboration
In questa procedura dettagliata viene descritto come utilizzare Azure AD B2B Collaboration. In qualità di amministratore IT di Contoso, si desidera condividere le applicazioni con i dipendenti da tre società partner. Per nessuna delle società partner è necessario disporre di Azure AD.

- Alice dall'organizzazione Simple Partner
- Bob, dall'organizzazione Medium Partner, deve accedere a un set di applicazioni
- Carol, dall'organizzazione Complex Partner, deve accedere a un set di applicazioni, e ha bisogno di fare parte dei gruppi di Contoso

Dopo aver inviato gli inviti agli utenti dell’azienda partner, è possibile configurarli in Azure AD per garantire l'accesso alle app e l'appartenenza ai gruppi tramite il portale di Azure. Si inizia aggiungendo Alice.

## Si aggiunge Alice alla directory di Contoso
1. Creare un file .cvs con le intestazioni come illustrato, compilando solo le voci che indicano l’**Email**, il **DisplayName**, e **InviteContactUsUrl** di Alice. **DisplayName** è il nome che verrà visualizzato nell'invito, nonché il nome che verrà visualizzato nella directory di Azure AD di Contoso. **InviteContactUsUrl** è un modo che ha Alice di contattare Contoso. Nell'esempio seguente viene specificato il profilo di LinkedIn di Contoso. È importante che le etichette siano nella prima riga del file .cvs nello stesso ordine e digitate nello stesso modo in cui è stato illustrato. Vedere di seguito la sezione formato CSV. ![File CSV di esempio per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Nel portale di Azure, aggiungere un utente alla directory di Contoso (Active Directory > Contoso > Utenti > Aggiungi utente). Nell’elenco a discesa "Tipo di utente", selezionare "Utenti nelle aziende partner". Caricare il file con estensione .csv. Assicurarsi che il file con estensione .csv venga chiuso prima di caricarlo. ![Carica file CSV per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice viene ora rappresentata come un utente esterno nella directory di Azure AD di Contoso. ![Alice è nell’elenco di Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Dal punto di vista di Alice, quest’ultima riceverà il seguente messaggio di posta elettronica. ![Email di invito per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice fa clic sul link e le viene richiesto di accettare l'invito e di accedere utilizzando le credenziali di lavoro. Se Alice non è presente nella directory di Azure AD, dovrà effettuare l'iscrizione. ![Iscrizione su invito per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice viene reindirizzata al pannello di accesso dell’App, che resta vuoto fino a quando all'utente viene concesso l'accesso alle app. ![Pannello di accesso per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Questa è la forma più semplice di collaborazione B2B. In qualità di utente nella directory di Microsoft Azure di Contoso, ad Alice può essere concesso l'accesso alle applicazioni e ai gruppi tramite il portale di Azure. Ora aggiungiamo Bob, che deve accedere alle applicazioni Moodle e Salesforce.

## Aggiungere Bob alla directory di Contoso e concedergli l'accesso alle app
1. Utilizzare Windows PowerShell con il modulo di Azure AD installato per trovare le ID dell’applicazione di Moodle e Salesforce. Gli ID possono essere recuperati tramite il cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Verrà visualizzato un elenco di tutte le applicazioni disponibili in Contoso e nei relativi AppPrincialIds. ![Recuperare le ID per Bob](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Creare file con estensione .csv, compilare con l’email, il DisplayName, l’**InviteAppID**, l’**InviteAppResources**, e l’InviteContactUsUrl di Bob. **InviteAppResources** è popolato con AppPrincipalIds di Moodle e Salesforce da PowerShell, separati da uno spazio. Questo viene evidenziato dalle ID cerchiate di verde e di blu che corrispondono alla schermata precedente di PowerShell. **InviteAppId** è popolato dallo stesso AppPrincipalId di Moodle per personalizzare il messaggio di posta elettronica e accedere alle pagine. ![File CSV di esempio per Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Caricare il file con estensione .csv tramite il portale di Azure esattamente come è stato fatto per Alice. Bob viene ora un utente esterno nella directory di Azure AD di Contoso.

4. Bob riceverà il seguente messaggio di posta elettronica. ![Email di invito per Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob fa clic sul collegamento e gli viene richiesto di accettare l'invito. Dopo aver effettuato l’accesso, viene indirizzato al pannello di accesso e può già utilizzare Moodle e Salesforce. ![Pannello di accesso per Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Si aggiungerà poi Carol, che deve accedere alle applicazioni e ha anche bisogno dell’appartenenza ai gruppi nella directory di Contoso.

## Aggiungere Carol alla directory di Contoso, garantirle l'accesso alle applicazioni e assegnarle l'appartenenza al gruppo

1. Utilizzare Windows PowerShell con il modulo di Azure AD installato per trovare le ID dell’applicazione e le ID del gruppo all’interno di Contoso.
 - Recuperare AppPrincipalId mediante il cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, così come è avvenuto con Bob.
 - Recuperare ObjectId per gruppi utilizzando il cmdlet `Get-MsolGroup | fl DisplayName, ObjectId` per visualizzare un elenco di tutti i gruppi in Contoso e i relativi ObjectIds. Le ID del gruppo possono essere recuperate anche come ID oggetto, nella scheda delle proprietà del gruppo nel portale di Azure. ![Recuperare le ID e i gruppi per Carol](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Creare file con estensione .csv, compilare con l’email, il DisplayName, l’InviteAppID, l’InviteAppResources, l’**InviteGroupResources**e l’InviteContactUsUrl di Carol. **InviteGroupResources** è popolato da ObjectID dei gruppi MyGroup1 ed Externals, separati da uno spazio. ![File CSV di esempio per Carol](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Caricare il file con estensione .csv tramite il portale di Azure.

4. Carol è un utente nella directory di Contoso ed è anche un membro dei gruppi MyGroup1 e Externals, come illustrato nel portale di Azure. ![Carol è nell’elenco di un gruppo in Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol riceverà un messaggio di posta elettronica contenente un collegamento per accettare l'invito. Dopo aver effettuato l’accesso, verrà reindirizzata al pannello di accesso App per accedere a Moodle e Salesforce.

Questo è tutto ciò che bisogna fare per aggiungere utenti da aziende partner in AD B2B Azure Collaboration. Questa procedura dettagliata illustrata l'aggiunta di Alice, Bob e Carol in tre file .cvs distinti, ma possono in realtà essere aggiunti insieme in un unico file .cvs per motivi di praticità. ![File CSV di esempio per Alice, Bob e Carol](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Articoli correlati
Vedere gli altri articoli su Azure AD B2B Collaboration.

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->