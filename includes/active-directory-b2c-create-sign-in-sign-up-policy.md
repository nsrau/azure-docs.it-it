Per abilitare l'accesso nell'applicazione, è necessario creare i criteri di accesso. I criteri descrivono l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Nella sezione delle impostazioni relativa ai criteri selezionare **Criteri di iscrizione o di accesso** e fare clic su **+ Aggiungi**.

![Selezionare i criteri di iscrizione o di accesso e fare clic sul pulsante Aggiungi](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Immettere un **Nome** di criterio a cui l'applicazione può fare riferimento. Ad esempio, immettere `SiUpIn`.

Selezionare **Provider di identità** e quindi selezionare **Iscrizione posta elettronica**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.

![Selezionare Iscrizione posta elettronica come provider di identità e quindi fare clic sul pulsante OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Selezionare **Attributi di iscrizione**. Scegliere gli attributi da raccogliere dall'utente durante l'iscrizione. Selezionare ad esempio **Paese/area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

![Selezionare alcuni attributi e fare clic sul pulsante OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Selezionare **Attestazioni dell'applicazione**. Scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione dopo un'esperienza di iscrizione o accesso riuscita. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **Nuovo utente** e **ID oggetto dell'utente**.

![Selezionare alcune attestazioni dell'applicazione e fare clic sul pulsante OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Fare clic su **Crea** per aggiungere il criterio. Il criterio viene elencato come **B2C_1_SiUpIn**. Il prefisso **B2C_1_** viene aggiunto al nome.

Aprire i criteri facendo clic su **B2C_1_SiUpIn**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui adesso**.

![Selezionare un criterio ed eseguirlo](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Impostazione      | Valore  |
| ------------ | ------ |
| **Applicazioni** | App Contoso B2C |
| **Selezionare l'URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di iscrizione o di accesso dell'utente in base alla configurazione specificata.

> [!NOTE]
> La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
>