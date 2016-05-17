Sono disponibili due tipi di account di archiviazione:

### Account di archiviazione di uso generico

Un account di archiviazione di uso generico permette di accedere ai servizi di Archiviazione di Azure come tabelle, code, file, BLOB e dischi di macchina virtuale di Azure con un unico account. Questo tipo di account di archiviazione offre due livelli di prestazioni:

- Un livello di prestazioni di archiviazione Standard che consente di archiviare tabelle, code, file, BLOB e dischi di macchina virtuale di Azure.
- Un livello di prestazioni di archiviazione Premium che attualmente supporta solo dischi di macchina virtuale di Azure. Per una panoramica approfondita del servizio di archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

### Account di archiviazione BLOB

Gli account di archiviazione BLOB sono account di archiviazione specializzati per l'archiviazione dei dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico esistenti e condividono tutte le straordinarie funzionalità di durabilità, disponibilità, scalabilità e prestazioni già usate, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta. Per applicazioni che richiedono solo archivi BLOB in blocchi o BLOB di aggiunta, è consigliabile usare account di archiviazione BLOB.

> [AZURE.NOTE] Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine.

Gli account di archiviazione BLOB espongono l'attributo **Livello di accesso**, che può essere specificato durante la creazione di account e modificato successivamente in base alle esigenze. I livelli di accesso che possono essere specificati in base al modello di accesso ai dati sono di due tipi:

- Livello di accesso **frequente**, che indica un accesso più frequente agli oggetti nell'account di archiviazione. Questo permette di archiviare i dati a un costo di accesso inferiore.
- Livello di accesso **sporadico**, che indica un accesso meno frequente agli oggetti nell'account di archiviazione. Questo permette di archiviare i dati a un costo di archiviazione dati inferiore.

Se il modello di utilizzo dei dati cambia, è anche possibile passare da uno di questi livelli di accesso all'altro in qualsiasi momento. La modifica del livello di accesso può comportare costi aggiuntivi. Per informazioni dettagliate, vedere [Prezzi e fatturazione per gli account di archiviazione BLOB](../articles/storage/storage-blob-storage-tiers.md#pricing-and-billing).

Per informazioni dettagliate sugli account di archiviazione BLOB, vedere [Archivio BLOB di Azure: livelli Frequente e Sporadico](../articles/storage/storage-blob-storage-tiers.md).

Prima di creare un account di archiviazione, è necessario effettuare una sottoscrizione di Azure, ovvero a un piano per accedere ai diversi servizi di Azure. Per iniziare a usare Azure, è possibile usare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/). Se si decide di acquistare un piano di sottoscrizione, è possibile scegliere una delle numerose [opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/). Gli utenti [iscritti a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ricevono crediti mensili gratuiti che possono essere usati con i servizi di Azure, incluso il servizio Archiviazione di Azure. Per informazioni sui prezzi in base al volume, vedere la pagina relativa ai [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Per informazioni su come creare un account di archiviazione, vedere [Creare un account di archiviazione](../articles/storage/storage-create-storage-account.md#create-a-storage-account). È possibile creare un massimo di 100 account di archiviazione con denominazione univoca con una singola sottoscrizione. Per informazioni dettagliate sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md).
