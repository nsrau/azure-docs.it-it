Per abilitare l'accesso nell'applicazione, è necessario creare i criteri di accesso. I criteri descrivono l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] Fare clic su **Criteri di accesso**.

Fare clic su **+Aggiungi** nella parte superiore del pannello.

Il **Nome** determina il nome dei criteri di accesso usati dall'applicazione. Immettere ad esempio **SiIn**.

Fare clic su **Provider di identità** e selezionare **Accesso all'account locale**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.

Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di accesso. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale** e **ID oggetto dell'utente**. Fare clic su **OK**.

Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come **B2C_1_SiIn** (il frammento **B2C\_1\_** viene aggiunto automaticamente come prefisso) nel pannello **Criteri di accesso**.

Aprire i criteri facendo clic su **B2C_1_SiIn**.

Selezionare **Contoso B2C app** nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta/URI di reindirizzamento**.

Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di accesso all'applicazione.

> [!NOTE]
> La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
>