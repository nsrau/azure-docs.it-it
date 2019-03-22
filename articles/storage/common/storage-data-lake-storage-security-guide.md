---
title: Guida alla sicurezza di archiviazione per Azure Data Lake Storage Gen2 | Microsoft Docs
description: Descrive in dettaglio i vari metodi di protezione di Archiviazione di Azure, inclusi ad esempio il controllo degli accessi in base al ruolo e la crittografia del servizio di archiviazione
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/07/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 22b070e6d70208057c85ad6a2322cc440d12a0fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008203"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Guida alla sicurezza di Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 offre un set di funzionalità incorporate negli account di Archiviazione di Azure. Di conseguenza, tutti i riferimenti in questo articolo sono pensati per un account di archiviazione di Azure con spazio dei nomi gerarchico abilitato (funzionalità di Data Lake Storage Gen2).

- Tutti i dati scritti in Archiviazione di Azure vengono crittografati automaticamente con la [crittografia del servizio di archiviazione](storage-service-encryption.md). Per altre informazioni, vedere [Announcing Default Encryption for Azure Blobs, Files, Table and Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/) (Annuncio della crittografia predefinita per BLOB, file, tabelle e archiviazione code di Azure).
- Azure Active Directory (Azure AD) e il controllo degli accessi in base al ruolo sono supportati per Archiviazione di Azure sia per le operazioni di gestione delle risorse che per le operazioni sui dati, come illustrato di seguito:
    - È possibile assegnare ruoli Controllo degli accessi in base al ruolo con ambito impostato sull'account di archiviazione alle entità di sicurezza e usare Azure AD per autorizzare le operazioni di gestione delle risorse, ad esempio la gestione delle chiavi.
    - L'integrazione di Azure AD è supportata per le operazioni sui dati in Archiviazione di Azure. È possibile assegnare ruoli Controllo degli accessi in base al ruolo con ambito impostato su una sottoscrizione, un gruppo di risorse, un account di archiviazione o un singolo file system a un'entità di sicurezza o un'identità gestita per le risorse di Azure. Per altre informazioni, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md).
- È possibile concedere l'accesso delegato agli oggetti dati nell'archiviazione di Azure usando [firme di accesso condiviso](../storage-dotnet-shared-access-signature-part-1.md).

Questo articolo offre una panoramica di queste funzionalità di sicurezza che possono essere usate con Archiviazione di Azure. Sono disponibili i collegamenti ad articoli contenenti informazioni dettagliate per ogni funzionalità, per poter approfondire facilmente i concetti per ogni argomento.

Ecco gli argomenti trattati in questo articolo:

* [Sicurezza del piano di gestione](#management-plane-security) : proteggere l'account di archiviazione

  Il piano di gestione è costituito dalle risorse usate per gestire l'account di archiviazione. Questa sezione illustra il modello di distribuzione Azure Resource Manager e come usare il controllo degli accessi in base al ruolo per controllare l'accesso agli account di archiviazione. Descrive anche la gestione delle chiavi dell'account di archiviazione e come rigenerarle.
* [Sicurezza del piano dati](#data-plane-security) : proteggere l'accesso ai dati

  Questa sezione illustra come concedere l'accesso agli oggetti dati effettivi nell'account di archiviazione, ad esempio file e directory, usando firme di accesso condiviso e criteri di accesso archiviati. Vengono trattate anche le firme di accesso condiviso sia a livello di servizio che di account. Viene inoltre spiegato come limitare l'accesso a un indirizzo IP specifico o un intervallo di indirizzi IP, come limitare il protocollo usato per HTTPS e come revocare una firma di accesso condiviso senza attenderne la scadenza.
* [Crittografia in transito](#encryption-in-transit)

Questa sezione descrive come proteggere i dati durante il trasferimento da e verso un account di archiviazione con Data Lake Storage Gen2 abilitato. Verrà descritto l'uso consigliato di HTTPS.

## <a name="management-plane-security"></a>Sicurezza del piano di gestione

Il piano di gestione è costituito da operazioni che interessano lo stesso account di archiviazione. Ad esempio, è possibile creare o eliminare un account di archiviazione, ottenere un elenco di account di archiviazione in una sottoscrizione, recuperare le chiavi dell'account di archiviazione o rigenerarle.

Questa guida è incentrata sul modello di Resource Manager per la distribuzione, ovvero il mezzo per la creazione di account di archiviazione con funzionalità di Data Lake Storage Gen2. Con gli account di archiviazione di Resource Manager, invece di concedere l'accesso all'intera sottoscrizione, è possibile controllare l'accesso al piano di gestione in base a un livello più limitato usando il controllo degli accessi in base al ruolo.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo

Viene ora spiegato cos'è il controllo degli accessi in base al ruolo e come usarlo. Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. A utenti, gruppi e applicazioni in questa directory può essere consentito l'accesso per gestire le risorse nella sottoscrizione di Azure che usa il modello di distribuzione di Resource Manager. Questo tipo di protezione è definito controllo degli accessi in base al ruolo. Per gestire l'accesso, usare **Controllo di accesso (IAM)** nel portale di Azure.

Con il modello di Resource Manager si inserisce l'account di archiviazione in un gruppo di risorse e si controlla l'accesso al piano di quell'account di archiviazione specifico tramite Azure Active Directory. Ad esempio, è possibile concedere a utenti specifici la possibilità di accedere alle chiavi dell'account di archiviazione, mentre altri utenti possono visualizzare le informazioni sull'account di archiviazione, ma non accedere alle relative chiavi.

#### <a name="granting-access"></a>Concessione dell'accesso

L’accesso viene concesso assegnando i ruoli RBAC appropriati a utenti, gruppi e applicazioni nell'ambito corretto. Per concedere l'accesso all'intera sottoscrizione, assegnare un ruolo a livello di sottoscrizione. È possibile concedere l'accesso a tutte le risorse in un gruppo di risorse concedendo le autorizzazioni al gruppo stesso. È anche possibile assegnare ruoli specifici a risorse specifiche, come gli account di archiviazione.

Ecco i punti principali che occorre conoscere sull'uso del controllo degli accessi in base al ruolo per accedere alle operazioni di gestione di un account di archiviazione di Azure:

* A chiunque abbia un'autorizzazione di accesso agli oggetti dati nell'account di archiviazione è possibile concedere l'autorizzazione di lettura delle chiavi dell'account di archiviazione. Questo utente potrà quindi usare le chiavi per accedere a file e directory.
* I ruoli possono essere assegnati a un account utente specifico, a un gruppo di utenti o a un'applicazione specifica.
* Per ogni ruolo è disponibile un elenco di azioni e non azioni. Per il ruolo Collaboratore Macchina virtuale, ad esempio, è disponibile un'azione "listKeys" che consente la lettura delle chiavi dell'account di archiviazione. Per il ruolo Collaboratore sono disponibili "non azioni", come l'aggiornamento dell'accesso per gli utenti in Active Directory.
* Di seguito sono elencati alcuni dei ruoli per l'archiviazione:

  * Proprietario: può gestire qualsiasi elemento, compreso l'accesso.
  * Collaboratore: può eseguire tutte le operazioni consentite al proprietario, tranne l'assegnazione dell'accesso. Un utente con questo ruolo può visualizzare e rigenerare le chiavi dell'account di archiviazione. Con le chiavi di account di archiviazione può accedere gli oggetti dati.
  * Lettore: può visualizzare le informazioni sull'account di archiviazione, tranne i segreti. Se ad esempio si assegna a un utente un ruolo con autorizzazioni di lettura per l'account di archiviazione, l'utente potrà visualizzare le proprietà dell'account di archiviazione, ma non apportare modifiche alle proprietà o visualizzare le chiavi dell'account di archiviazione.
  * Collaboratore Account di archiviazione: può gestire l'account di archiviazione, leggere i gruppi di risorse e le risorse della sottoscrizione, nonché creare e gestire distribuzioni di gruppi di risorse della sottoscrizione. Potendo accedere alle chiavi dell'account di archiviazione, può accedere anche al piano dati.
  * Amministratore Accesso utenti: può gestire l'accesso degli utenti all'account di archiviazione. Ad esempio, può concedere l'accesso in lettura a un utente specifico.
  * Collaboratore Macchina virtuale: può gestire le macchine virtuali, ma non l'account di archiviazione a cui è connesso. Questo ruolo consente di elencare le chiavi dell'account di archiviazione e ciò significa che l'utente al quale si assegna questo ruolo può aggiornare il piano dati.

    Per creare una macchina virtuale, un utente deve poter creare il file VHD corrispondente in un account di archiviazione. A questo scopo, dovrà essere in grado di recuperare la chiave dell'account di archiviazione e passarla all'API per la creazione della VM. Deve quindi avere questa autorizzazione per poter elencare le chiavi dell'account di archiviazione.
* La capacità di definire ruoli personalizzati è una funzionalità che consente di comporre un set di azioni da un elenco di azioni disponibili che possono essere eseguite sulle risorse di Azure.
* L'utente deve essere configurato in Azure Active Directory prima dell'assegnazione di un ruolo.
* È possibile creare un creare un report di chi ha concesso o revocato un tipo di accesso e a chi e in quale ambito usando PowerShell o l'interfaccia della riga di comando di Azure.

#### <a name="resources"></a>Risorse

* [Controllo degli accessi in base al ruolo di Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Questo articolo descrive il controllo degli accessi in base al ruolo di Azure Active Directory e il relativo funzionamento.
* [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)

  Questo articolo illustra tutti i ruoli predefiniti disponibili nel controllo degli accessi in base al ruolo.
* [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Rigenerazione delle chiavi dell'account di archiviazione

Le chiavi dell'account di archiviazione sono stringhe a 512 bit create da Azure che, insieme al nome dell'account di archiviazione, possono essere usate per accedere agli oggetti dati archiviati nell'account di archiviazione, ad esempio BLOB, entità all'interno di una tabella, messaggi nella coda e file in una condivisione file di Azure. Il controllo dell'accesso alle chiavi dell'account di archiviazione consente di controllare l'accesso al piano dati di tale account di archiviazione.

Per ogni account di archiviazione sono disponibili due chiavi, dette "Chiave 1" e "Chiave 2" nel [portale di Azure](https://portal.azure.com/) e nei cmdlet di PowerShell. Queste possono essere rigenerate manualmente usando uno dei diversi metodi disponibili, inclusi ad esempio il [portale di Azure](https://portal.azure.com/), PowerShell, l'interfaccia della riga di comando di Azure, oppure a livello di codice con la libreria del client di archiviazione per .NET o l'API REST dei servizi di archiviazione di Azure.

Esistono diversi motivi per rigenerare le chiavi dell'account di archiviazione.

* Si possono rigenerarle a intervalli regolari per motivi di sicurezza.
* È necessario rigenerare le chiavi dell'account di archiviazione se un utente riesce a violare un'applicazione e a recuperare la chiave hardcoded o salvata in un file di configurazione, ottenendo così l'accesso completo all'account di archiviazione.
* Un altro caso per la rigenerazione delle chiave si verifica se il team usa un'applicazione di Storage Explorer che mantiene la chiave dell'account di archiviazione e un membro del team lascia l'organizzazione. L'applicazione continuerà a funzionare, consentendo l'accesso all'account di archiviazione dopo che il membro del team se ne è andato. Questo è in effetti il motivo principale per cui sono state create le firme di accesso condiviso a livello di account. È possibile usare una firma di accesso condiviso a livello di account invece di archiviare le chiavi di accesso in un file di configurazione.

#### <a name="key-regeneration-plan"></a>Piano di rigenerazione delle chiavi

È consigliabile non rigenerare semplicemente la chiave in uso senza una pianificazione. Se lo si fa, è possibile che venga completamente interrotto l'accesso all'account di archiviazione, con il rischio di causare gravi interruzioni. È per questo motivo che sono disponibili due chiavi ed è consigliabile rigenerarne una alla volta.

Prima di rigenerare le chiavi, assicurarsi di avere un elenco di tutte le applicazioni che dipendono dall'account di archiviazione, nonché di tutti gli altri servizi usati in Azure. Ad esempio, se si usa Servizi multimediali di Azure che dipende dall'account di archiviazione, è necessario risincronizzare le chiavi di accesso con il proprio servizio multimediale dopo la rigenerazione delle chiavi. Anche per le eventuali applicazioni usate come strumento di esplorazione dovranno essere fornite nuove chiavi. Se sono disponibili VM i cui file VHD sono archiviati nell'account di archiviazione, queste non saranno interessate dalla rigenerazione delle chiavi dell'account di archiviazione.

È possibile rigenerare le chiavi nel portale di Azure. Una volta che le chiavi sono state rigenerate, per la sincronizzazione con i servizi di archiviazione possono essere necessari fino a 10 minuti.

Quando si è pronti, seguire la procedura generale che illustra in dettaglio come deve essere modificata la chiave. In questo caso, si presuppone che attualmente sia in uso la Chiave 1 e si voglia cambiare tutto per usare invece la Chiave 2.

1. Rigenerare la Chiave 2 per assicurarsi che sia protetta. Questa operazione può essere eseguita nel portale di Azure.
2. Modificare la chiave di archiviazione in tutte le applicazioni in cui è archiviata, in modo da usare il nuovo valore della Chiave 2. Testare e pubblicare l'applicazione.
3. Una volta che le applicazioni e i servizi sono tutti operativi, rigenerare la Chiave 1. Ciò garantisce che chiunque non abbia ricevuto espressamente la nuova chiave non potrà più accedere all'account di archiviazione.

Se attualmente si usa la Chiave 2, è possibile usare lo stesso processo, ma invertendo i nomi delle chiavi.

Si può eseguire la migrazione nell'arco di un paio di giorni, modificando ogni applicazione per l'uso della nuova chiave e pubblicandola. Una volta modificate tutte le applicazioni, si dovrà rigenerare la chiave precedente in modo che non funzioni più.

Un'altra opzione consiste nell'inserire la chiave dell'account di archiviazione in un [insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) come chiave privata e fare in modo che le applicazioni recuperino la chiave da quella posizione. Di conseguenza, quando si rigenera la chiave e si aggiorna l'insieme di credenziali delle chiave di Azure, non sarà necessario ridistribuire le applicazioni, perché acquisiranno automaticamente la nuova chiave dall'insieme di credenziali delle chiavi di Azure. È possibile fare in modo che l'applicazione legga la chiave ogni volta che è necessario oppure memorizzarla nella cache e in caso di errore quando viene usata, recuperarla di nuovo da Azure Key Vault.

L'uso dell'insieme di credenziali delle chiavi di Azure aggiunge anche un altro livello di sicurezza per le chiavi di archiviazione. Se si usa questo metodo, non si avrà mai una chiave di archiviazione hardcoded in un file di configurazione, eliminando la possibilità che qualcuno possa ottenere l'accesso alle chiavi senza un'autorizzazione specifica.

Un altro vantaggio dell'uso dell'insieme di credenziali delle chiavi di Azure consiste nella possibilità di controllare anche l'accesso alle chiavi tramite Azure Active Directory. Ciò significa che è possibile concedere l'accesso al numero limitato di applicazioni che devono recuperare le chiavi dall'insieme di credenziali delle chiavi di Azure, sapendo che altre applicazioni non potranno accedere alle chiavi se a queste non vengono concesse autorizzazioni in modo specifico.

> [!NOTE]
> Microsoft consiglia di usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la Chiave 1 in alcune posizioni e la Chiave 2 in altre, non si potranno ruotare le chiavi senza quale applicazione perda l'accesso.

#### <a name="resources"></a>Risorse

* [Gestire le impostazioni dell'account di archiviazione nel portale di Azure](storage-account-manage.md)
* [Informazioni di riferimento sulle API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Sicurezza del piano dati
La sicurezza del piano dati riguarda i metodi usati per proteggere gli oggetti dati archiviati in Archiviazione di Azure. Sono stati esaminati i metodi per crittografare i dati e per la sicurezza durante la trasmissione di dati, ora si vedrà come fare per controllare l'accesso agli oggetti stessi.

Sono disponibili tre opzioni per autorizzare l'accesso agli oggetti dati in Archiviazione di Azure, tra cui:

- Uso di Azure AD per autorizzare l'accesso a file system e code. Azure AD offre alcuni vantaggi rispetto ad altri approcci all'autorizzazione, tra cui l'eliminazione della necessità di archiviare i segreti nel codice. Per altre informazioni, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md). 
- Uso delle chiavi dell'account di archiviazione per autorizzare l'accesso tramite chiave condivisa. L'autorizzazione tramite chiave condivisa richiede l'archiviazione delle chiavi dell'account di archiviazione nell'applicazione, quindi Microsoft consiglia di usare Azure AD, quando possibile. Per le applicazioni di produzione oppure per autorizzare l'accesso alle tabelle e ai file di Azure, continuare a usare la chiave condivisa fino a quando l'integrazione di Azure AD è disponibile in anteprima.
- Uso di firme di accesso condiviso per concedere autorizzazioni controllate per oggetti dati specifici per un determinato periodo di tempo.

Oltre a limitare l'accesso tramite autorizzazione, è possibile usare anche [Firewall e reti virtuali](storage-network-security.md) per limitare l'accesso all'account di archiviazione in base alle regole di rete.  Questo approccio consente di negare l'accesso al traffico internet pubblico e di concedere l'accesso solo a determinati intervalli di indirizzi IP di reti virtuali di Azure o reti internet pubbliche.

### <a name="storage-account-keys"></a>Chiavi dell'account di archiviazione

Le chiavi dell'account di archiviazione sono stringhe a 512 bit create da Azure che, insieme al nome dell'account di archiviazione, possono essere usate per accedere agli oggetti dati archiviati nell'account di archiviazione.

Ad esempio, è possibile leggere i file. Molte di queste azioni possono essere eseguite tramite il portale di Azure o una delle numerose applicazioni di Storage Explorer. Per eseguire queste operazioni è anche possibile scrivere un codice per usare l'API REST.

Come anticipato nella sezione relativa alla [Sicurezza del piano di gestione](#management-plane-security), l'accesso alle chiavi di archiviazione per un account di archiviazione con il modello di Azure Resource Manager può essere controllato tramite il controllo degli accessi in base al ruolo.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Come delegare l'accesso agli oggetti nell'account usando firme di accesso condiviso e criteri di accesso archiviati

Una firma di accesso condiviso è una stringa contenente un token di sicurezza, che può essere collegato a un URI che consente di delegare l'accesso a oggetti di archiviazione e specificare limitazioni, ad esempio le autorizzazioni e l'intervallo di data/ora di accesso.

È possibile concedere l'accesso a file o directory.

È possibile offrire a un cliente un token di firma di accesso condiviso che potrà essere usato per caricare immagini in un file system nell'archivio BLOB e garantire a un'applicazione Web l'autorizzazione per leggere quelle immagini. In entrambi i casi, esiste una separazione dei compiti: a ogni applicazione può essere fornito solo l'accesso necessario per eseguire attività specifiche. Ciò è possibile con l'uso delle firme di accesso condiviso.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Perché usare le firme di accesso condiviso

Perché si dovrebbe voler usare una firma di accesso condiviso invece di fornire semplicemente la chiave dell'account di archiviazione, che è un'operazione molto più semplice? Fornire la chiave dell'account di archiviazione è come condividere le chiavi dell'ambito di archiviazione, infatti concede l'accesso completo. Un utente può usare le chiavi e caricare l'intero catalogo musicale nell'account di archiviazione. Può anche sostituire i file con versioni infette da virus o rubare i dati. Fornire l'accesso illimitato all'account di archiviazione è una scelta che non dovrebbe essere presa alla leggera.

Con le firme di accesso condiviso è possibile assegnare a un client solo le autorizzazioni necessarie per un periodo di tempo limitato. Ad esempio, se un utente deve caricare un file nel proprio account, è possibile concedere l'accesso in scrittura per un periodo di tempo sufficiente a caricare il file, considerando naturalmente le dimensioni del file. Se si cambia idea, è possibile revocare l'accesso.

È anche possibile specificare che le richieste eseguite con una firma di accesso condiviso sono limitate a un determinato indirizzo IP o un intervallo di indirizzi IP all'esterno di Azure. Si può anche specificare che le richieste devono essere eseguite con un protocollo specifico (HTTPS o HTTP/HTTPS). Ciò significa che se si vuole consentire il traffico HTTPS, è possibile impostare il protocollo richiesto solo su HTTPS e il traffico HTTP verrà bloccato.

#### <a name="definition-of-a-shared-access-signature"></a>Definizione di firma di accesso condiviso

Una firma di accesso condiviso è un set di parametri di query aggiunto all'URL che punta alla risorsa.

Fornisce informazioni sull'accesso consentito e sul periodo di tempo per cui è consentito l'accesso. Ecco un esempio di URI che fornisce l'accesso in lettura a un BLOB per cinque minuti. I parametri di query della firma di accesso condiviso devono essere con codifica URL, ad esempio %3A per due punti (:) o %20 per lo spazio.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Modalità di autorizzazione della firma di accesso condiviso tramite il servizio Archiviazione di Azure

Quando il servizio di archiviazione riceve la richiesta, accetta i parametri di query di input e crea una firma usando lo stesso metodo del programma chiamante, quindi confronta le due firme. Se concordano, il servizio di archiviazione può controllare la versione del servizio di archiviazione per assicurarsi che sia valida, verificare che la data e l'ora correnti rientrino nell'intervallo specificato, assicurarsi che l'accesso richiesto corrisponda alla richiesta eseguita e così via.

Ad esempio, se l'URL precedente puntasse a un file invece che a un BLOB, la richiesta non riuscirebbe perché specifica che la firma di accesso condiviso è per un BLOB. Se il comando REST chiamato doveva aggiornare un BLOB, non sarebbe riuscito perché la firma di accesso condiviso specifica che è consentito solo l'accesso in lettura.

#### <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso

* Una firma di accesso condiviso a livello di servizio può essere usata per accedere a risorse specifiche in un account di archiviazione. Alcuni esempi sono il recupero di un elenco di file in un file system o il download di un file.
* Una firma di accesso condiviso a livello di account può essere usata per accedere a tutto ciò per cui può essere usata una firma di accesso condiviso a livello di servizio. Può anche offrire opzioni a risorse non consentite con una firma di accesso condiviso a livello di servizio, ad esempio la possibilità di creare file system.

#### <a name="creating-a-sas-uri"></a>Creazione di un URI di firma di accesso condiviso

1. È possibile creare un URI su richiesta, definendo ogni volta tutti i parametri di query.

   Questo approccio è flessibile, ma se è disponibile un set logico di parametri simili ogni volta, è preferibile l'uso di criteri di accesso archiviati.
2. È possibile creare criteri di accesso archiviati per un intero file system, una condivisione file, una tabella o una coda. che potranno quindi essere usati come base per gli URI di firma di accesso condiviso creati. Le autorizzazioni basate su criteri di accesso archiviati possono essere revocate facilmente. In ogni file system, coda, tabella o condivisione di file possono essere definiti fino a cinque criteri.

   Se ad esempio si prevede che molte persone leggano i BLOB in un file system specifico, è possibile creare criteri di accesso archiviati con l'indicazione "concedere l'accesso in lettura" e qualsiasi altra impostazione che sarà uguale ogni volta. È quindi possibile creare un URI di firma di accesso condiviso usando le impostazioni dei criteri di accesso archiviati e specificando la data/ora di scadenza. Il vantaggio di questo approccio è che non è necessario specificare ogni volta tutti i parametri di query.

#### <a name="revocation"></a>Revoca

Si supponga che la firma di accesso condiviso sia stata compromessa o si voglia modificarla a causa dei requisiti di conformità alle normative o di sicurezza aziendale. Come si revoca l'accesso a una risorsa con quella firma di accesso condiviso? Dipende da come è stato creato l'URI di firma di accesso condiviso.

Se si usano URI ad hoc, sono disponibili tre opzioni. È possibile rilasciare token di firma di accesso condiviso con criteri di scadenza breve e attendere che la firma scada. È possibile rinominare o eliminare la risorsa, presupponendo che l'ambito del token sia imitato a un singolo oggetto. È possibile modificare le chiavi dell'account di archiviazione. Quest'ultima opzione può avere un impatto significativo, a seconda di quanti servizi usano l'account di archiviazione, ed è probabilmente una scelta che presuppone un'attività di pianificazione.

Se si usa una firma di accesso condiviso derivata da criteri di accesso archiviati, è possibile rimuovere l'accesso revocando i criteri. Si può semplicemente modificarla in modo che risulti già scaduta o rimuoverla completamente. Questa operazione ha effetto immediato e invalida qualsiasi firma di accesso condiviso creata con tali criteri di accesso archiviati. L'aggiornamento o la rimozione dei criteri di accesso archiviati può avere un impatto sugli utenti che accedono al file system, alla condivisione file, alla tabella o alla coda specifica con la firma di accesso condiviso, ma se i client sono configurati per richiedere una nuova firma di accesso condiviso quando quella precedente non è più valida, questa operazione funzionerà correttamente.

Poiché l'uso di una firma di accesso condiviso derivata da criteri di accesso archiviati offre la possibilità di revocare immediatamente la firma di accesso condiviso, è consigliabile usare sempre i criteri di accesso archiviati, quando possibile.

#### <a name="resources"></a>Risorse

Per informazioni più dettagliate sull'uso di firme di accesso condiviso e criteri di accesso archiviati, con esempi, vedere gli articoli seguenti:

* Ecco gli articoli di riferimento.

  * [Firma di accesso condiviso del servizio.](https://msdn.microsoft.com/library/dn140256.aspx)

    Questo articolo fornisce esempi dell'uso di una firma di accesso condiviso a livello di servizio con BLOB, messaggi della coda, intervalli di tabelle e file.
  * [Creazione di una firma di accesso condiviso del servizio](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Creazione di una firma di accesso condiviso dell'account](https://msdn.microsoft.com/library/mt584140.aspx)
* Si tratta di esercitazioni per l'uso della libreria client .NET per creare firme di accesso condiviso e criteri di accesso archiviati.

  * [Uso delle firme di accesso condiviso](../storage-dotnet-shared-access-signature-part-1.md)
  * [Firme di accesso condiviso, parte 2: Creare e usare una firma di accesso condiviso con il servizio BLOB](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Questo articolo contiene una spiegazione del modello di firma di accesso condiviso, esempi di firme di accesso condiviso e suggerimenti per la procedura consigliata da usare per le firme di accesso condiviso. È descritta anche la revoca dell'autorizzazione concessa.

* Authentication

  * [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Esercitazione introduttiva sulle firme di accesso condiviso

  * [Esercitazione introduttiva sulle firme di accesso condiviso](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Crittografia in transito

### <a name="transport-level-encryption--using-https"></a>Crittografia a livello di trasporto: uso di HTTPS

Un altro passaggio da adottare per garantire la sicurezza dei dati di archiviazione di Azure consiste nel crittografare i dati tra il client e l'archiviazione di Azure. Il primo suggerimento consiste nell'usare sempre il protocollo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) che garantisce una comunicazione protetta sulla rete Internet pubblica.

Per avere un canale di comunicazione sicuro, è consigliabile usare sempre HTTPS quando si chiamano le API REST o si accede a oggetti nella risorsa di archiviazione. Le **firme di accesso condiviso**, che possono essere usate per delegare l'accesso a oggetti di archiviazione di Azure, includono anche un'opzione per specificare che quando si usano firme di accesso condiviso si può usare solo il protocollo HTTPS, assicurando che chiunque invii collegamenti con token di firma di accesso condiviso userà il protocollo corretto.

È possibile imporre l'uso di HTTPS quando si chiamano le API REST per accedere a oggetti negli account di archiviazione abilitando l'opzione [Trasferimento sicuro obbligatorio](../storage-require-secure-transfer.md) per l'account di archiviazione. Una volta abilitata l'opzione, le connessioni che usano il protocollo HTTP verranno rifiutate.

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi

### <a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione di Azure (SSE)

La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione e non può essere disabilitata. La crittografia del servizio di archiviazione crittografa automaticamente i dati quando vengono scritti in Archiviazione di Azure. Quando i dati vengono letti da Archiviazione di Azure, vengono decrittografati dal servizio di archiviazione prima di essere restituiti. La crittografia del servizio di archiviazione consente di proteggere i dati senza dover modificare o aggiungere codice alle applicazioni.

È possibile usare chiavi gestite da Microsoft o chiavi personalizzate. Microsoft genera chiavi gestite e ne gestisce l'archiviazione protetta, nonché la rotazione regolare secondo quanto definito dai criteri interni di Microsoft. Per altre informazioni sull'uso delle chiavi personalizzate, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md).
