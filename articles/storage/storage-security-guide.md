---
title: Guida alla sicurezza di Archiviazione di Azure | Microsoft Docs
description: Descrive in dettaglio i vari metodi di protezione di Archiviazione di Azure, incluse ad esempio Crittografia del servizio di archiviazione, crittografia lato client, SMB 3.0 e Crittografia dischi di Azure.
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 6f931d94-ef5a-44c6-b1d9-8a3c9c327fb2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 9e75c5af6cb6d2f2a25f18269ec6822aa86459fc
ms.openlocfilehash: 95ea1a9bc8fe80c39ca9f0683855cc3a4e7a77c4
ms.lasthandoff: 02/07/2017


---
# <a name="azure-storage-security-guide"></a>Guida alla sicurezza di Archiviazione di Azure
## <a name="overview"></a>Panoramica
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Lo stesso account di archiviazione può essere protetto tramite il controllo degli accessi in base al ruolo e Azure Active Directory. È possibile proteggere i dati in transito tra un'applicazione e Azure usando la [crittografia lato client](storage-client-side-encryption.md), HTTPS o SMB 3.0. I dati possono essere impostati per la crittografia automatica quando vengono scritti in Archiviazione di Azure con [Crittografia del servizio di archiviazione di Azure (SSE)](storage-service-encryption.md). I dischi dati e del sistema operativo usati dalle macchine virtuali possono essere impostati per essere crittografati con [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md). È possibile concedere l'accesso delegato agli oggetti dati nell'archiviazione di Azure usando [firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

Questo articolo include una panoramica di ognuna di queste funzionalità di sicurezza che possono essere usate con Archiviazione di Azure. Sono disponibili i collegamenti ad articoli contenenti informazioni dettagliate per ogni funzionalità, per poter approfondire facilmente i concetti per ogni argomento.

Ecco gli argomenti trattati in questo articolo:

* [Sicurezza del piano di gestione](#management-plane-security) : proteggere l'account di archiviazione
  
  Il piano di gestione è costituito dalle risorse usate per gestire l'account di archiviazione. Questa sezione illustra il modello di distribuzione Azure Resource Manager e come usare il controllo degli accessi in base al ruolo per controllare l'accesso agli account di archiviazione. Descrive anche la gestione delle chiavi dell'account di archiviazione e come rigenerarle.
* [Sicurezza del piano dati](#data-plane-security) : proteggere l'accesso ai dati
  
  Questa sezione illustra come concedere l'accesso agli oggetti dati effettivi nell'account di archiviazione, ad esempio BLOB, file, code e tabelle, usando firme di accesso condiviso e criteri di accesso archiviati. Vengono trattate anche le firme di accesso condiviso sia a livello di servizio che di account. Si vedrà anche come limitare l'accesso a un indirizzo IP specifico, o un intervallo di indirizzi IP, come limitare il protocollo usato per HTTPS e come revocare una firma di accesso condiviso senza attendere che scada.
* [Crittografia in transito](#encryption-in-transit)
  
  Questa sezione descrive come proteggere i dati durante il trasferimento da e verso Archiviazione di Azure. Si esamineranno l'uso consigliato di HTTPS e la crittografia usata da SMB 3.0 per Condivisioni file di Azure. Si osserverà anche la crittografia lato client, che consente di crittografare i dati prima che siano trasferiti nel servizio di archiviazione in un'applicazione client e di decrittografarli dopo il trasferimento dal servizio di archiviazione.
* [Crittografia di dati inattivi](#encryption-at-rest)
  
  Si discuterà di Crittografia del servizio di archiviazione (SSE) e come è possibile abilitarla per un account di archiviazione, determinando la crittografia automatica dei BLOB in blocchi, BLOB di pagine e BLOB aggiunti al momento della scrittura nell'Archiviazione di Azure. Verrà illustrato anche come è possibile usare Crittografia dischi di Azure e si esamineranno differenze di base e casi relativi a Crittografia dischi rispetto a SSE e alla crittografia lato client. Si esaminerà brevemente la conformità FIPS per i computer del Governo degli Stati Uniti.
* Uso [di Analisi archiviazione](#storage-analytics) per controllare l'accesso dell'archiviazione di Azure
  
  Questa sezione illustra come trovare informazioni nei log di Analisi archiviazione per una richiesta. Si osserveranno dati dei log di Analisi archiviazione reali e si vedrà come distinguere se una richiesta viene eseguita con la chiave dell'account di archiviazione, con una firma di accesso condiviso oppure in modo anonimo e se è riuscita o meno.
* [Abilitazione dei client basati su browser tramite CORS](#Cross-Origin-Resource-Sharing-CORS)
  
  Questa sezione descrive come consentire la condivisione risorse tra le origini (CORS). Verrà descritto l'accesso tra domini e come gestirlo con le funzionalità CORS integrate in Archiviazione di Azure.

## <a name="management-plane-security"></a>Sicurezza del piano di gestione
Il piano di gestione è costituito da operazioni che interessano lo stesso account di archiviazione. Ad esempio, è possibile creare o eliminare un account di archiviazione, ottenere un elenco di account di archiviazione in una sottoscrizione, recuperare le chiavi dell'account di archiviazione o rigenerarle.

Quando si crea un nuovo account di archiviazione, si seleziona un modello di distribuzione classica o di Azure Resource Manager. Il modello di distribuzione classica per la creazione di risorse in Azure consente solo l'accesso di tipo "tutto o niente" alla sottoscrizione e, di conseguenza, all'account di archiviazione.

Questa guida è incentrata sul modello di Resource Manager, ovvero il mezzo consigliato per la creazione di account di archiviazione. Con gli account di archiviazione di Resource Manager, invece di concedere l'accesso all'intera sottoscrizione, è possibile controllare l'accesso al piano di gestione in base a un livello più limitato usando il controllo degli accessi in base al ruolo.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo
Si vedrà ora cos'è il controllo degli accessi in base al ruolo e come è possibile usarlo. Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. A utenti, gruppi e applicazioni in questa directory può essere consentito l'accesso per gestire le risorse nella sottoscrizione di Azure che usa il modello di distribuzione di Resource Manager. Questo approccio è detto controllo degli accessi in base al ruolo. Per gestire l'accesso è possibile usare il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md), [PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST del provider di risorse di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Con il modello di Resource Manager si inserisce l'account di archiviazione in un gruppo di risorse e si controlla l'accesso al piano di quell'account di archiviazione specifico tramite Azure Active Directory. Ad esempio, è possibile concedere a utenti specifici la possibilità di accedere alle chiavi dell'account di archiviazione, mentre altri utenti possono visualizzare le informazioni sull'account di archiviazione, ma non accedere alle relative chiavi.

#### <a name="granting-access"></a>Concessione dell'accesso
L’accesso viene concesso assegnando i ruoli RBAC appropriati a utenti, gruppi e applicazioni nell'ambito corretto. Per concedere l'accesso all'intera sottoscrizione, assegnare un ruolo a livello di sottoscrizione. È possibile concedere l'accesso a tutte le risorse in un gruppo di risorse concedendo le autorizzazioni al gruppo stesso. È anche possibile assegnare ruoli specifici a risorse specifiche, come gli account di archiviazione.

Ecco i punti principali che occorre conoscere sull'uso del controllo degli accessi in base al ruolo per accedere alle operazioni di gestione di un account di archiviazione di Azure:

* Quando si assegna l'accesso, si assegna essenzialmente un ruolo all'account a cui si vuole accedere. È possibile controllare l'accesso alle operazioni usate per gestire quell'account di archiviazione, ma non agli oggetti dati nell'account. Ad esempio, è possibile concedere l'autorizzazione per recuperare le proprietà dell'account di archiviazione, ad esempio la ridondanza, ma non a un contenitore o ai dati all'interno di un contenitore nell'archivio BLOB.
* A chiunque abbia l'autorizzazione di accesso agli oggetti dati nell'account di archiviazione è possibile concedere l'autorizzazione di lettura delle chiavi dell'account di archiviazione. Questo utente potrà quindi usare le chiavi per accedere a BLOB, code, tabelle e file.
* I ruoli possono essere assegnati a un account utente specifico, a un gruppo di utenti o a un'applicazione specifica.
* Per ogni ruolo è disponibile un elenco di azioni e non azioni. Ad esempio, per il ruolo Collaboratore Macchina virtuale è disponibile un'azione "listKeys" che consente la lettura delle chiavi dell'account di archiviazione. Per il ruolo Collaboratore sono disponibili "non azioni", come l'aggiornamento dell'accesso per gli utenti in Active Directory.
* I ruoli per l'archiviazione includono, tra le altre, le operazioni seguenti:
  
  * Proprietario: può gestire qualsiasi elemento, compreso l'accesso.
  * Collaboratore: può eseguire tutte le operazioni consentite al proprietario, tranne l'assegnazione dell'accesso. Un utente con questo ruolo può visualizzare e rigenerare le chiavi dell'account di archiviazione. Con le chiavi di account di archiviazione può accedere gli oggetti dati.
  * Lettore: può visualizzare le informazioni sull'account di archiviazione, tranne i segreti. Ad esempio, se si assegna a qualcuno un ruolo con autorizzazioni di lettura per l'account di archiviazione, potrà visualizzare le proprietà dell'account di archiviazione, ma non apportare modifiche alle proprietà o visualizzare le chiavi dell'account di archiviazione.
  * Collaboratore Account di archiviazione: può gestire l'account di archiviazione, leggere i gruppi di risorse e le risorse della sottoscrizione, nonché creare e gestire distribuzioni di gruppi di risorse della sottoscrizione. Potendo accedere alle chiavi dell'account di archiviazione, può accedere anche al piano dati.
  * Amministratore Accesso utenti: può gestire l'accesso degli utenti all'account di archiviazione. Ad esempio, può concedere l'accesso in lettura a un utente specifico.
  * Collaboratore Macchina virtuale: può gestire le macchine virtuali, ma non l'account di archiviazione a cui è connesso. Questo ruolo consente di elencare le chiavi dell'account di archiviazione e ciò significa che l'utente al quale si assegna questo ruolo può aggiornare il piano dati.
    
    Per creare una macchina virtuale, un utente deve poter creare il file VHD corrispondente in un account di archiviazione. A questo scopo, dovrà essere in grado di recuperare la chiave dell'account di archiviazione e passarla all'API per la creazione della VM. Deve quindi avere questa autorizzazione per poter elencare le chiavi dell'account di archiviazione.
* La capacità di definire ruoli personalizzati è una funzionalità che consente di comporre un set di azioni da un elenco di azioni disponibili che possono essere eseguite sulle risorse di Azure.
* L'utente deve essere configurato in Azure Active Directory prima di potergli assegnare un ruolo.
* È possibile creare un creare un report di chi ha concesso o revocato un tipo di accesso e a chi e in quale ambito usando PowerShell o l'interfaccia della riga di comando di Azure.

#### <a name="resources"></a>Risorse
* [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md)
  
  Questo articolo descrive il controllo degli accessi in base al ruolo di Azure Active Directory e il relativo funzionamento.
* [RBAC: Ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md)
  
  Questo articolo illustra tutti i ruoli predefiniti disponibili nel controllo degli accessi in base al ruolo.
* [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md)
  
  In questo articolo sono illustrati i modelli di distribuzione classica e Resource Manager e sono descritti i vantaggi dell'uso di Resource Manager e dei gruppi di risorse. Viene spiegato il funzionamento dei provider di calcolo, rete e archiviazione di Azure nel modello di Resource Manager.
* [Gestione del controllo degli accessi in base al ruolo con l'API REST](../active-directory/role-based-access-control-manage-access-rest.md)
  
  Questo articolo illustra come usare l'API REST per gestire il controllo degli accessi in base al ruolo.
* [Informazioni di riferimento sulle API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)
  
  Questo è il riferimento per le API che è possibile usare per gestire l'account di archiviazione a livello di codice.
* [Guida per gli sviluppatori sull'autenticazione con l'API di Azure Resource Manager](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)
  
  Questo articolo illustra come eseguire l'autenticazione con le API di Resource Manager.
* [Controllo degli accessi in base al ruolo per Microsoft Azure da Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Questo è un collegamento a un video su Channel 9 della conferenza Microsoft Ignite 2015. In questa sessione vengono illustrate le funzionalità di creazione report e gestione degli accessi di Azure, oltre alle procedure consigliate per la protezione dell'accesso alle sottoscrizioni di Azure con Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Rigenerazione delle chiavi dell'account di archiviazione
Le chiavi dell'account di archiviazione sono stringhe a 512 bit create da Azure che, insieme al nome dell'account di archiviazione, possono essere usate per accedere agli oggetti dati archiviati nell'account di archiviazione, ad esempio BLOB, entità all'interno di una tabella, messaggi nella coda e file in una condivisione file di Azure. Il controllo dell'accesso alle chiavi dell'account di archiviazione consente di controllare l'accesso al piano dati di tale account di archiviazione.

Per ogni account di archiviazione sono disponibili due chiavi, dette "Chiave 1" e "Chiave 2" nel [portale di Azure](http://portal.azure.com/) e nei cmdlet di PowerShell. Queste possono essere rigenerate manualmente usando uno dei diversi metodi disponibili, inclusi ad esempio il [portale di Azure](https://portal.azure.com/), PowerShell, l'interfaccia della riga di comando di Azure, oppure a livello di codice con la libreria del client di archiviazione per .NET o l'API REST dei servizi di archiviazione di Azure.

Esistono diversi motivi per rigenerare le chiavi dell'account di archiviazione.

* Si possono rigenerarle a intervalli regolari per motivi di sicurezza.
* È necessario rigenerare le chiavi dell'account di archiviazione se un utente riesce a violare un'applicazione e a recuperare la chiave hardcoded o salvata in un file di configurazione, ottenendo così l'accesso completo all'account di archiviazione.
* Un altro caso per la rigenerazione delle chiave si verifica se il team usa un'applicazione di Storage Explorer che mantiene la chiave dell'account di archiviazione e un membro del team lascia l'organizzazione. L'applicazione continuerà a funzionare, consentendo l'accesso all'account di archiviazione dopo che se ne è andato. Questo è in effetti il motivo principale per cui sono state create le firme di accesso condiviso a livello di account. È possibile usare una firma di accesso condiviso a livello di account invece di archiviare le chiavi di accesso in un file di configurazione.

#### <a name="key-regeneration-plan"></a>Piano di rigenerazione delle chiavi
È consigliabile non rigenerare semplicemente la chiave in uso senza una pianificazione. Se lo si fa, è possibile che venga completamente interrotto l'accesso all'account di archiviazione, con il rischio di causare gravi interruzioni. È per questo motivo che sono disponibili due chiavi ed è consigliabile rigenerarne una alla volta.

Prima di rigenerare le chiavi, assicurarsi di avere un elenco di tutte le applicazioni che dipendono dall'account di archiviazione, nonché di tutti gli altri servizi usati in Azure. Ad esempio, se si usa Servizi multimediali di Azure che dipende dall'account di archiviazione, è necessario risincronizzare le chiavi di accesso con il proprio servizio multimediale dopo la rigenerazione delle chiavi. Anche per le eventuali applicazioni usate come strumento di esplorazione dovranno essere fornite nuove chiavi. Si noti che se sono disponibili VM i cui file VHD sono archiviati nell'account di archiviazione, queste non saranno interessate dalla rigenerazione delle chiavi dell'account di archiviazione.

È possibile rigenerare le chiavi nel portale di Azure. Una volta che le chiavi sono state rigenerate, per la sincronizzazione con i servizi di archiviazione potrebbero essere necessari fino a 10 minuti.

Quando si è pronti, ecco la procedura generale che illustra in dettaglio come deve essere modificata la chiave. In questo caso, si presuppone che attualmente sia in uso la Chiave 1 e si voglia cambiare tutto per usare invece la Chiave 2.

1. Rigenerare la Chiave 2 per assicurarsi che sia protetta. Questa operazione può essere eseguita nel portale di Azure.
2. Modificare la chiave di archiviazione in tutte le applicazioni in cui è archiviata, in modo da usare il nuovo valore della Chiave 2. Testare e pubblicare l'applicazione.
3. Una volta che le applicazioni e i servizi sono tutti operativi, rigenerare la Chiave 1. Ciò garantisce che chiunque non abbia ricevuto espressamente la nuova chiave non potrà più accedere all'account di archiviazione.

Se attualmente si usa la Chiave 2, è possibile usare lo stesso processo, ma invertendo i nomi delle chiavi.

Si può eseguire la migrazione nell'arco di un paio di giorni, modificando ogni applicazione per l'uso della nuova chiave e pubblicandola. Una volta modificate tutte le applicazioni, si dovrà rigenerare la chiave precedente in modo che non funzioni più.

Un'altra opzione consiste nell'inserire la chiave dell'account di archiviazione in un [insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) come chiave privata e fare in modo che le applicazioni recuperino la chiave da quella posizione. Di conseguenza, quando si rigenera la chiave e si aggiorna l'insieme di credenziali delle chiave di Azure, non sarà necessario ridistribuire le applicazioni, perché acquisiranno automaticamente la nuova chiave dall'insieme di credenziali delle chiavi di Azure. Si noti che è possibile fare in modo che l'applicazione legga la chiave ogni volta che è necessario oppure memorizzarla nella cache e in caso di errore quando viene usata, recuperarla di nuovo dall'insieme di credenziali delle chiavi di Azure.

L'uso dell'insieme di credenziali delle chiavi di Azure aggiunge anche un altro livello di sicurezza per le chiavi di archiviazione. Se si usa questo metodo, non si avrà mai una chiave di archiviazione hardcoded in un file di configurazione, eliminando la possibilità che qualcuno possa ottenere l'accesso alle chiavi senza un'autorizzazione specifica.

Un altro vantaggio dell'uso dell'insieme di credenziali delle chiavi di Azure consiste nella possibilità di controllare anche l'accesso alle chiavi tramite Azure Active Directory. Ciò significa che è possibile concedere l'accesso al numero limitato di applicazioni che devono recuperare le chiavi dall'insieme di credenziali delle chiavi di Azure, sapendo che altre applicazioni non potranno accedere alle chiavi se a queste non vengono concesse autorizzazioni in modo specifico.

Nota: è consigliabile usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la Chiave 1 in alcune posizioni e la Chiave 2 in altre, non si potranno ruotare le chiavi senza quale applicazione perda l'accesso.

#### <a name="resources"></a>Risorse
* [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md#regenerate-storage-access-keys)
  
  Questo articolo fornisce una panoramica degli account di archiviazione e illustra le operazioni di visualizzazione, copia e rigenerazione delle chiavi di accesso alle risorse di archiviazione.
* [Informazioni di riferimento sulle API REST del provider di risorse di archiviazione di Azure](https://msdn.microsoft.com/library/mt163683.aspx)
  
  Questo articolo contiene collegamenti ad articoli specifici sul recupero delle chiavi dell'account di archiviazione e sulla rigenerazione delle chiavi dell'account di archiviazione per un account di Azure usando l'API REST. Nota: quanto detto vale per gli account di archiviazione di Resource Manager.
* [Operazioni sugli account di archiviazione](https://msdn.microsoft.com/library/ee460790.aspx)
  
  Questo articolo nelle informazioni di riferimento sulle API REST di gestione dei servizi di archiviazione contiene collegamenti ad articoli specifici relativi alle operazioni di recupero e rigenerazione delle chiavi dell'account di archiviazione tramite l'API REST. Nota: riguarda gli account di archiviazione della versione classica.
* [Addio per gestione delle chiavi: come gestire l'accesso ai dati di archiviazione di Azure con Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)
  
  Questo articolo illustra come usare Active Directory per controllare l'accesso alle chiavi di archiviazione di Azure nell'insieme di credenziali delle chiavi di Azure. Illustra anche come usare un processo di Automazione di Azure per rigenerare le chiavi su base oraria.

## <a name="data-plane-security"></a>Sicurezza del piano dati
La sicurezza del piano dati riguarda i metodi usati per proteggere gli oggetti dati archiviati in Archiviazione di Azure: BLOB, code, tabelle e file. Sono stati esaminati i metodi per crittografare i dati e per la sicurezza durante la trasmissione di dati, ora si vedrà come fare per consentire l'accesso agli oggetti stessi.

Esistono essenzialmente due metodi per controllare l'accesso agli oggetti dati. Il primo consiste nel controllare l'accesso alle chiavi dell'account di archiviazione, mentre il secondo usa firme di accesso condiviso per concedere l'accesso a oggetti dati specifici per un determinato periodo di tempo.

Un'eccezione importante riguarda la possibilità di consentire l'accesso pubblico ai BLOB impostando di conseguenza il livello di accesso per il contenitore che contiene i BLOB. Se si imposta l'accesso per un contenitore su BLOB o Contenitore, sarà consentito l'accesso pubblico in lettura ai BLOB in quel contenitore. Ciò significa che chiunque abbia un URL che punta a un BLOB in quel contenitore potrà aprirlo in un browser senza usare una firma di accesso condiviso o con le chiavi dell'account di archiviazione.

### <a name="storage-account-keys"></a>Chiavi dell'account di archiviazione
Le chiavi dell'account di archiviazione sono stringhe a 512 bit create da Azure che, insieme al nome dell'account di archiviazione, possono essere usate per accedere agli oggetti dati archiviati nell'account di archiviazione.

Ad esempio, è possibile leggere BLOB, scrivere nelle code, creare tabelle e modificare file. Molte di queste azioni possono essere eseguite tramite il portale di Azure o una delle numerose applicazioni di Storage Explorer. Per eseguire queste operazioni è anche possibile scrivere codice per usare l'API REST o una delle librerie client di archiviazione.

Come illustrato nella sezione [Sicurezza del piano di gestione](#management-plane-security), l'accesso alle chiavi di archiviazione per un account di archiviazione della versione classica può essere concesso fornendo l'accesso completo alla sottoscrizione di Azure. L'accesso alle chiavi di archiviazione per un account di archiviazione con il modello di Azure Resource Manager può essere controllato tramite il controllo degli accessi in base al ruolo.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Come delegare l'accesso agli oggetti nell'account usando firme di accesso condiviso e criteri di accesso archiviati
Una firma di accesso condiviso è una stringa contenente un token di sicurezza, che può essere collegato a un URI che consente di delegare l'accesso a oggetti di archiviazione e specificare limitazioni, ad esempio le autorizzazioni e l'intervallo di data/ora di accesso.

È possibile concedere l'accesso a BLOB, contenitori messaggi della coda, file e tabelle. Con le tabelle è effettivamente possibile concedere l'autorizzazione per accedere a un intervallo di entità nella tabella specificando gli intervalli di chiavi di partizione e di riga per cui si vuole concedere l'accesso all'utente. Ad esempio, se sono disponibili dati archiviati con una chiave di partizione di uno stato, è possibile concedere l'accesso ai dati solo per la California.

In un altro esempio, si può fornire un a un'applicazione Web un token di firma di accesso condiviso che consente di scrivere entità in una coda e fornire a un'applicazione ruolo di lavoro un token di firma di accesso condiviso per ottenere messaggi dalla coda ed elaborarli. In alternativa si può fornire a un cliente un token di firma di accesso condiviso che potrà essere usato per caricare immagini in un contenitore nell'archivio BLOB e fornire a un'applicazione Web l'autorizzazione per leggere quelle immagini. In entrambi i casi, esiste una separazione dei compiti: a ogni applicazione può essere fornito solo l'accesso necessario per eseguire attività specifiche. Ciò è possibile con l'uso delle firme di accesso condiviso.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Perché usare le firme di accesso condiviso
Perché si dovrebbe voler usare una firma di accesso condiviso invece di fornire semplicemente la chiave dell'account di archiviazione, che è un'operazione molto più semplice? Fornire la chiave dell'account di archiviazione è come condividere le chiavi dell'ambito di archiviazione, infatti concede l'accesso completo. Un utente può usare le chiavi e caricare l'intero catalogo musicale nell'account di archiviazione. Può anche sostituire i file con versioni infette da virus o rubare i dati. Fornire l'accesso illimitato all'account di archiviazione è una scelta che non dovrebbe essere presa alla leggera.

Con le firme di accesso condiviso è possibile assegnare a un client solo le autorizzazioni necessarie per un periodo di tempo limitato. Ad esempio, se un utente deve caricare un BLOB nel proprio account, è possibile concedere l'accesso in scrittura per un periodo di tempo sufficiente caricare il BLOB, considerando naturalmente le dimensioni del BLOB. Se si cambia idea, è possibile revocare l'accesso.

È anche possibile specificare che le richieste eseguite con una firma di accesso condiviso sono limitate a un determinato indirizzo IP o un intervallo di indirizzi IP all'esterno di Azure. Si può anche specificare che le richieste devono essere eseguite con un protocollo specifico (HTTPS o HTTP/HTTPS). Ciò significa che se si vuole consentire il traffico HTTPS, è possibile impostare il protocollo richiesto solo su HTTPS e il traffico HTTP verrà bloccato.

#### <a name="definition-of-a-shared-access-signature"></a>Definizione di firma di accesso condiviso
Una firma di accesso condiviso è un set di parametri di query aggiunto all'URL che punta alla risorsa.

Fornisce informazioni sull'accesso consentito e sul periodo di tempo per cui è consentito l'accesso. Ecco un esempio di URI che fornisce l'accesso in lettura a un BLOB per cinque minuti. Si noti che i parametri di query della firma di accesso condiviso devono essere con codifica URL, ad esempio %3A per due punti (:) o %20 per lo spazio.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Modalità di autenticazione della firma di accesso condiviso tramite il servizio di archiviazione di Azure
Quando il servizio di archiviazione riceve la richiesta, accetta i parametri di query di input e crea una firma usando lo stesso metodo del programma chiamante, quindi confronta le due firme. Se concordano, il servizio di archiviazione quindi può controllare la versione del servizio di archiviazione per assicurarsi che sia valida, verificare che la data e l'ora correnti rientrino nell'intervallo specificato, assicurarsi che l'accesso richiesto corrisponda alla richiesta eseguita e così via.

Ad esempio, se l'URL precedente puntasse a un file invece che a un BLOB, la richiesta non riuscirebbe perché specifica che la firma di accesso condiviso è per un BLOB. Se il comando REST chiamato doveva aggiornare un BLOB, non sarebbe riuscito perché la firma di accesso condiviso specifica che è consentito solo l'accesso in lettura.

#### <a name="types-of-shared-access-signatures"></a>Tipi di firme di accesso condiviso
* Una firma di accesso condiviso a livello di servizio può essere usata per accedere a risorse specifiche in un account di archiviazione. Alcuni esempi consistono nel recuperare un elenco di BLOB in un contenitore, scaricare un BLOB, aggiornare un'entità in una tabella, aggiungere messaggi a una coda o caricare un file in una condivisione file.
* Una firma di accesso condiviso a livello di account può essere usata per accedere a tutto ciò per cui può essere usata una firma di accesso condiviso a livello di servizio. Può anche fornire opzioni a risorse non consentite con una firma di accesso condiviso a livello di servizio, ad esempio la possibilità di creare contenitori, tabelle, code e condivisioni file. È anche possibile specificare l'accesso a più servizi contemporaneamente. Ad esempio, è possibile consentire a un utente di accedere a BLOB e file nell'account di archiviazione.

#### <a name="creating-an-sas-uri"></a>Creazione di un URI di firma di accesso condiviso
1. È possibile creare un URI ad hoc su richiesta, definendo ogni volta tutti i parametri di query.
   
   Questo approccio è molto flessibile, ma se è disponibile un set logico di parametri simili ogni volta, l'uso di criteri di accesso archiviati è preferibile.
2. È possibile creare criteri di accesso archiviati per un intero contenitore, una condivisione file, una tabella o una coda, che potranno quindi essere usati come base per gli URI di firma di accesso condiviso creati. Le autorizzazioni basate su criteri di accesso archiviati possono essere revocate facilmente. In ogni contenitore, coda, tabella o condivisione di file possono essere definiti fino a 5 criteri.
   
   Ad esempio, se si prevede che molte persone leggano i BLOB in un contenitore specifico, è possibile creare criteri di accesso archiviati con l'indicazione "concedere l'accesso in lettura" e qualsiasi altra impostazione che sarà uguale ogni volta. È quindi possibile creare un URI di firma di accesso condiviso usando le impostazioni dei criteri di accesso archiviati e specificando la data/ora di scadenza. Il vantaggio di questo approccio è che non è necessario specificare ogni volta tutti i parametri di query.

#### <a name="revocation"></a>Revoca
Si supponga che la firma di accesso condiviso sia stata compromessa o si voglia modificarla a causa dei requisiti di conformità alle normative o di sicurezza aziendale. Come si revoca l'accesso a una risorsa con quella firma di accesso condiviso? Dipende da come è stato creato l'URI di firma di accesso condiviso.

Se si usa un URI ad hoc, sono disponibili tre opzioni. È possibile rilasciare token di firma di accesso condiviso con criteri di scadenza breve e attendere semplicemente che la firma scada. È possibile rinominare o eliminare la risorsa, presupponendo che l'ambito del token sia imitato a un singolo oggetto. È possibile modificare le chiavi dell'account di archiviazione. Quest'ultima opzione può avere un impatto significativo, a seconda di quanti servizi usano l'account di archiviazione ed è probabilmente una scelta che presuppone un'attività di pianificazione.

Se si usa una firma di accesso condiviso derivata da criteri di accesso archiviati, è possibile rimuovere l'accesso revocando i criteri. Si può semplicemente modificarla in modo che risulti già scaduta o rimuoverla completamente. Questa operazione ha effetto immediato e invalida qualsiasi firma di accesso condiviso creata con tali criteri di accesso archiviati. L'aggiornamento o la rimozione dei criteri di accesso archiviati può avere un impatto sugli utenti che accedono al contenitore, alla condivisione file, alla tabella o alla coda specifica con la firma di accesso condiviso, ma se i client sono configurati per richiedere una nuova firma di accesso condiviso quando quella precedente non è più valida, questa operazione funzionerà correttamente.

Poiché l'uso di una firma di accesso condiviso derivata da criteri di accesso archiviati offre la possibilità di revocare immediatamente la firma di accesso condiviso, è consigliabile usare sempre i criteri di accesso archiviati, quando possibile.

#### <a name="resources"></a>Risorse
Per informazioni più dettagliate sull'uso di firme di accesso condiviso e criteri di accesso archiviati, con esempi, vedere gli articoli seguenti:

* Ecco gli articoli di riferimento.
  
  * [Firma di accesso condiviso del servizio.](https://msdn.microsoft.com/library/dn140256.aspx)
    
    Questo articolo fornisce esempi dell'uso di una firma di accesso condiviso a livello di servizio con BLOB, messaggi della coda, intervalli di tabelle e file.
  * [Creazione di una firma di accesso condiviso del servizio](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Creazione di una firma di accesso condiviso dell'account](https://msdn.microsoft.com/library/mt584140.aspx)
* Si tratta di esercitazioni per l'uso della libreria client .NET per creare firme di accesso condiviso e criteri di accesso archiviati.
  
  * [Uso delle firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md)
  * [Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con il servizio BLOB](storage-dotnet-shared-access-signature-part-2.md)
    
    Questo articolo contiene una spiegazione del modello di firma di accesso condiviso, esempi di firme di accesso condiviso e suggerimenti per la procedura consigliata da usare per le firme di accesso condiviso. È descritta anche la revoca dell'autorizzazione concessa.
* Limitazione dell'accesso con un indirizzo IP (ACL IP)
  
  * [Che cos'è un elenco di controllo di accesso (ACL) di endpoint?](../virtual-network/virtual-networks-acl.md)
  * [Creazione di una firma di accesso condiviso del servizio](https://msdn.microsoft.com/library/azure/dn140255.aspx)
    
    Questo è un articolo di riferimento per la firma di accesso condiviso a livello di servizio. Include un esempio di ACL di IP.
  * [Creazione di una firma di accesso condiviso dell'account](https://msdn.microsoft.com/library/azure/mt584140.aspx)
    
    Questo è un articolo di riferimento per la firma di accesso condiviso a livello di account. Include un esempio di ACL di IP.
* Autenticazione
  
  * [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Esercitazione introduttiva sulle firme di accesso condiviso
  
  * [Esercitazione introduttiva sulle firme di accesso condiviso](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Crittografia in transito
### <a name="transport-level-encryption--using-https"></a>Crittografia a livello di trasporto: uso di HTTPS
Un altro passaggio da adottare per garantire la sicurezza dei dati di archiviazione di Azure consiste nel crittografare i dati tra il client e l'archiviazione di Azure. Il primo suggerimento consiste nell'usare sempre il protocollo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) che garantisce una comunicazione protetta sulla rete Internet pubblica.

È consigliabile usare sempre HTTPS quando si chiamano le API REST o si accede a oggetti nella risorsa di archiviazione. Le **firme di accesso condiviso**, che possono essere usate per delegare l'accesso a oggetti di archiviazione di Azure, includono anche un'opzione per specificare che quando si usano firme di accesso condiviso si può usare solo il protocollo HTTPS, assicurando che chiunque invii collegamenti con token di firma di accesso condiviso userà il protocollo corretto.

#### <a name="resources"></a>Risorse
* [Abilitare HTTPS per un'app in Azure App Service](../app-service-web/web-sites-configure-ssl-certificate.md)
  
  Questo articolo illustra come abilitare HTTPS per un'app Web di Azure.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Uso della crittografia durante la trasmissione con Condivisioni file di Azure
Il servizio di archiviazione file di Azure supporta HTTPS quando si usa l'API REST, ma è più comunemente usato come una condivisione file SMB collegata a una VM. SMB 2.1 non supporta la crittografia, quindi le connessioni sono consentite solo all'interno della stessa area in Azure. Tuttavia, SMB 3.0 supporta la crittografia e può essere usato con Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, consentendo l'accesso tra aree e anche l'accesso sul desktop.

Si noti che mentre Condivisioni file Azure può essere usato con Unix, il client SMB Linux non supporta ancora la crittografia, quindi l'accesso è consentito solo all'interno di un'area di Azure. Il supporto della crittografia per Linux fa parte del programma degli sviluppatori Linux responsabili della funzionalità di SMB. Quando verrà aggiunta la crittografia, si avrà la stessa possibilità di accesso a una condivisione file di Azure in Linux, come avviene per Windows.

#### <a name="resources"></a>Risorse
* [Come usare Archiviazione file di Azure con Linux](storage-how-to-use-files-linux.md)
  
  Questo articolo illustra come montare una condivisione file di Azure in un sistema Linux e caricare o scaricare file.
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)
  
  Questo articolo fornisce una panoramica delle condivisioni file di Azure e come montarle e usarle con PowerShell e .NET.
* [Analisi di archiviazione file di Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
  
  Questo articolo annuncia la disponibilità a livello generale di Archiviazione file di Azure e fornisce dettagli tecnici sulla crittografia in SMB 3.0.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Uso della crittografia lato client per proteggere i dati inviati alla risorsa di archiviazione
Un'altra opzione che consente di assicurarsi che i dati siano protetti durante il trasferimento tra un'applicazione client e la risorsa di archiviazione è la crittografia lato client. I dati vengono crittografati prima di essere trasferiti nell'archiviazione di Azure. Quando si recuperano i dati dall'archiviazione di Azure, vengono decrittografati dopo la ricezione sul lato client. Anche se i dati vengono crittografati mentre sono in transito, è consigliabile usare anche HTTPS, perché incorpora controlli di integrità dei dati che contribuiscono a ridurre gli errori di rete che interessano l'integrità dei dati.

La crittografia lato client è anche un metodo per crittografare i dati inattivi, perché i dati vengono archiviati in formato crittografato. Verranno fornite informazioni più dettagliate nella sezione [Crittografi di dati inattivi](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi
Esistono tre funzionalità di Azure che consentono di crittografare dati inattivi. Crittografia dischi di Azure viene usata per crittografare i dischi dati e del sistema operativo nelle macchine virtuali IaaS. Le altre due, ovvero SSE e crittografia lato client, vengono entrambe usate per crittografare i dati nell'archiviazione di Azure. Si esaminerà ognuna di queste funzionalità, eseguendo quindi un confronto per vedere quando usare ognuna di esse.

Anche se è possibile usare la crittografia lato client per crittografare i dati in transito, che vengono anche archiviati in forma crittografata nella risorsa di archiviazione, è preferibile usare semplicemente HTTPS durante il trasferimento e configurare un modo per crittografare automaticamente i dati quando sono archiviati. Esistono due modi per eseguire questa operazione: SSE e Crittografia dischi di Azure. Una viene usata per crittografare direttamente i dati sui dischi dati e del sistema operativo usati dalle VM e l'altra viene usata per crittografare i dati scritti nell'archivio BLOB di Azure.

### <a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione di Azure (SSE)
SSE consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura nell'archiviazione di Azure. Quando si leggono i dati dall'archiviazione di Azure, verranno decrittografati dal servizio di archiviazione prima di essere restituiti. Ciò consente di proteggere i dati senza dover modificare il codice o aggiungere codice alle applicazioni.

Questa impostazione si applica all'intero account di archiviazione. È possibile abilitare e disabilitare questa funzionalità modificando il valore dell'impostazione. A questo scopo si può usare il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST del provider di risorse di archiviazione o la libreria del client di archiviazione per .NET. Per impostazione predefinita, la funzionalità SSE è disattivata.

Al momento, le chiavi usate per la crittografia sono gestite da Microsoft. Le chiavi vengono generate in origine e viene gestita l'archiviazione protetta delle chiavi, nonché la rotazione regolare secondo quanto definito dai criteri interni di Microsoft. In futuro, sarà possibile gestire le proprie chiavi di crittografia e verrà fornito un percorso di migrazione dalle chiavi gestite da Microsoft alle chiavi gestite dal cliente.

Questa funzionalità è disponibile per gli account di archiviazione Premium e Standard creati con il modello di distribuzione di Resource Manager. SSE viene applicato solo per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta. Altri tipi di dati, tra cui tabelle, code e file, non verranno crittografati.

I dati vengono crittografati solo quando SSE è abilitata e i dati vengono scritti nell'archiviazione BLOB. L'abilitazione o la disabilitazione di SSE non influisce sui dati esistenti. In altre parole, quando si abilita la crittografia, non tornare indietro e crittografare i dati che esistono già. né per decrittografare i dati già presenti quando si disabilita SSE.

Se si vuole usare questa funzionalità con un account di archiviazione classico, è possibile creare un nuovo account di archiviazione di Resource Manager e usare AzCopy per copiare i dati nel nuovo account. 

### <a name="client-side-encryption"></a>crittografia lato client
La crittografia lato client è stata citata nella discussione riguardante la crittografia dei dati in transito. Questa funzionalità consente di crittografare a livello di codice i dati in un'applicazione client prima dell'invio in rete per la scrittura nell'archiviazione di Azure e di decrittografare i dati a livello di codice dopo il recupero dall'archiviazione di Azure.

In questo modo è disponibile la crittografia in transito, ma anche la funzionalità di crittografia dei dati inattivi. Si noti che anche se i dati vengono crittografati in transito, è comunque consigliabile usare HTTPS per sfruttare i controlli di integrità dei dati incorporati, che contribuiscono a ridurre gli errori di rete che interessano l'integrità dei dati.

Un esempio d'uso di questa opzione è la disponibilità di un'applicazione Web che archivia e recupera i BLOB e si vuole proteggere l'applicazione e i dati nel miglior modo possibile. In questo caso, si userà la crittografia lato client. Il traffico tra il client e il servizio BLOB di Azure contiene la risorsa crittografata e nessuno può interpretare i dati in transito e ricostituirli nei BLOB privati.

La crittografia lato client è incorporata nelle librerie client di archiviazione per .NET e Java, che a loro volta usano le API dell'insieme di credenziali delle chiavi di Azure, rendendo l'implementazione piuttosto semplice. Il processo di crittografia e decrittografia dei dati usa la tecnica delle envelope e archivia i metadati usati per la crittografia in ogni oggetto di archiviazione. Ad esempio, per i BLOB li archivia nei metadati del BLOB, mentre per le code li aggiunge a ogni messaggio nella coda.

Per la crittografia stessa, è possibile generare e gestire chiavi di crittografia personalizzate. È anche possibile usare le chiavi generate dalla libreria client di archiviazione di Azure oppure è possibile impostare la generazione delle chiavi usando l'insieme di credenziali delle chiavi di Azure. Si possono archiviare le chiavi di crittografia nell'archiviazione chiavi locale oppure in un insieme di credenziali delle chiavi di Azure. L'insieme di credenziali delle chiavi di Azure consente di concedere l'accesso ai segreti nello stesso insieme di credenziali delle chiavi Azure a utenti specifici usando Azure Active Directory. Ciò significa che non tutti possono leggere l'insieme di credenziali delle chiavi di Azure e recuperare le chiavi usate per la crittografia lato client.

#### <a name="resources"></a>Risorse
* [Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali chiave di Azure](storage-encrypt-decrypt-blobs-key-vault.md)
  
  Questo articolo illustra come usare la crittografia lato client con l'insieme di credenziali delle chiavi di Azure, inclusa la creazione della chiave di crittografia della chiave e la relativa archiviazione nell'insieme di credenziali tramite PowerShell.
* [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](storage-client-side-encryption.md)
  
  Questo articolo fornisce una spiegazione della crittografia lato client, con esempi d'uso della libreria client di archiviazione per crittografare e decrittografare le risorse dai quattro servizi di archiviazione. Illustra anche l'insieme di credenziali delle chiavi di Azure.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Uso di Crittografia dischi di Azure per crittografare i dischi usati dalle macchine virtuali
Crittografia dischi di Azure è una nuova funzionalità. Questa funzionalità consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS. Per Windows, le unità vengono crittografate mediante la tecnologia di crittografia BitLocker standard del settore. Per Linux, i dischi vengono crittografati mediante la tecnologia DM-Crypt, integrata nell'insieme di credenziali delle chiavi per consentire il controllo e la gestione delle chiavi di crittografia del disco.

La soluzione supporta gli scenari seguenti per le macchine virtuali IaaS, se abilitati in Microsoft Azure:

* Integrazione dell'insieme di credenziali delle chiavi di Azure.
* Macchine virtuali di livello Standard: [serie A, D, DS, G, GS e così via per VM IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Abilitazione della crittografia nelle macchine virtuali IaaS Windows e Linux
* Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows
* Disabilitazione della crittografia nelle unità dati per le macchine virtuali IaaS Linux
* Abilitazione della crittografia in macchine virtuali IaaS in esecuzione nel sistema operativo client Windows
* Abilitazione della crittografia su volumi con percorsi di montaggio
* Abilitazione della crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite mdadm
* Abilitazione della crittografia nelle macchine virtuali Linux usando LVM per i dischi dati
* Abilitazione della crittografia nelle macchine virtuali Windows configurate usando spazi di archiviazione
* Sono supportate tutte le aree geografiche pubbliche di Azure

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti in questa versione:

* VM IaaS del piano Basic
* Disabilitazione della crittografia in un'unità del sistema operativo per le VM IaaS Linux
* Macchine virtuali IaaS create usando il metodo di creazione classico per le macchine virtuali
* Integrazione con il servizio di gestione delle chiavi locale.
* File di Azure (file system condiviso), file system di rete (NFS, Network File System), volumi dinamici e macchine virtuali Windows configurate con sistemi RAID basati su software


> [!NOTE]
> La crittografia del disco del sistema operativo Linux è attualmente supportata nelle distribuzioni Linux seguenti: RHEL 7.2, CentOS 7.2n e Ubuntu 16.04.
> 
> 

Questa funzionalità garantisce che tutti i dati presenti sui dischi delle macchine virtuali siano crittografati mentre sono inattivi in Archiviazione di Azure.

#### <a name="resources"></a>Risorse
* [Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)
  
### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Confronto tra Crittografia dischi di Azure, SSE e crittografia lato client
#### <a name="iaas-vms-and-their-vhd-files"></a>VM IaaS e i relativi file VHD
Per i dischi usati dalle VM IaaS è consigliabile usare Crittografia dischi di Azure. La funzionalità SSE può essere attivata per crittografare i file VHD usati per eseguire il backup di questi dischi nell'Archiviazione di Azure, ma permette di crittografare solo dati appena scritti. Ciò significa che se si crea una VM e quindi si abilita SSE nell'account di archiviazione che contiene il file VHD, verranno crittografate solo le modifiche, non il file VHD originale.

Se si crea una VM utilizzando un'immagine proveniente da Azure Marketplace, Azure esegue una [copia superficiale](https://en.wikipedia.org/wiki/Object_copying) dell'immagine nell'account di archiviazione in Archiviazione di Azure senza crittografarla, anche se è abilitata la funzionalità SSE. Dopo aver creato la VM e avviato l'aggiornamento dell'immagine, SSE inizierà a crittografare i dati. Per questo motivo, è consigliabile usare Crittografia dischi di Azure nelle VM create da immagini in Azure Marketplace, se si vuole che siano completamente crittografate.

Se si sposta una macchina virtuale pre-crittografata dall'ambiente locale in Azure, si potranno caricare le chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure e continuare a usare la crittografia usata in locale per quella VM. Crittografia dischi di Azure viene abilitata per gestire questo scenario.

Se sono disponibili file VHD non crittografati dall'ambiente locale, è possibile caricarli nella raccolta come immagine personalizzata ed effettuare il provisioning di una VM dalla raccolta. Se si esegue questa operazione usando i modelli di Resource Manager, è possibile richiedere l'attivazione di Crittografia dischi di Azure all'avvio della VM.

Quando si aggiunge un disco dati e lo si monta nella VM, è possibile attivare Crittografia dischi di Azure su quel disco dati. Verrà crittografato prima di tutto il disco dati in locale e quindi il livello di gestione del servizio eseguirà una scrittura lazy nell'archiviazione in modo che il relativo contenuto venga crittografato.

#### <a name="client-side-encryption"></a>crittografia lato client
La crittografia lato client è il metodo più sicuro per crittografare i dati, perché li crittografa prima della trasmissione e crittografa i dati inattivi. Richiede tuttavia l'aggiunta di codice alle applicazioni tramite l'archiviazione, un'operazione che si potrebbe non voler eseguire. In questi casi, è possibile usare HTTPS per i dati in transito e la funzionalità SSE per crittografare i dati inattivi.

Grazie alla crittografia lato client, è possibile crittografare entità tabella, messaggi nella coda e BLOB. SSE consente di crittografare solo i BLOB. Se è necessario crittografare i dati di tabelle e code, è necessario usare la crittografia lato client.

La crittografia lato client viene gestita completamente dall'applicazione. Questo è l'approccio più sicuro, ma è necessario apportare modifiche all'applicazione a livello di codice e implementare processi di gestione delle chiavi. Usare questo approccio quando si vuole avere la massima sicurezza per i dati in transito e garantire che i dati archiviati siano crittografati.

La crittografia lato client comporta un carico maggiore sul client ed è necessario considerare questo aspetto nei piani di scalabilità, soprattutto se viene crittografata e trasferita una grande quantità di dati.

#### <a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione di Azure (SSE)
La crittografia del servizio di archiviazione è gestita da Archiviazione di Azure. SSE non solo garantisce la sicurezza dei dati in transito, ma permette di crittografare i dati al momento della scrittura in Archiviazione di Azure. L'uso di questa funzionalità non incide in alcun modo sulle prestazioni.

SSE permette di crittografare solo BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. Se è necessario crittografare dati di tabelle o di code, è consigliabile usare la crittografia lato client.

Se è disponibile un archivio o una raccolta di file VHD usati come base per la creazione di nuove macchine virtuali, è possibile creare un nuovo account di archiviazione, abilitare SSE e quindi caricare i file VHD in quell'account. Questi file VHD saranno crittografati da Archiviazione di Azure.

Se Crittografia dischi di Azure è abilitata per i dischi in una VM e la funzionalità SSE è abilitata nell'account di archiviazione che contiene i file VHD, tutti i nuovi dati scritti saranno crittografati due volte.

## <a name="storage-analytics"></a>di Analisi archiviazione
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Uso di Analisi archiviazione per monitorare il tipo di autorizzazione
Per ogni account di archiviazione è possibile abilitare Analisi archiviazione di Azure per eseguire la registrazione e archiviare dati di metrica. Questo è uno strumento molto utile da usare quando si vogliono controllare le metriche delle prestazioni di un account di archiviazione o è necessario risolvere i problemi di un account di archiviazione perché si verificano problemi di prestazioni.

Un'altra parte di dati che è possibile visualizzare nel log di Analisi archiviazione è il metodo di autenticazione usato da un utente quando accede alla risorsa di archiviazione. Ad esempio, con l'archivio BLOB è possibile vedere se è stata usata una firma di accesso condiviso o le chiavi dell'account di archiviazione o se il BLOB usato era pubblico.

Queste informazioni possono essere molto utili se l'accesso alla risorsa di archiviazione e strettamente protetto. Ad esempio, nell'archivio BLOB è possibile impostare tutti i contenitori su privato e implementare l'uso di un servizio di firma di accesso condiviso per tutte le applicazioni. È quindi possibile controllare i log regolarmente per verificare se l'accesso ai BLOB viene eseguito con chiavi dell'account di archiviazione, cosa che può indicare una violazione della sicurezza, o se i BLOB sono pubblici ma non dovrebbero esserlo.

#### <a name="what-do-the-logs-look-like"></a>Aspetto dei log
Dopo avere abilitato le metriche dell'account di archiviazione e la registrazione tramite il portale di Azure, i dati di analisi inizieranno ad accumularsi rapidamente. La registrazione e le metriche per ogni servizio sono separate. La registrazione viene scritta solo quando si verificano attività nell'account di archiviazione, mentre le metriche vengono registrate ogni minuto, ogni ora oppure ogni giorno, a seconda della configurazione.

I log sono archiviati in BLOB in blocchi in un contenitore denominato $logs nell'account di archiviazione. Questo contenitore viene creato automaticamente quando si abilita Analisi archiviazione. Dopo la creazione di questo contenitore, non sarà possibile eliminarlo, anche se si può eliminare il relativo contenuto.

Nel contenitore $logs è presente una cartella per ogni servizio e sono disponibili le sottocartelle per anno/mese/giorno/ora. Nella cartella delle ore i log sono semplicemente numerati. Ecco come apparirà la struttura di directory:

![Visualizzazione dei file di log](./media/storage-security-guide/image1.png)

Viene registrata ogni richiesta all'archiviazione di Azure viene registrata. Ecco uno snapshot di un file di log, che mostra i primi campi.

![Snapshot di un file di log](./media/storage-security-guide/image2.png)

Si noterà che è possibile usare i registri per rilevare qualsiasi tipo di chiamate a un account di archiviazione.

#### <a name="what-are-all-of-those-fields-for"></a>Scopo di tutti questi campi
Le risorse indicate di seguito includono un articolo che fornisce l'elenco dei molti campi disponibili nei log e lo scopo per cui vengono usati. Ecco l'elenco dei campi in ordine:

![Snapshot dei campi in un file di log](./media/storage-security-guide/image3.png)

In questo caso, sono interessati le voci per GetBlob e come vengono autenticati, quindi occorre cercare le voci con tipo di operazione "Get-Blob" e controllare lo stato della richiesta (4<sup>a</sup> colonna) e il tipo di autorizzazione (8<sup>a</sup> colonna).

Ad esempio, nelle prime righe nell'elenco precedente, request-status è "Success" e authorization-type "authenticated". Ciò significa che la richiesta è stata convalidata con la chiave dell'account di archiviazione.

#### <a name="how-are-my-blobs-being-authenticated"></a>Modalità di autenticazione dei BLOB
Sono tre i casi interessanti in questo caso.

1. Il BLOB è pubblico e vi si accede tramite un URL senza una firma di accesso condiviso. In questo caso, request-status è "AnonymousSuccess" e authorization-type è "anonymous".
   
   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Il BLOB è privato ed è stato usato con una firma di accesso condiviso. In questo caso, request-status è "SASSuccess" e authorization-type è "sas".
   
   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Il BLOB è privato ed è stata usata la chiave di archiviazione per accedervi. In questo caso, request-status è "**Success**" e authorization-type è "**authenticated**".
   
   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

È possibile usare Microsoft Message Analyzer per visualizzare e analizzare i log. Include funzionalità di ricerca e filtro. Ad esempio, è possibile cercare le istanze di GetBlob per verificare se l'utilizzo è quello previsto, ovvero per assicurarsi che un utente non acceda all'account di archiviazione in modo non appropriato.

#### <a name="resources"></a>Risorse
* [Analisi archiviazione](storage-analytics.md)
  
  Questo articolo fornisce una panoramica di Analisi archiviazione e come abilitarla.
* [Formato log Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343259.aspx)
  
  Questo articolo illustra il formato dei log di Analisi archiviazione e descrive in dettaglio i campi disponibili, ad esempio authentication-type che indica il tipo di autenticazione usato per la richiesta.
* [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
  
  Questo articolo illustra come configurare il monitoraggio delle metriche e la registrazione per un account di archiviazione.
* [Risoluzione dei problemi end-to-end mediante le metriche e la registrazione di Archiviazione di Azure, AzCopy e Message Analyzer](storage-e2e-troubleshooting.md)
  
  Questo articolo descrive la risoluzione dei problemi mediante Analisi archiviazione e illustra come usare Microsoft Message Analyzer.
* [Guida operativa di Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
  
  Questo è l'articolo di riferimento per Microsoft Message Analyzer e include collegamenti a un'esercitazione, procedure di avvio rapido e un riepilogo delle funzionalità.

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione risorse tra le origini (CORS)
### <a name="cross-domain-access-of-resources"></a>Accesso tra domini alle risorse
Quando un Web browser in esecuzione in un dominio invia una richiesta HTTP per una risorsa da un dominio diverso, viene definita richiesta HTTP tra le origini. Ad esempio, una pagina HTML servita da contoso.com esegue una richiesta per un'immagine JPEG ospitata in fabrikam.blob.core.windows.net. Per motivi di sicurezza, i browser limitano le richieste HTTP tra le origini avviate da script, ad esempio JavaScript. Ciò significa che quando il codice JavaScript in una pagina Web in contoso.com richiede tale immagine JPEG in fabrikam.blob.core.windows.net, il browser non consentirà la richiesta.

Cosa ha a che fare questo comportamento con Archiviazione di Azure? Anche se si archiviano asset statici, ad esempio file di dati JSON o XML nell'archiviio BLOB con un account di archiviazione denominato Fabrikam, il dominio per gli asset sarà fabrikam.blob.core.windows.net e l'applicazione Web contoso.com non potrà accedervi tramite JavaScript, perché i domini sono diversi. Questo vale anche se si prova a chiamare uno dei servizi di archiviazione di Azure, ad esempio l'archivio tabelle, che restituisce dati JSON da elaborare con il client JavaScript.

#### <a name="possible-solutions"></a>Possibili soluzioni
Un modo per risolvere questo problema consiste nell'assegnare un dominio personalizzato come "storage.contoso.com" a fabrikam.blob.core.windows.net. Il problema è dovuto al fatto che è possibile assegnare solo quel dominio personalizzato a un solo account di archiviazione. Cosa accade se gli asset vengono archiviati in più account di archiviazione?

Un altro modo per risolvere il problema consiste nell'impostare l'applicazione Web come proxy per le chiamate di archiviazione. Ciò significa che se si carica un file nell'archivio BLOB, l'applicazione Web lo scriverà in locale e quindi lo copierà nell'archivio BLOB oppure lo leggerà completamente in memoria e quindi lo scriverà nell'archivio BLOB. In alternativa, è possibile scrivere un'applicazione Web dedicato, ad esempio un'API Web, che carica i file in locale e li scrive nell'archivio BLOB. In entrambi i casi, è necessario considerare questa funzione nel determinare le esigenze di scalabilità.

#### <a name="how-can-cors-help"></a>Utilità di CORS
Archiviazione di Azure consente di abilitare CORS (Condivisione risorse tra le origini). Per ogni account di archiviazione è possibile specificare i domini che possono accedere alle risorse in tale account di archiviazione. Ad esempio, nel caso descritto in precedenza, è possibile abilitare CORS nell'account di archiviazione fabrikam.blob.core.windows.net e configurarlo per consentire l'accesso a contoso.com. L'applicazione Web contoso.com può quindi accedere direttamente alle risorse in fabrikam.blob.core.windows.net.

Una cosa da notare è che CORS consente l'accesso, ma non fornisce l'autenticazione necessaria per l'accesso non pubblico delle risorse di archiviazione. Ciò significa che è possibile accedere ai BLOB solo se sono pubblici oppure si include una firma di accesso condiviso che fornisce l'autorizzazione appropriata. Tabelle, code e i file non hanno accesso pubblico e richiedono una firma di accesso condiviso.

Per impostazione predefinita, CORS è disabilitato in tutti i servizi. È possibile abilitare CORS con l'API REST o la libreria client di archiviazione per chiamare uno dei metodi che impostano i criteri del servizio. In questo caso, includere una regola CORS, in formato XML. Ecco un esempio di una regola CORS impostata con l'operazione di impostazione delle proprietà del servizio per il servizio BLOB per un account di archiviazione. È possibile eseguire questa operazione usando la libreria client di archiviazione o le API REST per Archiviazione di Azure.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Ecco il significato di ogni riga:

* **AllowedOrigins** : indica i domini non corrispondenti che possono richiedere e ricevere dati dal servizio di archiviazione. Significa che contoso.com e fabrikam.com possono richiedere dati dall'archivio BLOB per un account di archiviazione specifico. È anche possibile impostare questo parametro su un carattere jolly (\*) per consentire a tutti i domini di accedere alle richieste.
* **AllowedMethods** : si tratta dell'elenco di metodi (verbi di richiesta HTTP) che si possono usare quando si esegue la richiesta. In questo esempio, sono consentiti solo PUT e GET. È possibile impostarlo su un carattere jolly (\*) per consentire l'uso di tutti i metodi.
* **AllowedHeaders** : sono le intestazioni della richiesta che possono essere specificate dal dominio di origine quando si esegue la richiesta. In questo esempio, sono consentite tutte le intestazioni dei di metadati che iniziano con x-ms-meta-data, x-ms-meta-target e x-ms-meta-abc. Il carattere jolly (\*) indica che è permessa qualsiasi intestazione che inizi col prefisso consentito.
* **ExposedHeaders** : indica quali intestazioni della risposta devono essere esposte al richiedente dal browser. In questo esempio, verranno esposte tutte le intestazioni che iniziano con "x-ms-meta-".
* **MaxAgeInSeconds** : indica il periodo massimo di memorizzazione della richiesta OPTIONS preliminare nella cache di un browser. Per altre informazioni sulla richiesta preliminare, vere il primo articolo di seguito.

#### <a name="resources"></a>Risorse
Per altre informazioni su CORS e su come abilitarlo, vedere queste risorse.

* [Supporto della condivisione risorse tra le origini (CORS) per i servizi di archiviazione di Azure in Azure.com](storage-cors-support.md)
  
  Questo articolo fornisce una panoramica di CORS e illustra come impostare le regole per i diversi servizi di archiviazione.
* [Supporto della condivisione risorse tra le origini (CORS) per i servizi di archiviazione di Azure in MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)
  
  Si tratta della documentazione di riferimento per il supporto di CORS per i servizi di archiviazione di Azure. Include i collegamenti ad articoli applicabili a ogni servizio di archiviazione e illustra un esempio descrivendo ogni elemento nel file CORS.
* [Archiviazione di Microsoft Azure: Introduzione a CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)
  
  Questo è un collegamento all'articolo relativo al blog iniziale che annuncia CORS e illustra come usarlo.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Domande frequenti sulla sicurezza di Archiviazione di Azure
1. **Come è possibile verificare l'integrità dei BLOB trasferiti e recuperati nell'archiviazione di Azure se non è possibile usare il protocollo HTTPS?**
   
   Se per qualsiasi motivo è necessario utilizzare il protocollo HTTP anziché HTTPS e si utilizzano BLOB in blocchi, è possibile utilizzare il controllo MD5 per verificare l'integrità dei BLOB in fase di trasferimento. Ciò contribuisce a proteggere dagli errori a livello di rete/trasporto, ma non necessariamente dalle violazioni.
   
   Se è possibile utilizzare HTTPS, che fornisce la protezione a livello di trasporto, il controllo MD5 è ridondante e superfluo.
   
   Per altre informazioni, vedere il blog relativo alla [panoramica di MD5 per i BLOB di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Come viene gestita la conformità FIPS per il Governo degli Stati Uniti?**
   
   I FIPS (Federal Information Processing Standard) degli Stati Uniti definiscono gli algoritmi di crittografia approvati per l'uso nei sistemi informatici del Governo degli Stati Uniti per la protezione dei dati sensibili. L'abilitazione della modalità FIPS su un desktop o un server Windows indica al sistema operativo che devono essere usati solo gli algoritmi di crittografia convalidati per FIPS. Se un'applicazione usa algoritmi non conformi, le applicazioni verranno interrotte. Con .NET Framework 4.5.2 o versione successiva, quando il computer è in modalità FIPS l'applicazione cambia automaticamente gli algoritmi di crittografia in modo che vengano usati di algoritmi conformi a FIPS.
   
   Microsoft lascia che ogni cliente decida se abilitare la modalità FIPS. Non esiste apparentemente alcun motivo valido per indurre i clienti che non sono soggetti alle norme governative ad abilitare la modalità FIPS per impostazione predefinita.
   
   **Risorse**

* [Perché non viene più consigliata la "Modalità FIPS"](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)
  
  Questo post di blog fornisce una panoramica di FIPS e spiega perché non viene più abilitata la modalità FIPS per impostazione predefinita.
* [Convalida FIPS 140](https://technet.microsoft.com/library/cc750357.aspx)
  
  Questo articolo fornisce informazioni sulla conformità dei prodotti e dei moduli di crittografia Microsoft allo standard FIPS per il Governo Federale degli Stati Uniti.
* [Effetti delle impostazioni di sicurezza "Crittografia di sistema: utilizza algoritmi FIPS compatibili per crittografia, hash e firma" in Windows XP e versioni successive di Windows](https://support.microsoft.com/kb/811833)
  
  Questo articolo illustra l'uso della modalità FIPS in computer Windows meno recenti.
