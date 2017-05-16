> [!NOTE]
> Si prevede di ottimizzare questa esperienza e deprecare i passaggi seguenti.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Creare credenziali di amministratore nel tenant di Azure AD B2C.

Per la sezione successiva sono necessarie credenziali che usino il dominio del tenant di Azure AD B2C. A tale scopo è necessario creare un account amministratore con tali credenziali. A tale scopo, procedere come segue:

1. Nel [portale di Azure](https://portal.azure.com) passare al contesto del tenant di Azure AD B2C e aprire il pannello Azure AD B2C. [Visualizza procedura.](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. Selezionare **Utenti e gruppi**.
1. Selezionare **Tutti gli utenti**.
1. Fare clic su **Nuovo utente**.
    * Impostare il **nome** = `Admin`.
    * Impostare il **nome utente** = `admin@{tenantName}.onmicrosoft.com`, dove `{tenantName}` è il nome del tenant di Azure AD B2C.
1. In **Ruolo Directory** scegliere **Amministratore globale** e fare clic su **Ok**.
1. Fare clic su **Crea** per creare l'utente amministratore.
1. Selezionare **Mostra password** e copiare la password.

### <a name="set-up-the-key-container"></a>Configurare il contenitore di chiavi

Il contenitore di chiavi viene usato per archiviare le chiavi. Per configurarne uno:

1. Aprire un nuovo prompt dei comandi di PowerShell.  A tale scopo è possibile premere **tasto WINDOWS + R**, digitare `powershell` e premere INVIO.
1. Scaricare questa procedura di riproduzione per ottenere lo strumento ExploreAdmin di PowerShell.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. Passare alla cartella con lo strumento ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importare lo strumento ExploreAdmin in PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. Verificare che `b2c_1a_TokenSigningKeyContainer` non esista ancora.  Sostituire `{tenantName}` con il nome del tenant.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. Quando viene visualizzato il prompt di accesso, usare l'account amministratore creato nella sezione precedente.

    b. Quando richiesto, immettere il numero di telefono per configurare l'autenticazione a più fattori.

    c. Quando richiesto, modificare la password.

    d. **È previsto un errore**  L'errore dovrebbe indicare che `b2c_1a_TokenSigningKeyContainer` non è stato trovato.  Se non si verificano errori perché questi passaggi sono già stati completati, ignorare il resto di questa sezione.

1. Creare `b2c_1a_TokenSigningKeyContainer`.  Sostituire `{tenantName}` con il nome del tenant.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. Creare `b2c_1a_TokenEncryptionKeyContainer`.  Sostituire `{tenantName}` con il nome del tenant.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. Creare `b2c_1a_FacebookSecret`.  Sostituire `{tenantName}` con il nome del tenant.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
