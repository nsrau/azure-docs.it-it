<properties
   pageTitle="Informazioni generali sulla sicurezza di Archiviazione di Azure | Microsoft Azure"
   description="Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure che possono essere usate con Archiviazione di Azure."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Informazioni generali sulla sicurezza di Archiviazione di Azure

Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Archiviazione di Azure offre un set completo di funzionalità di sicurezza:

- L'account di archiviazione può essere protetto con il controllo degli accessi in base al ruolo e Azure Active Directory.
- È possibile proteggere i dati in transito tra un'applicazione e Azure usando la crittografia lato client, HTTPS o SMB 3.0.
- I dati possono essere impostati per la crittografia automatica quando vengono scritti in Archiviazione di Azure con Crittografia del servizio di archiviazione di Azure.
- I dischi di dati e del sistema operativo usati dalle macchine virtuali possono essere impostati per la crittografia con Crittografia dischi di Azure.
- È possibile concedere l'accesso delegato agli oggetti dati in Archiviazione di Azure usando le firme di accesso condiviso.

Questo articolo offre informazioni generali su ognuna di queste funzionalità di sicurezza principali che possono essere usate con Archiviazione di Azure. Per maggiori informazioni sono disponibili collegamenti ad articoli contenenti informazioni dettagliate su ogni funzionalità.

Per un'analisi più approfondita della sicurezza in Archiviazione di Azure, vedere la [Guida alla sicurezza di Archiviazione di Azure](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md).

Qui di seguito sono elencati gli argomenti trattati in questo articolo:

- Controllo degli accessi in base al ruolo
- Gestione delle chiavi dell'account di archiviazione
- Accesso delegato agli oggetti di archiviazione
- Crittografia in transito
- Crittografia di dati inattivi/Crittografia del servizio di archiviazione
- Analisi archiviazione

## Controllo degli accessi in base al ruolo

È possibile proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo. Ogni sottoscrizione di Azure è associata a un tenant di Azure Active Directory (AD). Gli utenti, i gruppi e le applicazioni da tale directory possono gestire le risorse nella sottoscrizione di Azure. Questi diritti di accesso vengono concessi assegnando il ruolo di controllo degli accessi appropriato a utenti, gruppi e applicazioni in un ambito specifico.

È possibile usare il controllo degli accessi in base al ruolo per le operazioni di gestione dell'account di archiviazione di Azure. Con il controllo degli accessi in base al ruolo è possibile definire chi può:

- Gestire l'account di archiviazione.
- Leggere le chiavi dell'account di archiviazione e usare queste chiavi per accedere a BLOB, code, tabelle e file.
- Rigenerare le chiavi di archiviazione.

Altre informazioni:

- [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## Gestione delle chiavi dell'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio. È possibile mantenere le connessioni all'account di archiviazione con una chiave di accesso mentre si rigenera l'altra.

Le chiavi dell'account di archiviazione, insieme al nome dell'account di archiviazione, possono essere usate per accedere agli oggetti dati archiviati nell'account di archiviazione, ad esempio BLOB, entità all'interno di una tabella, messaggi nella coda e file in una condivisione file di Azure. Con il controllo degli accessi in base al ruolo è possibile gestire l'accesso alle chiavi dell'account di archiviazione per controllare l'accesso agli oggetti dati stessi.

Altre informazioni:

- [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md)

## Accesso delegato agli oggetti di archiviazione

Una firma di accesso condiviso (SAS) è una stringa contenente un token di sicurezza che può essere collegato a un URI che consente di delegare l'accesso a oggetti di archiviazione e specificare limitazioni, ad esempio le autorizzazioni e l'intervallo di data/ora di accesso.

Fornire la chiave dell'account di archiviazione è come condividere le chiavi dell'ambito di archiviazione, infatti concede l'accesso completo. Con le firme di accesso condiviso è possibile assegnare a un client solo le autorizzazioni necessarie per un periodo di tempo limitato. È possibile:

- È possibile concedere l'accesso a BLOB, contenitori, messaggi nella coda, file e tabelle. Con le tabelle è effettivamente possibile concedere l'autorizzazione per accedere a un intervallo di entità nella tabella specificando gli intervalli di chiavi di partizione e di riga per cui si vuole concedere l'accesso all'utente.
- Specificare che le richieste eseguite con una firma di accesso condiviso sono limitate a un determinato indirizzo IP o un intervallo di indirizzi IP all'esterno di Azure.
- Specificare che le richieste debbano essere eseguite con un protocollo specifico (HTTPS o HTTP/HTTPS). Ciò significa che se si vuole consentire il traffico HTTPS, è possibile impostare il protocollo richiesto solo su HTTPS e il traffico HTTP verrà bloccato.

Altre informazioni:

- [Informazioni sul modello di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md)

## Crittografia in transito
La crittografia in transito è un meccanismo di protezione dei dati durante la trasmissione tra le reti. Con Archiviazione di Azure è possibile proteggere i dati con:

- Crittografia a livello di trasporto, ad esempio HTTPS quando si trasferiscono dati all'interno o all'esterno di Archiviazione di Azure.
- Crittografia di rete, ad esempio la crittografia SMB 3.0 per le condivisioni file di Azure.
- Crittografia lato client, per crittografare i dati prima che siano trasferiti nella risorsa di archiviazione e decrittografarli dopo il trasferimento dalla risorsa di archiviazione.

### Crittografia a livello di trasporto

Per garantire la sicurezza dei dati di Archiviazione di Azure è possibile crittografare i dati tra il client e Archiviazione di Azure. È consigliabile [abilitare HTTPS](../app-service-web/web-sites-configure-ssl-certificate.md) quando si chiamano le API REST o si accede a oggetti nella risorsa di archiviazione. Sono anche disponibili le [firme di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS) che possono essere usate per delegare l'accesso a oggetti di Archiviazione di Azure. Queste includono un parametro facoltativo, "protocol", che consente di specificare che possa essere usato solo il protocollo HTTPS quando si usa la firma di accesso condiviso. Ciò garantisce che chiunque invii collegamenti con i token di firma di accesso condiviso usi il protocollo corretto.

### Crittografia di rete per l'accesso alla condivisione file

SMB 3.0 supporta la crittografia e può essere usato con Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, consentendo l'accesso tra aree e anche l'accesso sul desktop.

Le condivisioni file di Azure possono essere usate con le macchine virtuali Linux. Il client SMB Linux non supporta ancora la crittografia, quindi l'accesso è consentito solo all'interno di un'area di Azure. Il supporto della crittografia per Linux è in programma. Quando verrà aggiunta la crittografia, per accedere alle condivisioni file di Azure in Linux si avrà la stessa crittografia a livello di rete usata per Windows.

Altre informazioni:

- [Come usare Archiviazione file di Azure con Linux](../storage/storage-how-to-use-files-linux.md)
- [Introduzione ad Archiviazione file di Azure in Windows](../storage/storage-dotnet-how-to-use-files.md)
- [Analisi di archiviazione file di Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### Crittografia lato client

La crittografia lato client consente di assicurare che i dati siano protetti durante il trasferimento tra un'applicazione client e Archiviazione di Azure. I dati vengono crittografati prima di essere trasferiti nell'archiviazione di Azure. Quando si recuperano i dati dall'archiviazione di Azure, vengono decrittografati dopo la ricezione sul lato client. Anche se i dati vengono crittografati mentre sono in transito, è anche possibile usare HTTPS per attenuare gli errori di rete che interessano l'integrità dei dati.

Altre informazioni:

- [Crittografia lato client per Archiviazione di Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Serie di controlli della sicurezza del cloud: crittografia dei dati in transito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## Crittografia di dati inattivi

Esistono tre funzionalità di Azure che consentono di crittografare dati inattivi:

- Crittografia del servizio di archiviazione
- Crittografia lato client
- Azure Disk Encryption

### Crittografia del servizio di archiviazione

Crittografia del servizio di archiviazione consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure. Quando si leggono i dati dall'archiviazione di Azure, verranno decrittografati dal servizio di archiviazione prima di essere restituiti. Ciò consente di proteggere i dati senza dover modificare il codice o aggiungere codice alle applicazioni.

[Crittografia del servizio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) è disponibile per l'[l'archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/). Per informazioni su altri tipi di archiviazione di Azure, vedere [File](https://azure.microsoft.com/services/storage/files/), [Disco (Archiviazione Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Tabella](https://azure.microsoft.com/services/storage/tables/) e [Coda](https://azure.microsoft.com/services/storage/queues/).

Altre informazioni:

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/storage-service-encryption.md)

### Crittografia lato client

La crittografia lato client è stata citata nella sezione riguardante la crittografia dei dati in transito. Questa funzionalità consente di crittografare a livello di codice i dati in un'applicazione client prima dell'invio in rete per la scrittura nell'archiviazione di Azure e di decrittografare i dati a livello di codice dopo il recupero dall'archiviazione di Azure.

In questo modo è disponibile la crittografia in transito, ma anche la funzionalità di crittografia dei dati inattivi. Si noti che anche se i dati vengono crittografati in transito, è possibile usare HTTPS per sfruttare i controlli di integrità dei dati incorporati, che contribuiscono ad attenuare gli errori di rete che interessano l'integrità dei dati.

Altre informazioni:

- [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](../storage/storage-client-side-encryption.md)

### Azure Disk Encryption

Crittografia dischi di Azure per le macchine virtuali consente di soddisfare i requisiti di conformità e sicurezza dell'organizzazione, grazie alla possibilità di crittografare i dischi delle macchine virtuali, inclusi i dischi di avvio e di dati, con chiavi e criteri gestiti nell'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/).

Crittografia dischi per le macchine virtuali funziona con sistemi operativi sia Linux, sia Windows. Usa l'insieme di credenziali delle chiavi per proteggere, gestire e controllare l'uso delle chiavi di crittografia dei dischi. Tutti i dati nei dischi delle macchine virtuali vengono crittografati mentre sono inattivi, usando una tecnologia di crittografia standard del settore negli account di archiviazione di Azure. La soluzione Crittografia dischi per Windows è basata sulla [Crittografia unità BitLocker di Microsoft](https://technet.microsoft.com/library/cc732774.aspx) e la soluzione Linux è basata su [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Altre informazioni:

- [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Analisi archiviazione

Per ogni account di archiviazione è possibile abilitare Analisi archiviazione di Azure per eseguire la registrazione e archiviare dati di metrica. Si tratta di uno strumento molto utile quando si vogliono verificare le metriche delle prestazioni o risolvere i problemi con le prestazioni di un account di archiviazione.

Un'altra parte di dati che è possibile visualizzare nel log di Analisi archiviazione è il metodo di autenticazione usato da un utente quando accede alla risorsa di archiviazione. Ad esempio, con l'archivio BLOB è possibile vedere se è stata usata una firma di accesso condiviso o le chiavi dell'account di archiviazione o se il BLOB usato era pubblico.

Altre informazioni:

- [Analisi dell'archiviazione](../storage/storage-analytics.md)
- [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Monitorare un account di archiviazione nel portale di Azure](../storage/storage-monitor-storage-account.md)
- [Risoluzione dei problemi end-to-end mediante le metriche e la registrazione di Archiviazione di Azure, AzCopy e Message Analyzer](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->