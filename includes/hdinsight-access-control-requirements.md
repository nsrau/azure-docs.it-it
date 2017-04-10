Si potrebbe usare una sottoscrizione di Azure di cui non si è amministratore o proprietario, ad esempio una sottoscrizione di proprietà dell'azienda. In tal caso, per eseguire le procedure di questo articolo è necessario verificare che sia presente quanto segue:

* Accesso di tipo Collaboratore. Per accedere ad Azure, è necessario almeno l'accesso Collaboratore al gruppo di risorse di Azure. Questo gruppo di risorse viene usato per creare un cluster HDInsight e altre risorse di Azure.
* Registrazione del provider. Un utente con almeno l'accesso Collaboratore alla sottoscrizione di Azure deve avere registrato in precedenza il provider della risorsa in uso. La registrazione del provider viene eseguita quando un utente con accesso Collaboratore alla sottoscrizione crea una risorsa per la prima volta nella sottoscrizione stessa. Può essere eseguita anche senza creare una risorsa [registrando un provider tramite REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Per altre informazioni sull'uso della gestione degli accessi, vedere gli articoli seguenti:

* [Introduzione alla gestione degli accessi nel portale di Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../articles/active-directory/role-based-access-control-configure.md)
