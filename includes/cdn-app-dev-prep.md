## Operazioni preliminari

Prima di poter scrivere il codice di gestione della rete CDN, è necessaria un po' di preparazione. La prima cosa da fare è creare un gruppo di risorse per includere il profilo di rete CDN che verrà creato in questa esercitazione. Quindi si configurerà Azure Active Directory per fornire l'autenticazione per l'applicazione. Una volta eseguita questa operazione, si applicheranno le autorizzazioni al gruppo di risorse in modo che solo gli utenti autorizzati dal tenant di Azure AD possano interagire con il profilo di rete CDN.

### Creazione del gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic sul pulsante **Nuovo** in alto a sinistra e poi su **Gestione** e **Gruppo di risorse**.
	
	![Creazione di un nuovo gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-1.png)

3. Assegnare al gruppo di risorse il nome *CdnConsoleTutorial*. Selezionare la sottoscrizione e scegliere un percorso locale. Se si desidera, è possibile selezionare la casella di controllo **Aggiungi al dashboard** per aggiungere il gruppo di risorse al dashboard del portale. In questo modo sarà più semplice trovarlo in un secondo momento. Dopo avere eseguito le selezioni, fare clic su **Crea**.

	![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-2.png)

4. Dopo aver creato il gruppo di risorse, se non lo si è aggiunto al dashboard, è possibile trovarlo facendo clic su **Sfoglia** e poi su **Gruppi di risorse**. Fare clic sul gruppo di risorse per aprirlo. Annotare l'**ID sottoscrizione**. Sarà necessario più avanti.

	 ![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-subscription-id.png)

### Creazione dell'applicazione Azure AD

Per l'autenticazione delle app con Azure Active Directory sono possibili due approcci: singoli utenti o un'entità servizio. Un'entità servizio è simile a un account di servizio di Windows. Anziché concedere a un determinato utente le autorizzazioni per interagire con i profili di rete CDN, si concedono le autorizzazioni per l'entità servizio. Le entità servizio in genere vengono usate per i processi automatizzati e non interattivi. Sebbene in questa esercitazione si scriva un'applicazione console interattiva, ci si concentrerà sull'approccio dell'entità servizio.

La creazione di un'entità servizio è costituita da diversi passaggi, compresa la creazione di un'applicazione Azure Active Directory. A tale scopo si [eseguirà questa esercitazione](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Assicurarsi di completare tutti i passaggi dell'[esercitazione collegata](../articles/resource-group-create-service-principal-portal.md). È *estremamente importante* eseguirla esattamente come descritto. Assicurarsi di annotare l'**ID tenant**, il **nome di dominio tenant** (in genere un dominio *. onmicrosoft.com*, se non è stato specificato un dominio personalizzato), l'**ID client** e la **chiave di autenticazione client**, che serviranno più avanti. Prestare attenzione all'**ID client** e alla **chiave di autenticazione client**, in quanto queste credenziali possono essere usate da chiunque per eseguire operazioni come entità servizio.
> 	
> Quando si arriva al passaggio denominato [Configura applicazione multi-tenant](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), selezionare **No**.
> 
> Quando si arriva al passaggio [Assegnare l'applicazione al ruolo](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), usare il gruppo di risorse creato in precedenza, *CdnConsoleTutorial*, ma anziché il ruolo di **lettore**, assegnare il ruolo di **collaboratore profilo di rete CDN**. Dopo aver assegnato all'applicazione il ruolo di **collaboratore profilo di rete CDN** nel gruppo di risorse, tornare a questa esercitazione.

Dopo aver creato l'entità servizio e assegnato il ruolo di **collaboratore profilo di rete CDN**, il pannello **Utenti** per il gruppo di risorse dovrebbe essere simile a questo.

![Pannello Utenti](./media/cdn-app-dev-prep/cdn-service-principal.png)


### Autenticazione utente interattiva

Se, invece di un'entità servizio, si preferisce l'autenticazione interattiva del singolo utente, il processo è molto simile a quello per un'entità servizio. Infatti, è necessario seguire la stessa procedura, a parte alcune piccole modifiche.

>[AZURE.IMPORTANT] Seguire questa procedura solo se si sceglie di utilizzare l'autenticazione interattiva del singolo utente anziché un'entità servizio.

1. Quando si crea l'applicazione, invece di **App Web**, scegliere **Applicazione nativa**.
	
	![Applicazione nativa](./media/cdn-app-dev-prep/cdn-native-application.png)
	
2. Nella pagina successiva verrà richiesto di immettere un **URI di reindirizzamento**. L'URI non verrà convalidato, ma è necessario ricordare i valori immessi. Sarà necessario più avanti.

3. Non è necessario creare una **chiave di autenticazione client**.

4. Invece di assegnare un'entità servizio al ruolo di **collaboratore profilo di rete CDN**, assegneremo singoli utenti o gruppi. In questo esempio si può notare che è stato assegnato l'*utente CDN Demo* al ruolo di **collaboratore profilo di rete CDN**.
	
	![Accesso del singolo utente](./media/cdn-app-dev-prep/cdn-aad-user.png)

<!---HONumber=AcomDC_0629_2016-->