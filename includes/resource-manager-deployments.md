## <a name="incremental-and-complete-deployments"></a>Distribuzioni incrementali e complete
Quando si distribuiscono le risorse, specificare se la distribuzione è un aggiornamento incrementale o completo. La differenza principale tra le due modalità è il modo in cui Resource Manager gestisce le risorse esistenti nel gruppo di risorse che non sono presenti nel modello:

* Nella modalità di completamento, Resource Manager **elimina** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello. 
* Nella modalità incrementale, Resource Manager **lascia invariate** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello.

Per entrambe le modalità, Resource Manager prova a effettuare il provisioning di tutte le risorse specificate nel modello. Se la risorsa esiste già nel gruppo di risorse e le relative impostazioni sono identiche, l'operazione non comporta alcuna modifica. Se si modificano le impostazioni per una risorsa, il provisioning viene effettuato con le nuove impostazioni della risorsa. Se si prova ad aggiornare il percorso o il tipo di una risorsa esistente, la distribuzione ha esito negativo e restituisce un errore. È invece necessario distribuire una nuova risorsa con il percorso o il tipo necessari.

Per impostazione predefinita, Resource Manager usa la modalità incrementale.

Per illustrare la differenza tra le modalità incrementale e completa, si consideri lo scenario seguente.

Il **gruppo di risorse esistente** contiene:

* Risorsa A
* Risorsa B
* Risorsa C

Il **modello** definisce:

* Risorsa A
* Risorsa B
* Risorsa D

Quando viene implementato in modalità **incrementale**, il gruppo di risorse contiene:

* Risorsa A
* Risorsa B
* Risorsa C
* Risorsa D

Quando viene implementato in modalità **completa**, la risorsa C viene eliminata. Il gruppo di risorse contiene:

* Risorsa A
* Risorsa B
* Risorsa D
