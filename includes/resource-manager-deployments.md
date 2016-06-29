Il modello può essere un file locale oppure un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

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

<!---HONumber=AcomDC_0615_2016-->