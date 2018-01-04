
## <a name="register-your-application"></a>Registrare l'applicazione
È possibile registrare l'applicazione in uno dei due modi.

### <a name="option-1-express-mode"></a>Opzione 1: Modalità Express
Rapidamente, è possibile registrare l'applicazione nel modo seguente:
1. Passare al [portale di registrazione applicazione Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Selezionare **aggiungere un'app**.

3. Nel **nome applicazione** , immettere un nome per l'applicazione.

4. Verificare che il **impostazione guidata** casella di controllo è selezionata, quindi fare clic **crea**.

5. Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice.

### <a name="option-2-advanced-mode"></a>Opzione 2: La modalità avanzata
Per registrare l'applicazione e aggiungere le informazioni di registrazione dell'applicazione alla soluzione, eseguire le operazioni seguenti:
1. Se è ancora stato già registrato nell'applicazione, passare al [portale di registrazione dell'applicazione Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Selezionare **aggiungere un'app**.

3. Nel **nome applicazione** , immettere un nome per l'applicazione. 

4. Verificare che il **impostazione guidata** casella di controllo è deselezionata e quindi selezionare **crea**.

5. Selezionare **aggiungere piattaforma**selezionare **applicazione nativa**, quindi selezionare **salvare**.

6. Nel **ID applicazione** casella, copiare il GUID.

7. Passare a Visual Studio, aprire il *App.xaml.cs* file e quindi sostituire `your_client_id_here` con l'ID dell'applicazione che è stato appena registrato e copiato.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
