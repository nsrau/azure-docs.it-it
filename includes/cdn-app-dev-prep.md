## <a name="prerequisites"></a>prerequisiti
Prima di scrivere codice per la gestione di una rete CDN, è necessario eseguire alcune operazioni preliminari per consentire al codice di interagire con Azure Resource Manager. A tale scopo è necessario:

* Creare un gruppo di risorse in cui includere il profilo di rete CDN creato in questa esercitazione
* Configurare Azure Active Directory per fornire l'autenticazione per l'applicazione
* Applicare le autorizzazioni al gruppo di risorse in modo che solo gli utenti autorizzati del tenant di Azure AD possano interagire con il profilo di rete CDN

### <a name="creating-the-resource-group"></a>Creazione del gruppo di risorse
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa**.
3. Cercare **Gruppo di risorse** e quindi nel riquadro Gruppo di risorse fare clic su **Crea**.

    ![Creazione di un nuovo gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Assegnare al gruppo di risorse il nome *CdnConsoleTutorial*.  Selezionare la sottoscrizione e scegliere un percorso locale.  Se si vuole, è possibile selezionare la casella di controllo **Aggiungi al dashboard** per aggiungere il gruppo di risorse al dashboard nel portale.  In questo modo sarà più facile trovarlo in un secondo momento.  Dopo avere eseguito le selezioni, fare clic su **Crea**.

    ![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Se dopo la creazione il gruppo di risorse non viene aggiunto al dashboard, è possibile trovarlo facendo clic su **Esplora** e quindi su **Gruppi di risorse**.  Fare clic sul gruppo di risorse per aprirlo.  Annotare l' **ID sottoscrizione**. Sarà necessario in un secondo momento.

    ![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Creazione dell'applicazione Azure AD e applicazione delle autorizzazioni
Per l'autenticazione delle app con Azure Active Directory sono possibili due approcci: singoli utenti o un'entità servizio. Un'entità servizio è simile a un account di servizio di Windows.  Anziché concedere a un determinato utente le autorizzazioni per interagire con i profili di rete CDN, si concedono le autorizzazioni all'entità servizio.  Le entità servizio vengono in genere usate per processi automatizzati non interattivi.  Sebbene in questa esercitazione si scriva un'applicazione console interattiva, ci si concentrerà sull'approccio dell'entità servizio.

La creazione di un'entità servizio è costituita da diversi passaggi, compresa la creazione di un'applicazione Azure Active Directory.  Per crearla, si eseguirà [questa esercitazione](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Assicurarsi di completare tutti i passaggi dell' [esercitazione collegata](../articles/resource-group-create-service-principal-portal.md).  È *importante* completarla esattamente come descritto.  Assicurarsi di annotare l'**ID tenant**, il **nome di dominio del tenant** (in genere un dominio *.onmicrosoft.com*, se non è stato specificato un dominio personalizzato), l'**ID client** e la **chiave di autenticazione client**, perché queste informazioni saranno necessarie più avanti.  Proteggere con attenzione l'**ID client** e la **chiave di autenticazione client**, perché queste credenziali possono essere usate da chiunque per eseguire operazioni come entità servizio.
>
> Quando si arriva al passaggio denominato Configurare l'applicazione multi-tenant, selezionare **No**.
>
> Quando si arriva al passaggio [Assegnare l'applicazione al ruolo](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role), usare il gruppo di risorse creato in precedenza, *CdnConsoleTutorial*, assegnando però il ruolo **Collaboratore profilo rete CDN** anziché il ruolo **Lettore**.  Dopo aver assegnato all'applicazione il ruolo di **collaboratore profilo di rete CDN** nel gruppo di risorse, tornare a questa esercitazione. 
>
>

Dopo la creazione dell'entità servizio e l'assegnazione del ruolo **Collaboratore profilo rete CDN**, il pannello **Utenti** per il gruppo di risorse dovrebbe avere un aspetto simile all'immagine seguente.

![Pannello Utenti](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticazione utente interattiva
Se invece di un'entità servizio si preferisce usare l'autenticazione interattiva di un singolo utente, il processo è simile a quello per un'entità servizio.  È infatti necessario seguire la stessa procedura, a parte alcune modifiche secondarie.

> [!IMPORTANT]
> Seguire questa procedura solo se si sceglie di utilizzare l'autenticazione interattiva del singolo utente anziché un'entità servizio.
>
>

1. Quando si crea l'applicazione, invece di **Applicazione Web** scegliere **Applicazione nativa**.

    ![Applicazione nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Nella pagina successiva viene richiesto un **URI di reindirizzamento**.  L'URI non verrà convalidato, ma è necessario ricordare i valori immessi. Sarà necessario in un secondo momento.
3. Non è necessario creare una **chiave di autenticazione client**.
4. Invece di assegnare un'entità servizio al ruolo di **collaboratore profilo di rete CDN** , verranno assegnati singoli utenti o gruppi.  In questo esempio si può notare che è stato assegnato l'utente *CDN Demo User* al ruolo **CDN Profile Contributor**.  

    ![Accesso del singolo utente](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
