## <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione  

Per la distribuzione con FTP e l'istanza Git locale, è necessario che nel server sia configurato un utente della distribuzione per l'autenticazione.

> [!NOTE]
> Un utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web.
> `username` e `password` sono a livello di account e sono quindi diversi dalle credenziali della sottoscrizione di Azure.
>

Eseguire il comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) per creare le credenziali per la distribuzione.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

Il nome utente deve essere univoco e la password deve essere complessa. Se viene visualizzato un errore ` 'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

È necessario creare l'utente della distribuzione una sola volta e sarà possibile usarlo per tutte le distribuzioni di Azure.

Registrare il nome utente e la password, poiché verranno usate in un passaggio successivo, quando si distribuisce l'applicazione.