Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <URI from previous step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene richiesta una password, assicurarsi di immettere la password creata nella sezione relativa alla [configurazione di un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```

Il comando precedente restituisce informazioni simili all'esempio seguente:
