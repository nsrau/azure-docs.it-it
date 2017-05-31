>[!NOTE]
> È possibile lasciare commenti in questa pagina oppure nel forum di [commenti e suggerimenti](https://feedback.azure.com/forums/216843-virtual-machines) su Azure tramite #azerrormessage.

## <a name="error-response-format"></a>Formato di risposta di errore 
Per la risposta di errore le macchine virtuali di Azure usano il formato JSON seguente:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Una risposta di errore include sempre un codice di stato e un oggetto error. Ogni oggetto error contiene sempre un codice di errore e una sezione message. Se la macchina virtuale viene creata con un modello, l'oggetto error include anche una sezione details, che contiene un livello interno di messaggio e codici di errore. In genere, il livello più interno del messaggio di errore rappresenta l'errore radice.


## <a name="common-virtual-machine-management-errors"></a>Messaggi di errore comuni nella gestione di macchine virtuali

Questa sezione contiene un elenco dei messaggi di errore comuni che possono essere visualizzati durante la gestione di macchine virtuali:

|  Codice di errore  |  Messaggio di errore  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Non è stato possibile acquisire un lease durante la creazione del disco '{0}' usando il BLOB con URI {1}. Il BLOB è già in uso.  |  
|  AllocationFailed  |  Allocazione non riuscita. Provare a ridurre le dimensioni della macchina virtuale o il numero di macchine virtuali, riprovare più tardi oppure provare la distribuzione in un set di disponibilità o in un percorso di Azure diverso.  |  
|  AllocationFailed  |  Allocazione della macchina virtuale non riuscita a causa di un errore interno. Riprovare più tardi o provare a distribuire in un percorso diverso.  |
|  ArtifactNotFound  |  L'estensione della macchina virtuale con editore '{0}' e tipo '{1}' non è stata trovata nel percorso '{2}'.  |
|  ArtifactNotFound  |  L'estensione con editore '{0}', tipo '{1}' e versione del gestore dei tipi '{2}' non è stata trovata nel repository di estensioni.  |
|  ArtifactVersionNotFound  |  Nel repository di elementi non sono state trovate versioni che soddisfano la versione richiesta '{0}'.  |
|  ArtifactVersionNotFound  |  Nel repository di elementi non sono state trovate versioni che soddisfano la versione richiesta '{0}' per l'estensione della macchina virtuale con editore '{1}' e tipo '{2}'.  |
|  AttachDiskWhileBeingDetached  |  Non è possibile collegare un disco dati '{0}' alla macchina virtuale '{1}' perché il disco è attualmente in fase di scollegamento. Attendere che il disco sia completamente scollegato, quindi riprovare.  |
|  RichiestaNonValida  |  Il set di disponibilità di tipo 'Allineato' non sono ancora supportati in questa area.  |
|  RichiestaNonValida  |  L'aggiunta di una macchina virtuale con dischi gestiti a un set di disponibilità non gestito o l'aggiunta di una macchina virtuale con dischi basati su BLOB a un set di disponibilità gestito non è supportata. Creare un set di disponibilità con lo SKU 'Allineato' per potervi aggiungere una macchina virtuale con dischi gestiti.  |
|  RichiestaNonValida  |  Il servizio Managed Disks non è supportato in questa area.  |
|  RichiestaNonValida  |  Per il tipo di sistema operativo '{0}' non sono supportate più estensioni della macchina virtuale per ciascun gestore. L'estensione della macchina virtuale '{1}' con gestore '{2}' è già stata aggiunta o specificata nell'input.  |
|  RichiestaNonValida  |  L'operazione '{0}' non è supportata nella risorsa '{1}' con dischi gestiti.  |
|  CertificateImproperlyFormatted  |  La rappresentazione JSON di un segreto recuperato da {0} ha un campo dati che non corrisponde a un file PFX formattato correttamente oppure la password fornita non decodifica correttamente il file PFX.  |
|  CertificateImproperlyFormatted  |  I dati recuperati da {0} non sono deserializzabili in JSON.  |
|  Conflitto  |  Il ridimensionamento del disco è consentito solo quando viene creata o deallocata una macchina virtuale.  |
|  ConflictingUserInput  |  Non è possibile collegare il disco '{0}' perché è già di proprietà della macchina virtuale '{1}'.  |
|  ConflictingUserInput  |  I gruppi di risorse di origine e di destinazione sono uguali.  |
|  ConflictingUserInput  |  Gli account di archiviazione di origine e di destinazione per il disco {0} sono diversi.  |
|  ContainerAlreadyOnLease  |  Esiste già un lease nel contenitore di archiviazione in cui si trova il BLOB con URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  La richiesta di spostamento delle risorse contiene risorse dell'insieme di credenziali delle chiavi a cui fanno riferimento una o più istanze di {0} nella richiesta. Questo scenario non è attualmente supportato nello spostamento tra sottoscrizioni. Esaminare i dettagli sull'errore per gli ID di risorsa dell'insieme di credenziali delle chiavi.  |
|  DiagnosticsOperationInternalError  |  Si è verificato un errore interno durante l'elaborazione di un profilo di diagnostica della macchina virtuale {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Il BLOB {0} è già usato da un altro disco appartenente alla macchina virtuale '{1}'. Per informazioni di riferimento sul disco, è possibile esaminare i metadati del BLOB.  |
|  DiskBlobNotFound  |  Il BLOB VHD con URI {0} per il disco '{1}' non è stato trovato.  |
|  DiskBlobNotFound  |  Il BLOB VHD con URI {0} non è stato trovato.  |
|  DiskEncryptionKeySecretMissingTags  |  Il segreto {0} non contiene i tag {1}. Aggiornare la versione del segreto, aggiungere i tag richiesti e riprovare.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  L'annullamento del wrapping del valore {0} del segreto tramite la chiave {1} non è riuscito.  |
|  DiskImageNotReady  |  L'immagine del disco {0} si trova nello stato {1}. Riprovare quando l'immagine è pronta.  |
|  DiskPreparationError  |  Si sono verificati uno o più errori durante la preparazione dei dischi della macchina virtuale. Per informazioni, vedere la visualizzazione dell'istanza del disco.  |
|  DiskProcessingError  |  L'elaborazione del disco è stata interrotta perché la macchina virtuale contiene altri dischi in stato di errore.  |
|  ImageBlobNotFound  |  Il BLOB VHD con URI {0} per il disco '{1}' non è stato trovato.  |
|  ImageBlobNotFound  |  Il BLOB VHD con URI {0} non è stato trovato.  |
|  IncorrectDiskBlobType  |  I BLOB del disco possono essere solo di tipo BLOB di pagine. Il BLOB {0} per il disco '{1}' è di tipo BLOB in blocchi.  |
|  IncorrectDiskBlobType  |  I BLOB del disco possono essere solo di tipo BLOB di pagine. Il BLOB {0} è del tipo '{1}'.  |
|  IncorrectImageBlobType  |  I BLOB del disco possono essere solo di tipo BLOB di pagine. Il BLOB {0} per il disco '{1}' è di tipo BLOB in blocchi.  |
|  IncorrectImageBlobType  |  I BLOB del disco possono essere solo di tipo BLOB di pagine. Il BLOB {0} è del tipo '{1}'.  |
|  InternalOperationError  |  Non è stato possibile risolvere l'account di archiviazione {0}. Verificare che sia stato creato con il provider delle risorse di archiviazione nello stesso percorso della risorsa di calcolo.  |
|  InternalOperationError  |  Le attività di ricerca obiettivo {0} non sono riuscite.  |
|  InternalOperationError  |  Si è verificato un errore durante la convalida del profilo di rete della macchina virtuale '{0}'.  |
|  InvalidAccountType  |  Il tipo di account {0} non è valido.  |
|  InvalidParameter  |  Il valore del parametro {0} non è valido.  |
|  InvalidParameter  |  La password amministratore specificata non è consentita.  |
|  InvalidParameter  |  La password fornita deve avere una lunghezza compresa tra {0} e {1} caratteri e deve soddisfare almeno {2} dei seguenti requisiti di complessità delle password: <ol><li> Contiene un carattere maiuscolo</li><li>Contiene un carattere minuscolo</li><li>Contiene una cifra numerica</li><li>Contiene un carattere speciale.</li></ol>  |
|  InvalidParameter  |  Il nome utente amministratore specificato non è consentito.  |
|  InvalidParameter  |  Non è possibile collegare un disco del sistema operativo esistente se la macchina virtuale è stata creata da una piattaforma o da un'immagine utente.  |
|  InvalidParameter  |  Il nome del contenitore {0} non è valido. I nomi dei contenitori devono avere una lunghezza compresa tra 3 e 63 caratteri e possono contenere solo caratteri alfanumerici minuscoli e trattini. Il trattino deve essere preceduto e seguito da un carattere alfanumerico.  |
|  InvalidParameter  |  Il nome del contenitore {0} nell'URL {1} non è valido. I nomi dei contenitori devono avere una lunghezza compresa tra 3 e 63 caratteri e possono contenere solo caratteri alfanumerici minuscoli e trattini. Il trattino deve essere preceduto e seguito da un carattere alfanumerico.  |
|  InvalidParameter  |  Il nome del BLOB nell'URL {0} contiene una barra. Questo carattere non è attualmente supportato per i dischi.  |
|  InvalidParameter  |  L'URI {0} non sembra essere un URI BLOB corretto.  |
|  InvalidParameter  |  Un disco denominato '{0}' usa già lo stesso LUN: {1}.  |
|  InvalidParameter  |  Un disco denominato '{0}' esiste già.  |
|  InvalidParameter  |  Non è possibile specificare gli override dell'immagine utente per un disco già definito nel riferimento all'immagine specificato.  |
|  InvalidParameter  |  Un disco denominato '{0}' usa già lo stesso URL VHD {1}.  |
|  InvalidParameter  |  Il conteggio del dominio di errore specificato {0} deve essere compreso nell'intervallo {1}-{2}.  |
|  InvalidParameter  |  Il tipo di licenza {0} non è valido. I tipi di licenza validi sono: Windows_Client o Windows_Server, con distinzione tra maiuscole e minuscole.  |
|  InvalidParameter  |  Il nome dell'host Linux non può superare i {0} caratteri o contenere i caratteri seguenti: {1}.  |
|  InvalidParameter  |  Il percorso di destinazione per le chiavi pubbliche SSH è attualmente limitato al valore predefinito {0} a causa di un errore noto nell'agente di provisioning di Linux.  |
|  InvalidParameter  |  Esiste già un disco in corrispondenza del numero di unità logica (LUN) {0}.  |
|  InvalidParameter  |  La sottoscrizione {0} della richiesta deve corrispondere alla sottoscrizione {1} contenuta nell'ID del disco gestito.  |
|  InvalidParameter  |  I dati personalizzati in OSProfile devono essere con codifica Base 64 e con una lunghezza massima di {0} caratteri.  |
|  InvalidParameter  |  Il nome BLOB nell'URL {0} deve terminare con l'estensione '{1}'.  |
|  InvalidParameter  |  {0}' non è un prefisso del nome BLOB VHD acquisito valido. Un prefisso valido corrisponde a regex '{1}'.  |
|  InvalidParameter  |  Non è possibile aggiungere i certificati alla macchina virtuale se non è stato eseguito il provisioning dell'agente della macchina virtuale.  |
|  InvalidParameter  |  Esiste già un disco in corrispondenza del numero di unità logica (LUN) {0}.  |
|  InvalidParameter  |  Non è possibile creare la macchina virtuale perché le dimensioni {0} richieste non sono disponibili nel cluster in cui il set di disponibilità è attualmente allocato. Le dimensioni disponibili sono: {1}. Per altre informazioni sulla strategia di ridimensionamento delle macchine virtuali, vedere https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Le dimensioni di macchina virtuale {0} richieste non sono disponibili nell'area corrente. Le dimensioni disponibili nell'area corrente sono: {1}. Per altre informazioni sulle dimensioni di macchina virtuale disponibili in ogni area, vedere https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Le dimensioni di macchina virtuale {0} richieste non sono disponibili nell'area corrente. Per altre informazioni sulle dimensioni di macchina virtuale disponibili in ogni area, vedere https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Il nome utente amministratore di Windows non può superare i {0} caratteri, terminare con un punto (.) o contenere i seguenti caratteri: {1}.  |
|  InvalidParameter  |  Il nome del computer di Windows non può contenere più di {0} caratteri, non può essere interamente numerico e non può contenere i caratteri seguenti: {1}.  |
|  MissingMoveDependentResources  |  La richiesta di spostamento delle risorse non contiene tutte le risorse dipendenti. Controllare i dettagli dell'errore per individuare gli ID delle risorse mancanti.  |
|  MoveResourcesHaveInvalidState  |  La richiesta di spostamento delle risorse contiene macchine virtuali associate ad account di archiviazione non validi. Verificare i dettagli per questi ID di risorsa e per i nomi degli account di archiviazione di riferimento.  |
|  MoveResourcesHavePendingOperations  |  La richiesta di spostamento delle risorse contiene risorse per le quali è in sospeso un'operazione. Controllare i dettagli per individuare gli ID di queste risorse. Riprovare l'operazione dopo aver completato le operazioni in sospeso.  |
|  MoveResourcesNotFound  |  La richiesta di spostamento delle risorse fa riferimento a risorse non disponibili. Controllare i dettagli per individuare gli ID di queste risorse.  |
|  NetworkingInternalOperationError  |  Errore di allocazione di rete sconosciuto.  |
|  NetworkingInternalOperationError  |  Errore di allocazione di rete sconosciuto.  |
|  NetworkingInternalOperationError  |  Si è verificato un errore interno durante l'elaborazione di un profilo di rete della macchina virtuale.  |
|  NotFound  |  Il set di disponibilità {0} non è stato trovato.  |
|  NotFound  |  La macchina virtuale di origine '{0}' specificata nella richiesta non esiste in questa località di Azure.  |
|  NotFound  |  Il tenant con ID {0} non è stato trovato.  |
|  NotFound  |  L'immagine {0} non è stata trovata.  |
|  NotSupported  |  Il tipo di licenza è {0}, ma il BLOB {1} dell'immagine non è locale.  |
|  OperationNotAllowed  |  Il set di disponibilità {0} non può essere eliminato. Prima di eliminare un set di disponibilità, verificare che non contenga macchine virtuali.  |
|  OperationNotAllowed  |  Non è consentito modificare lo SKU del set di disponibilità dal tipo 'Allineato' al tipo 'Classico'.  |
|  OperationNotAllowed  |  Non è possibile modificare le estensioni nella macchina virtuale quando non è in esecuzione.  |
|  OperationNotAllowed  |  L'azione di acquisizione è supportata solo su macchine virtuali con dischi basati su BLOB. Per creare un'immagine da una macchina virtuale gestita, usare le API della risorsa 'Immagine'.  |
|  OperationNotAllowed  |  Non sarà possibile creare la risorsa {0} dall'immagine {1} fino a quando la creazione dell'immagine non verrà completata.  |
|  OperationNotAllowed  |  Gli aggiornamenti alle impostazioni di crittografia non sono consentiti quando la macchina virtuale è allocata. Riprovare dopo che la macchina virtuale è stata deallocata  |
|  OperationNotAllowed  |  L'aggiunta di un disco gestito a una macchina virtuale con dischi basati su BLOB non è supportata.  |
|  OperationNotAllowed  |  Il numero massimo di dischi dati che è possibile collegare a una macchina virtuale di queste dimensioni è {0}.  |
|  OperationNotAllowed  |  L'aggiunta di un disco basato su BLOB a una macchina virtuale con dischi gestiti non è supportata.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita sull'immagine '{1}' perché l'immagine è contrassegnata per l'eliminazione. È solo possibile ripetere l'operazione di eliminazione oppure attendere il completamento di un'operazione in corso.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché la macchina virtuale è generalizzata.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita perché la raccolta di punti di ripristino '{1}' è contrassegnata per l'eliminazione.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nell'estensione della macchina virtuale '{1}' perché è contrassegnata per l'eliminazione. È solo possibile ripetere l'operazione di eliminazione oppure attendere il completamento di un'operazione in corso.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita perché è in corso il provisioning delle macchine virtuali '{1}' con l'immagine '{2}'.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita perché il set di scalabilità di macchine virtuali '{1}' sta usando l'immagine '{2}'.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché la macchina virtuale è contrassegnata per l'eliminazione. È solo possibile ripetere l'operazione di eliminazione oppure attendere il completamento di un'operazione in corso.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché la macchina virtuale è deallocata o contrassegnata per la deallocazione.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché la macchina virtuale è in esecuzione. Usare il comando di spegnimento esplicito se la macchina virtuale viene spenta dall'interno del sistema operativo guest.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché la macchina virtuale non è deallocata.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché l'estensione '{2}' della macchina virtuale presenta uno stato di errore.  |
|  OperationNotAllowed  |  L'operazione '{0}' non è consentita nella macchina virtuale '{1}' perché è in esecuzione un'altra operazione.  |
|  OperationNotAllowed  |  L'operazione '{0}' richiede che la macchina virtuale '{1}' sia di tipo generalizzato.  |
|  OperationNotAllowed  |  L'operazione richiede che la macchina virtuale sia in esecuzione (o impostata per l'esecuzione).  |
|  OperationNotAllowed  |  Un disco con dimensioni pari a {0} GB, inferiori alle dimensioni di {1} GB del disco corrispondente nell'immagine, non è consentito.  |
|  OperationNotAllowed  |  È possibile aggiungere le estensioni del set di scalabilità di macchine virtuali del gestore '{0}' solo al momento della creazione del set di scalabilità di macchine virtuali.  |
|  OperationNotAllowed  |  È possibile eliminare le estensioni del set di scalabilità di macchine virtuali del gestore '{0}' solo al momento dell'eliminazione del set di scalabilità di macchine virtuali.  |
|  OperationNotAllowed  |  La macchina virtuale '{0}' usa già dischi gestiti.  |
|  OperationNotAllowed  |  La macchina virtuale '{0}' appartiene al set di disponibilità '{1}' di tipo 'Classico'. Aggiornare il set di disponibilità affinché usi lo SKU 'Allineato' e quindi ripetere la conversione.  |
|  OperationNotAllowed  |  La macchina virtuale creata dall'immagine non può contenere dischi basati su BLOB. Tutti i dischi devono essere dischi gestiti.  |
|  OperationNotAllowed  |  L'operazione di acquisizione non può essere completata perché la macchina virtuale non è generalizzata.  |
|  OperationNotAllowed  |  Le operazioni di gestione nella macchina virtuale '{0}' non sono consentite perché i dischi della macchina virtuale sono in fase di conversione a dischi gestiti.  |
|  OperationNotAllowed  |  Un'operazione in corso sta modificando lo stato di alimentazione della macchina virtuale {0} a {1}. Eseguire l'operazione {2} più tardi.  |
|  OperationNotAllowed  |  Non è possibile aggiungere o aggiornare la macchina virtuale. Le dimensioni di macchina virtuale {0} richieste potrebbero non essere disponibili nell'unità di allocazione esistente. Per altre informazioni sulla strategia di ridimensionamento delle macchine virtuali, vedere https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Non è possibile ridimensionare la macchina virtuale perché le dimensioni {0} richieste non sono disponibili nel cluster in cui il set di disponibilità è attualmente allocato. Le dimensioni disponibili sono: {1}. Per altre informazioni sulla strategia di ridimensionamento delle macchine virtuali, vedere https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Non è possibile ridimensionare la macchina virtuale perché le dimensioni {0} richieste non sono disponibili nel cluster in cui la macchina virtuale è attualmente allocata. Per passare a una macchina virtuale di dimensioni {1}, deallocarla (tramite l'operazione Arresta nel portale di Azure) e ripetere l'operazione di ridimensionamento. Per altre informazioni sulla strategia di ridimensionamento delle macchine virtuali, vedere https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Il provisioning del sistema operativo per la macchina virtuale '{0}' non è riuscito perché il sistema operativo guest è ancora in fase di provisioning.  |
|  OSProvisioningClientError  |  Il provisioning del sistema operativo per la macchina virtuale '{0}' non è riuscito. Dettagli dell'errore: {1} Assicurarsi che l'immagine sia stata preparata correttamente (generalizzata). <ul><li>Istruzioni per Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  La generazione della chiave host SSH non è riuscita. Dettagli sull'errore: {0}. Per risolvere il problema, verificare che l'agente Linux sia stato configurato correttamente. <ul><li>Per le istruzioni, vedere: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  Il nome utente specificato per la macchina virtuale non è valido per questa distribuzione Linux. Dettagli sull'errore: {0}.  |
|  OSProvisioningInternalError  |  Il provisioning del sistema operativo non è riuscito per la macchina virtuale '{0}' a causa di un errore interno.  |
|  OSProvisioningTimedOut  |  Il provisioning del sistema operativo per la macchina virtuale '{0}' non è stato completato nel tempo assegnato. La macchina virtuale può comunque completare il provisioning. Controllare lo stato del provisioning più tardi.  |
|  OSProvisioningTimedOut  |  Il provisioning del sistema operativo per la macchina virtuale '{0}' non è stato completato nel tempo assegnato. La macchina virtuale può comunque completare il provisioning. Controllare lo stato del provisioning più tardi. Assicurarsi inoltre che l'immagine sia stata preparata correttamente (generalizzata).   <ul><li>Istruzioni per Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Istruzioni per Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Il provisioning del sistema operativo per la macchina virtuale '{0}' non è stato completato nel tempo assegnato. Tuttavia, è stato rilevato che l'agente guest di macchine virtuali è in esecuzione. Questo può indicare che il sistema operativo guest non è stato preparato correttamente per essere usato come immagine di macchina virtuale (con CreateOption=FromImage). Per risolvere il problema, usare il disco rigido virtuale così com'è con CreateOption=Attach oppure prepararlo correttamente per essere usato come immagine:   <ul><li>Istruzioni per Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Istruzioni per Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Le dimensioni della macchina virtuale richieste non sono attualmente disponibili nel percorso selezionato.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Non è possibile aggiornare la risorsa in questo momento perché è in corso un aggiornamento della piattaforma. Riprovare più tardi.  |
|  StorageAccountLimitation  |  L'account di archiviazione '{0}' non supporta i BLOB di pagine richiesti per creare i dischi.  |
|  StorageAccountLimitation  |  L'account di archiviazione '{0}' ha superato la quota allocata.  |
|  StorageAccountLocationMismatch  |  Non è stato possibile risolvere l'account di archiviazione {0}. Verificare che sia stato creato con il provider delle risorse di archiviazione nello stesso percorso della risorsa di calcolo.  |
|  StorageAccountNotFound  |  L'account di archiviazione {0} non è stato trovato. Verificare che l'account di archiviazione non sia stato eliminato e che appartenga allo stesso percorso di Azure della macchina virtuale.  |
|  StorageAccountNotRecognized  |  Usare un account di archiviazione gestito dal provider delle risorse di archiviazione. L'uso di {0} non è supportato.  |
|  StorageAccountOperationInternalError  |  Si è verificato un errore interno durante l'accesso all'account di archiviazione {0}.  |
|  StorageAccountSubscriptionMismatch  |  L'account di archiviazione {0} non appartiene alla sottoscrizione {1}.  |
|  StorageAccountTooBusy  |  L'account di archiviazione '{0}' è attualmente occupato. Valutare l'uso di un altro account.  |
|  StorageAccountTypeNotSupported  |  Il disco {0} usa {1} che è un account di archiviazione BLOB. Riprovare con un account di archiviazione per utilizzo generico.  |
|  StorageAccountTypeNotSupported  |  L'account di archiviazione {0} è di tipo {1}. La diagnostica di avvio supporta account di archiviazione di tipo {2}.  |
|  SubscriptionNotAuthorizedForImage  |  La sottoscrizione non è autorizzata.  |
|  TargetDiskBlobAlreadyExists  |  Il BLOB {0} esiste già. Specificare un URI BLOB diverso per creare un nuovo disco dati vuoto '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  L'operazione di acquisizione non può continuare perché il BLOB dell'immagine di destinazione {0} esiste già e il flag per sovrascrivere i BLOB VHD non è impostato. Eliminare il BLOB o impostare il flag per sovrascrivere i BLOB VHD, quindi riprovare.  |
|  TargetDiskBlobAlreadyExists  |  L'operazione di acquisizione non può continuare perché il BLOB dell'immagine di destinazione {0} contiene un lease attivo.   |
|  TargetDiskBlobAlreadyExists  |  Il BLOB {0} esiste già. Specificare un URI BLOB diverso come destinazione per il disco '{1}'.  |
|  TooManyVMRedeploymentRequests  |  Sono state ricevute troppe richieste di ridistribuzione per la macchina virtuale '{0}' o per le macchine virtuali incluse nello stesso set di disponibilità di questa macchina virtuale. Riprovare più tardi.  |
|  VHDSizeInvalid  |  Il valore {0} specificato per la dimensione del disco '{1}' con BLOB {2} non è valido. La dimensione del disco deve essere compresa tra {3} e {4}.  |
|  VMAgentStatusCommunicationError  |  La macchina virtuale '{0}' non ha segnalato lo stato per le estensioni o l'agente di macchine virtuali. Verificare che la macchina virtuale contenga un agente di macchine virtuali e che possa stabilire connessioni in uscita ad Archiviazione di Azure.  |
|  VMArtifactRepositoryInternalError  |  Si è verificato un errore durante la comunicazione con il repository di elementi per recuperare i dettagli sull'elemento della macchina virtuale.  |
|  VMArtifactRepositoryInternalError  |  Si è verificato un errore interno durante il recupero dei dati dell'elemento della macchina virtuale dal repository di elementi.  |
|  VMExtensionHandlerNonTransientError  |  Il gestore '{0}' ha segnalato un errore per l'estensione '{1}' della macchina virtuale con codice di errore terminale '{2}' e messaggio di errore '{3}'  |
|  VMExtensionManagementInternalError  |  Si è verificato un errore interno durante l'elaborazione dell'estensione della macchina virtuale '{0}'.  |
|  VMExtensionManagementInternalError  |  Si sono verificati più errori durante la preparazione delle estensioni della macchina virtuale. Per informazioni, vedere la visualizzazione dell'istanza dell'estensione della macchina virtuale.  |
|  VMExtensionProvisioningError  |  La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione '{0}'. Messaggio di errore: "{1}".  |
|  VMExtensionProvisioningError  |  Il provisioning di più estensioni della macchina virtuale non è riuscito nella macchina virtuale. Per informazioni, vedere la visualizzazione dell'istanza dell'estensione della macchina virtuale.  |
|  VMExtensionProvisioningTimeout  |  Timeout del provisioning dell'estensione della macchina virtuale '{0}'. L'installazione dell'estensione ha richiesto troppo tempo oppure non è stato possibile ottenere lo stato dell'estensione.  |
|  VMMarketplaceInvalidInput  |  Per la creazione di una macchina virtuale da un'immagine non Marketplace non sono richieste informazioni sul piano. Rimuovere le informazioni sul piano nella richiesta. Il nome del disco del sistema operativo è {0}.  |
|  VMMarketplaceInvalidInput  |  Le informazioni di acquisto non corrispondono. La distribuzione dall'immagine Marketplace non è riuscita. Il nome del disco del sistema operativo è {0}.  |
|  VMMarketplaceInvalidInput  |  Per la creazione di una macchina virtuale da un'immagine Marketplace sono richieste informazioni sul piano nella richiesta. Il nome del disco del sistema operativo è {0}.  |
|  VMNotFound  |  La macchina virtuale '{0}' non è stata trovata.  |
|  VMRedeploymentFailed  |  La ridistribuzione della macchina virtuale '{0}' non è riuscita a causa di un errore interno. Riprovare più tardi.  |
|  VMRedeploymentTimedOut  |  La ridistribuzione della macchina virtuale '{0}' non è stata completata nel tempo previsto. È possibile che venga comunque completata in futuro. In alternativa, inviare di nuovo la richiesta.  |
|  VMStartTimedOut  |  La macchina virtuale '{0}' non è stata avviata nel tempo assegnato. La macchina può comunque essere avviata. Controllare lo stato di alimentazione più tardi.  |


## <a name="next-steps"></a>Passaggi successivi
Per ricevere assistenza, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.