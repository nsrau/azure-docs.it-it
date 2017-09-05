Per abilitare la modifica del profilo nell'applicazione, è necessario creare i criteri di modifica del profilo. Questi criteri descrivono l'esperienza utente durante la procedura di modifica del profilo e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Nella sezione delle impostazioni relativa ai criteri selezionare **Criteri di modifica del profilo** e fare clic su **+ Aggiungi**.

![Selezionare Criteri di modifica del profilo e fare clic sul pulsante Aggiungi](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Immettere un **Nome** di criterio a cui l'applicazione può fare riferimento. Ad esempio, immettere `SiPe`.

Selezionare **Provider di identità** e quindi selezionare**Accesso all'account locale**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.

![Selezionare Accesso all'account locale come provider di identità e quindi fare clic sul pulsante OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Selezionare **Attributi del profilo**. Scegliere gli attributi che l'utente può visualizzare e modificare nel profilo. Selezionare ad esempio **Paese/area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

![Selezionare alcuni attributi e fare clic sul pulsante OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Selezionare **Attestazioni dell'applicazione**. Scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione dopo un'esperienza di modifica del profilo riuscita. Selezionare ad esempio **Nome visualizzato** e **Codice postale**.

![Selezionare alcune attestazioni dell'applicazione e fare clic sul pulsante OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Fare clic su **Crea** per aggiungere il criterio. Il criterio viene elencato come **B2C_1_SiPe**. Il prefisso **B2C_1_** viene aggiunto al nome.

Aprire i criteri selezionando **B2C_1_SiPe**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui adesso**.

![Selezionare un criterio ed eseguirlo](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Impostazione      | Valore  |
| ------------ | ------ |
| **Applicazioni** | App Contoso B2C |
| **Selezionare l'URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di modifica del profilo dell'utente in base alla configurazione specificata.

> [!NOTE]
> La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
>