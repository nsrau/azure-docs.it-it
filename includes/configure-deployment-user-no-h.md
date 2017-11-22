In Cloud Shell creare credenziali di distribuzione con il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set). Un utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Nome utente e password sono a livello di account. _Sono quindi diversi dalle credenziali della sottoscrizione di Azure._

Nell'esempio seguente sostituire *\<username>* e *\<password>* (incluse le parentesi) con un nuovo nome utente e una nuova password. Il nome utente deve essere univoco. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Se viene visualizzato un errore ` 'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

L'utente di distribuzione viene creato una sola volta e può essere usato per tutte le distribuzioni di Azure.

> [!NOTE]
> Registrare il nome utente e la password. Saranno necessari per distribuire l'app Web in un secondo momento.
>
>