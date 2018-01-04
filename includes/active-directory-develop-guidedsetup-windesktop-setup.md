
## <a name="set-up-your-project"></a>Configurare il progetto

In questa sezione si crea un nuovo progetto per illustrare come integrare un'applicazione Desktop di Windows .NET (XAML) con *Accedi con Microsoft* in modo che l'applicazione può eseguire una query Web API che richiedono un token.

L'applicazione creata in questa Guida consente di visualizzare un pulsante che viene utilizzato per chiamare un grafico, un'area per visualizzare i risultati sullo schermo e un pulsante di disconnessione.

> [!NOTE]
> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [Scaricare un progetto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)e ignorare il [passaggio di configurazione](#create-an-application-express) per configurare l'esempio di codice prima di eseguirla.
>

Per creare l'applicazione, eseguire le operazioni seguenti:
1. In Visual Studio, selezionare **File** > **New** > **progetto**.
2. In **modelli**selezionare **Visual c#**.
3. Selezionare **applicazione WPF** o **applicazione WPF**, a seconda della versione della versione di Visual Studio in uso.

## <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto
1. In Visual Studio, selezionare **strumenti** > **Gestione pacchetti NuGet**> **Package Manager Console**.
2. Nella finestra della Console di gestione pacchetti, incollare il seguente comando di Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Questo comando installa una libreria di autenticazione Microsoft. MSAL gestisce l'acquisizione, la memorizzazione nella cache e aggiornare i token utente che vengono utilizzati per accedere alle API che sono protetti da Azure Active Directory v2.
    >

## <a name="add-the-code-to-initialize-msal"></a>Aggiungere il codice per inizializzare MSAL
In questo passaggio si crea una classe per gestire l'interazione con MSAL, come la gestione dei token.

1. Aprire il *App.xaml.cs* file e quindi aggiungere il riferimento per MSAL alla classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Aggiornare la classe app per le operazioni seguenti:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Creare l'applicazione dell'interfaccia utente
In questa sezione viene illustrato come un'applicazione può richiedere un server back-end protetto, ad esempio Microsoft Graph. 

Oggetto *MainWindow. XAML* file deve essere creata automaticamente come parte del modello di progetto. Aprire il file e quindi sostituire l'applicazione  *\<griglia >* nodo con il codice seguente:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

