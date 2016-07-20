## Distribuzioni incrementali e complete

Per impostazione predefinita, Gestione risorse gestisce le distribuzioni come aggiornamenti incrementali al gruppo di risorse. Con la distribuzione incrementale, Gestione risorse:

- **lascia invariate** le risorse presenti nel gruppo di risorse, ma non specificate nel modello
- **aggiunge** le risorse specificate nel modello, ma non presenti nel gruppo di risorse
- **non esegue nuovamente il provisioning** delle risorse presenti nel gruppo di risorse alle stesse condizioni definite nel modello

Con la distribuzione completa, Gestione risorse:

- **elimina** le risorse presenti nel gruppo di risorse, ma non specificate nel modello
- **aggiunge** le risorse specificate nel modello, ma non presenti nel gruppo di risorse
- **non esegue nuovamente il provisioning** delle risorse presenti nel gruppo di risorse alle stesse condizioni definite nel modello
 
La proprietà **Mode** consente di specificare il tipo di distribuzione.

<!---HONumber=AcomDC_0706_2016-->