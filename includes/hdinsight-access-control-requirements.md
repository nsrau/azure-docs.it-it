Se si usa una sottoscrizione di Azure di cui non si è amministratore o proprietario, ad esempio una sottoscrizione di proprietà della società, è necessario verificare quanto segue prima di usare i passaggi illustrati in questo articolo:

* Per accedere ad Azure, è necessario almeno l'accesso Collaboratore al gruppo di risorse di Azure. Questo gruppo di risorse viene usato per creare un cluster Azure HDInsight e altre risorse di Azure.
* Un utente con almeno l'accesso Collaboratore alla sottoscrizione di Azure deve avere registrato in precedenza il provider della risorsa in uso. La registrazione del provider viene eseguita quando un utente con accesso Collaboratore alla sottoscrizione crea una risorsa per la prima volta nella sottoscrizione stessa. Può essere eseguita anche senza creare una risorsa [registrando un provider tramite REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Per altre informazioni sull'uso della gestione degli accessi, vedere gli articoli seguenti:

* [Introduzione alla gestione degli accessi nel portale di Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../articles/active-directory/role-based-access-control-configure.md)


<!--HONumber=Jan17_HO1-->


