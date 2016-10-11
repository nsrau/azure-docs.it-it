Se si usa una sottoscrizione di Azure di cui non si è amministratore/proprietario, ad esempio una sottoscrizione di proprietà dell'azienda, è necessario verificare quanto segue prima di usare i passaggi illustrati in questo documento:

* L'account di Azure deve includere almeno l'accesso __Collaboratore__ al gruppo di risorse di Azure usato durante la creazione di HDInsight e di altre risorse di Azure.

* Un utente con almeno l'accesso __Collaboratore__ alla sottoscrizione di Azure deve avere registrato in precedenza il provider della risorsa usata. La registrazione del provider viene eseguita quando un utente con accesso Collaboratore alla sottoscrizione crea una risorsa per la prima volta nella sottoscrizione stessa. Può anche essere eseguita senza creare una risorsa [registrando un provider tramite REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Per altre informazioni sull'uso della gestione degli accessi, vedere i documenti seguenti:

* [Introduzione alla gestione degli accessi nel portale di Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../articles/active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_1005_2016-->