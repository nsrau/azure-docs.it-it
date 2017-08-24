Per abilitare una reimpostazione dettagliata delle password nell'applicazione, è necessario creare criteri di reimpostazione delle password. Annotare l'opzione di reimpostazione delle password a livello di tenant specificata [qui](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Questi criteri descrivono l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Nella sezione delle impostazioni relativa ai criteri selezionare **Criteri di reimpostazione password** e fare clic su **+ Aggiungi**.

![Selezionare i criteri di iscrizione o di accesso e fare clic sul pulsante Aggiungi](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Immettere un **Nome** di criterio a cui l'applicazione può fare riferimento. Ad esempio, immettere `SSPR`.

Selezionare **Provider di identità** e quindi selezionare **Reimposta la password usando l'indirizzo di posta elettronica**. Fare clic su **OK**.

![Selezionare Reimposta la password usando l'indirizzo di posta elettronica come provider di identità e fare clic sul pulsante OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Selezionare **Attestazioni dell'applicazione**. Scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione dopo un'esperienza di reimpostazione della password riuscita. Selezionare ad esempio **ID oggetto dell'utente**.

![Selezionare alcune attestazioni dell'applicazione e fare clic sul pulsante OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Fare clic su **Crea** per aggiungere il criterio. Il criterio viene elencato come **B2C_1_SSPR**. Il prefisso **B2C_1_** viene aggiunto al nome.

Aprire i criteri selezionando **B2C_1_SSPR**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui adesso**.

![Selezionare un criterio ed eseguirlo](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Impostazione      | Valore  |
| ------------ | ------ |
| **Applicazioni** | App Contoso B2C |
| **Selezionare l'URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di reimpostazione della password dell'utente nell'applicazione.

> [!NOTE]
> La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
>
