---
title: Integrazione del writeback delle password locale con Azure AD SSPR-Azure Active Directory
description: Recuperare le password cloud riscritte nell'infrastruttura di Active Directory locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 151e7b2ab091959c8b6c08d366318a49987aefd9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848528"
---
# <a name="what-is-password-writeback"></a>Che cos'è il writeback delle password?

L'uso di un'utilità di reimpostazione delle password basata sul cloud è molto utile, ma la maggior parte delle aziende usa ancora una directory locale in cui sono presenti gli utenti. In che modo Microsoft supporta il mantenimento della tradizionale istanza locale di Active Directory (AD) in sincronia con le modifiche di password nel cloud? Il writeback delle password è una funzione abilitata con [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) che consente di riscrivere le modifiche alle password nel cloud in una directory locale esistente in tempo reale.

Il writeback delle password è supportato in ambienti che usano:

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticazione pass-through](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Per i clienti che usano Azure AD Connect 1.0.8641.0 e versioni precedenti, il writeback delle password non funzionerà più dopo il [ritiro del Servizio di controllo di accesso di Azure in data 7 novembre 2018](../develop/active-directory-acs-migration.md). A partire da tale data, Azure AD Connect 1.0.8641.0 e versioni precedenti non consentiranno più il writeback delle password perché per questa funzionalità dipendono dal Servizio di controllo di accesso di Azure.
>
> Per evitare un'interruzione del servizio, eseguire l'aggiornamento da una versione precedente di Azure AD Connect a una più recente. Vedere l'articolo [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](../hybrid/how-to-upgrade-previous-version.md).
>

Il writeback delle password offre:

* **Applicazione di criteri delle password Active Directory locali**: quando un utente reimposta la propria password, viene verificato che soddisfi i criteri Active Directory locali prima di eseguirne il commit nella directory. Questo esame include il controllo di cronologia, complessità, validità, filtri delle password e altre restrizioni per le password definite in Active Directory locale.
* **Nessun ritardo nei feedback**: il writeback delle password è un'operazione sincrona. Gli utenti vengono informati immediatamente se la password non soddisfa i criteri o non può essere reimpostata o modificata per qualsiasi motivo.
* **Supporta la modifica delle password dal pannello di accesso e da Office 365**: quando gli utenti federati o sincronizzati con l'hash delle password modificano le password scadute o non scadute, queste verranno riscritte nell'ambiente Active Directory locale.
* **Supporta il writeback delle password quando un amministratore le reimposta dal portale di Azure**: quando un amministratore reimposta la password di un utente nel [portale di Azure](https://portal.azure.com), se tale utente è federato o sincronizzato con l'hash delle password, la password verrà riscritta in locale. Questa funzionalità non è attualmente supportata nel portale di amministrazione di Office.
* **Non richiede regole del firewall in entrata**: il writeback delle password usa un bus di servizio di Azure come canale di comunicazione sottostante. Tutte le comunicazioni sono in uscita tramite la porta 443.

> [!NOTE]
> Gli account amministratore presenti all'interno di gruppi protetti nell'istanza locale di AD possono essere usati con il writeback delle password. Gli amministratori possono modificare la propria password nel cloud, ma non possono usare la reimpostazione della password per reimpostare una password dimenticata. Per altre informazioni sui gruppi protetti, vedere l'articolo relativo ad [account e gruppi protetti in Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Requisiti di licenza per il writeback delle password

**La reimpostazione, la modifica e lo sblocco self-service della password con writeback locale sono funzionalità Premium di Azure AD**. Per altre informazioni sulle licenze, vedere il sito [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Per usare il writeback delle password, è necessario disporre una delle licenze seguenti assegnate nel tenant:

* Azure AD P1 Premium
* Azure AD P2 Premium
* Enterprise Mobility + Security E3 o A3
* Enterprise Mobility + Security E5 o A5
* Microsoft 365 E3 o A3
* Microsoft 365 E5 o A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> I piani di licenza Office 365 autonomi *non supportano "Reimpostazione/modifica/sblocco con writeback in locale delle password in modalità self-service"* e richiedono uno dei piani precedenti per l'uso della funzionalità.

## <a name="how-password-writeback-works"></a>Funzionamento del writeback delle password

Quando un utente federato o sincronizzato con l'hash delle password tenta di reimpostare o modificare la propria password nel cloud, si verifica quanto segue:

1. Viene verificato il tipo di password usato dall'utente. Se la password è gestita in locale:
   * Si verifica che il servizio di writeback sia attivo e in esecuzione. In questo caso viene consentito all'utente di continuare.
   * Se il servizio di writeback non è attivo, l'utente viene informato che la password non può essere reimpostata al momento.
1. L'utente supera quindi i controlli di autenticazione appropriati e raggiunge la pagina **Reimposta password**.
1. L'utente seleziona una nuova password e la conferma.
1. Quando l'utente seleziona **Invia**, la password non crittografata viene crittografata con una chiave simmetrica creata durante il processo di installazione della funzionalità di writeback.
1. La password crittografata viene inclusa in un payload inviato tramite un canale HTTPS al servizio Inoltro del bus di servizio specifico del tenant, che viene configurato automaticamente durante il processo di installazione della funzionalità di writeback. L'inoltro è protetto da una password generata casualmente, nota solo all'installazione locale.
1. Dopo che il messaggio ha raggiunto il bus di servizio, l'endpoint di reimpostazione della password si attiva automaticamente e rileva la richiesta di reimpostazione in sospeso.
1. Il servizio cerca quindi l'utente usando l'attributo di ancoraggio cloud. Affinché questa ricerca abbia esito positivo:

   * L'oggetto utente deve esistere nello spazio connettore Active Directory.
   * L'oggetto utente deve essere collegato all'oggetto metaverse (MV) corrispondente.
   * L'oggetto utente deve essere collegato all'oggetto Azure Active Directory Connector corrispondente.
   * Il collegamento dall'oggetto connettore di Active Directory all'oggetto MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento.
   
   Quando la chiamata proviene dal cloud, il motore di sincronizzazione usa l'attributo **cloudAnchor** per cercare l'oggetto spazio connettore di Azure Active Directory. Quindi segue il collegamento all'oggetto MV e poi segue il collegamento che riporta all'oggetto Active Directory. Poiché possono essere presenti più oggetti Active Directory (più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul collegamento `Microsoft.InfromADUserAccountEnabled.xxx` per scegliere quello corretto.

1. Dopo che l'account utente è stato trovato, viene tentata la reimpostazione della password direttamente nella foresta Active Directory appropriata.
1. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata.
   > [!NOTE]
   > Se l'hash delle password dell'utente viene sincronizzato con Azure AD usando la sincronizzazione dell'hash delle password, è probabile che i criteri della password locali siano più deboli rispetto ai criteri della password cloud. In questo caso vengono applicati i criteri locali. Questi criteri garantiscono che i criteri locali siano applicati nel cloud, indipendentemente dal fatto che si usi la sincronizzazione dell'hash delle password o la federazione per il Single Sign-On.

1. Se l'operazione di impostazione della password non riesce, viene restituito un messaggio di errore con la richiesta all'utente di riprovare. L'operazione potrebbe non riuscire perché:
    * Il servizio era inattivo.
    * La password selezionata non rispettava i criteri dell'organizzazione.
    * Non è stato trovato l'utente in Azure Active Directory locale.

      I messaggi di errore forniscono indicazioni agli utenti in modo che possano tentare di risolvere il problema senza l'intervento dell'amministratore.

## <a name="password-writeback-security"></a>Sicurezza del writeback delle password

Il writeback delle password è un servizio altamente sicuro. Per garantire che le informazioni siano protette, viene abilitato un modello di sicurezza a quattro livelli descritto di seguito:

* **Inoltro del bus di servizio specifico del tenant**
   * Quando si configura il servizio, viene configurato l'inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password**
   * Dopo la creazione dell'inoltro del bus di servizio, viene creata una chiave asimmetrica complessa, usata per la crittografia della password durante la trasmissione. Questa chiave si trova solo nell'archivio segreto dell'azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi altra password nella directory.
* **Transport Layer Security (TLS) standard di settore**
   1. Quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata con la chiave pubblica dell'utente.
   1. La chiave crittografata viene inclusa in un messaggio HTTPS inviato tramite un canale crittografato mediante certificati SSL Microsoft al proprio inoltro del bus di servizio.
   1. Quando il messaggio arriva nel bus di servizio, l'agente locale viene attivato e autenticato al bus di servizio tramite la password complessa generata in precedenza.
   1. L'agente locale preleva il messaggio crittografato e lo decrittografa con la chiave privata.
   1. L'agente locale tenta di impostare la password tramite l'API SetPassword di AD DS. Questo passaggio consente di applicare i criteri password locali di Active Directory, come complessità, validità, cronologia, filtri e così via, nel cloud.
* **Criteri di scadenza del messaggio**
   * Se il messaggio si trova nel bus di servizio perché il servizio locale è inattivo, raggiungerà il timeout e verrà rimosso dopo alcuni minuti. Il timeout e la rimozione del messaggio migliorano ulteriormente la sicurezza.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password

Dopo che un utente ha inviato una reimpostazione password, la richiesta di reimpostazione passa attraverso diverse operazioni di crittografia prima di arrivare nell'ambiente locale. Queste operazioni di crittografia garantiscono la massima affidabilità del servizio e sicurezza. Sono descritte come segue:

* **Passaggio 1 - Crittografia della password con una chiave RSA a 2048 bit**: dopo che un utente ha inviato una password per il writeback in locale, la password inviata viene crittografata con una chiave RSA a 2048 bit.
* **Passaggio 2 - Crittografia a livello di pacchetto con AES-GCM**: l'intero pacchetto, ovvero la password più i metadati necessari, viene crittografato con AES-GCM. Questa crittografia impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto.
* **Passaggio 3 - Tutte le comunicazioni avvengono tramite TLS/SSL**: tutte le comunicazioni con ServiceBus avvengono in un canale SSL/TLS. Questa crittografia protegge i contenuti da terze parti non autorizzate.
* **Rollover automatico delle chiavi ogni 6 mesi**: ogni 6 mesi o ogni volta che il writeback delle password viene disabilitato e di nuovo abilitato in Azure AD Connect, viene eseguito automaticamente il rollover di tutte le chiavi per garantire i massimi livelli di sicurezza e protezione del servizio.

### <a name="password-writeback-bandwidth-usage"></a>Utilizzo della larghezza di banda per il writeback delle password

Il writeback delle password è un servizio a larghezza di banda ridotta, che invia le richieste all'agente locale solo nelle circostanze seguenti:

* Vengono inviati due messaggi quando la funzionalità viene abilitata o disabilitata tramite Azure AD Connect.
* Viene inviato un messaggio viene ogni 5 minuti come heartbeat del servizio per la durata dell'esecuzione del servizio.
* Vengono inviati due messaggi ogni volta che viene inviata una nuova password:
   * Il primo messaggio è un richiesta di eseguire l'operazione.
   * Il secondo messaggio contiene il risultato dell'operazione e viene inviato nelle circostanze seguenti:
      * Ogni volta che viene inviata una nuova password durante la reimpostazione self-service della password utente.
      * Ogni volta che viene inviata una nuova password durante un'operazione di modifica della password utente.
      * Ogni volta che viene inviata una nuova password durante una reimpostazione della password utente avviata dall'amministratore, solo dal portale di amministrazione di Azure.

#### <a name="message-size-and-bandwidth-considerations"></a>Considerazioni sulle dimensioni dei messaggi e sulla larghezza di banda

La dimensione di ogni messaggio illustrato in precedenza è in genere inferiore a 1 KB. Anche in caso di carichi estremi, il servizio di writeback delle password stesso usa qualche kilobit di larghezza di banda al secondo. Poiché ogni messaggio viene inviato in tempo reale, solo quando necessario per un'operazione di aggiornamento della password, e poiché le dimensioni dei messaggi sono così ridotte, l'utilizzo della larghezza di banda da parte della funzionalità di writeback è troppo basso per avere un impatto significativo.

## <a name="supported-writeback-operations"></a>Operazioni di writeback supportate

Il writeback delle password viene eseguito in tutte le situazioni seguenti:

* **Attività degli utenti finali supportate**
   * Qualsiasi operazione self-service volontaria di modifica della password dell'utente finale
   * Qualsiasi operazione self-service forzata di modifica della password dell'utente finale, ad esempio in seguito a scadenza della password
   * Qualsiasi reimpostazione password self-service dell'utente finale originata dal [portale di reimpostazione password](https://passwordreset.microsoftonline.com)
* **Operazioni degli amministratori supportate**
   * Qualsiasi operazione self-service volontaria di modifica della password dell'amministratore
   * Qualsiasi operazione self-service forzata di modifica della password dell'amministratore, ad esempio in seguito a scadenza della password
   * Qualsiasi reimpostazione password self-service dell'amministratore originata dal [portale di reimpostazione password](https://passwordreset.microsoftonline.com)
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operazioni di writeback non supportate

Il writeback delle password *non* viene eseguito nelle situazioni seguenti:

* **Attività degli utenti finali non supportate**
   * Qualsiasi reimpostazione, da parte dell'utente finale, della propria password tramite PowerShell versione 1, versione 2 o l'API Graph di Azure AD
* **Operazioni degli amministratori non supportate**
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell versione 1, versione 2 o dall'API Graph di Azure AD
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dall'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Uso della casella di controllo "l'utente deve modificare la password all'accesso successivo" in locale Active Directory strumenti di amministrazione come Active Directory utenti e computer oppure il Centro di amministrazione di Active Directory è supportato come funzionalità di anteprima di Azure AD Connect. Per altre informazioni, vedere l'articolo [implementare la sincronizzazione dell'hash delle password con Azure ad Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md#public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon).

## <a name="next-steps"></a>Passaggi successivi

Abilitare il writeback delle password con l'esercitazione: [Abilitazione del writeback delle password](tutorial-enable-writeback.md)
