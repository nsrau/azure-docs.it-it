Creare credenziali di distribuzione con il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set).

Un utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Nome utente e password sono a livello di account. Sono quindi diversi dalle credenziali della sottoscrizione di Azure.

Nel comando seguente sostituire *\<user-name>* e *\<password>* con un nuovo nome utente e una nuova password.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Il nome utente deve essere univoco. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. Se viene visualizzato un errore ` 'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

È necessario creare l'utente di distribuzione una sola volta; è possibile usarlo per tutte le distribuzioni di Azure.

> [!NOTE]
> Registrare il nome utente e la password. Saranno necessari per distribuire l'app Web in un secondo momento.
>
>