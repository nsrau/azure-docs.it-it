## <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <URI from previous step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app. Verrà richiesta la password creata in precedenza quando è stato creato l'utente della distribuzione. Assicurarsi di immettere la password creata in [Configurare un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```

Il comando precedente restituisce informazioni simili all'esempio seguente:
