---
title: Writeback delle password locali con reimpostazione della password self-service - Azure Active Directory
description: Informazioni su come eseguire il writeback degli eventi di modifica o reimpostazione della password in Azure Active Directory in un ambiente di directory locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 690dead3cb0059dd1b20ff042a93c36d674e62d2
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052682"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Come funziona il writeback per la reimpostazione della password self-service in Azure Active Directory?

La reimpostazione della password self-service (SSPR) in Azure Active Directory (Azure AD) consente agli utenti di reimpostare le password nel cloud, ma la maggior parte delle società dispone anche di un ambiente di Active Directory Domain Services (AD DS) locale in cui si trovano gli utenti. Il writeback delle password è una funzione abilitata con [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) che consente di riscrivere le modifiche alle password nel cloud in una directory locale esistente in tempo reale. In questa configurazione, quando gli utenti modificano o reimpostano le proprie password usando SSPR nel cloud, le password aggiornate vengono anche scritte nell'ambiente Active Directory Domain Services locale

> [!IMPORTANT]
> Questo articolo concettuale spiega a un amministratore come funziona il writeback della reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service e si deve ripristinare l'accesso al proprio account, passare a https://aka.ms/sspr.
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

Il writeback delle password è supportato in ambienti che usano i seguenti modelli di identità ibridi:

* [Sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticazione pass-through](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Il writeback delle password offre le funzionalità seguenti:

* **Applicazione di criteri delle password Active Directory Domain Services (AD DS) locali**: quando un utente reimposta la propria password, viene verificato che soddisfi i criteri di AD DS locali prima di eseguirne il commit nella directory. Questo esame include il controllo di cronologia, complessità, validità, filtri delle password e altre restrizioni per le password definite in AD DS.
* **Nessun ritardo nel feedback**:  Il writeback delle password è un'operazione sincrona. Gli utenti vengono informati immediatamente se la password non soddisfa i criteri o non può essere reimpostata o modificata per qualsiasi motivo.
* **Supporta le modifiche delle password dal pannello di accesso e Microsoft 365**: quando gli utenti federati o con sincronizzazione di hash della password cambiano le password scadute o non scadute, le password vengono riscritte in servizi di dominio Active Directory.
* **Supporta il writeback delle password quando un amministratore le reimposta dal portale di Azure**: quando un amministratore reimposta la password di un utente nel [portale di Azure](https://portal.azure.com), se tale utente è federato o sincronizzato con l'hash delle password, la password verrà riscritta in locale. Questa funzionalità non è attualmente supportata nel portale di amministrazione di Office.
* **Non richiede regole del firewall in entrata**: il writeback delle password usa un bus di servizio di Azure come canale di comunicazione sottostante. Tutte le comunicazioni sono in uscita tramite la porta 443.

> [!NOTE]
> Gli account amministratore presenti all'interno di gruppi protetti nell'istanza locale di AD possono essere usati con il writeback delle password. Gli amministratori possono modificare la propria password nel cloud, ma non possono usare la reimpostazione della password per reimpostare una password dimenticata. Per ulteriori informazioni sui gruppi protetti, vedere [account protetti e gruppi in servizi di dominio Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Per iniziare a usare il writeback della reimpostazione della password self-service, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare il writeback della reimpostazione della password self-service](./tutorial-enable-sspr-writeback.md)

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
1. Il servizio cerca quindi l'utente usando l'attributo di ancoraggio cloud. Affinché questa ricerca abbia esito positivo devono essere soddisfatte le condizioni seguenti:

   * L'oggetto utente deve esistere nello spazio connettore servizi di dominio Active Directory.
   * L'oggetto utente deve essere collegato all'oggetto metaverse (MV) corrispondente.
   * L'oggetto utente deve essere collegato all'oggetto connettore Azure AD corrispondente.
   * Il collegamento dall'oggetto connettore servizi di dominio Active Directory alla MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento.

   Quando la chiamata viene ricevuta dal cloud, il motore di sincronizzazione usa l'attributo **cloudAnchor** per cercare l'oggetto spazio connettore Azure ad. Segue quindi il collegamento all'oggetto MV e quindi segue il collegamento all'oggetto servizi di dominio Active Directory. Poiché possono essere presenti più oggetti di servizi di dominio Active Directory (a più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul `Microsoft.InfromADUserAccountEnabled.xxx` collegamento per scegliere quello corretto.

1. Dopo che l'account utente è stato trovato, viene eseguito un tentativo di reimpostare la password direttamente nella foresta di servizi di dominio Active Directory appropriata.
1. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata.

   > [!NOTE]
   > Se l'hash delle password dell'utente viene sincronizzato con Azure AD usando la sincronizzazione dell'hash delle password, è probabile che i criteri della password locali siano più deboli rispetto ai criteri della password cloud. In questo caso vengono applicati i criteri locali. Questi criteri garantiscono che i criteri locali siano applicati nel cloud, indipendentemente dal fatto che si usi la sincronizzazione dell'hash delle password o la federazione per il Single Sign-On.

1. Se l'operazione di impostazione della password non riesce, viene restituito un messaggio di errore con la richiesta all'utente di riprovare. L'operazione potrebbe non riuscire per i motivi seguenti:
    * Il servizio era inattivo.
    * La password selezionata non rispetta i criteri dell'organizzazione.
    * Impossibile trovare l'utente nell'ambiente Servizi di dominio Active Directory locale.

   I messaggi di errore forniscono indicazioni agli utenti in modo che possano tentare di risolvere il problema senza l'intervento dell'amministratore.

## <a name="password-writeback-security"></a>Sicurezza del writeback delle password

Il writeback delle password è un servizio altamente sicuro. Per garantire che le informazioni siano protette, viene abilitato un modello di sicurezza a quattro livelli come descritto di seguito:

* **Inoltro del bus di servizio specifico del tenant**
   * Quando si configura il servizio, viene configurato l'inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password**
   * Dopo la creazione dell'inoltro del bus di servizio, viene creata una chiave asimmetrica complessa, usata per la crittografia della password durante la trasmissione. Questa chiave si trova solo nell'archivio segreto dell'azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi altra password nella directory.
* **Transport Layer Security (TLS) standard di settore**
   1. Quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata con la chiave pubblica dell'utente.
   1. La chiave crittografata viene inclusa in un messaggio HTTPS inviato tramite un canale crittografato mediante certificati TLS/SSL Microsoft al proprio inoltro del bus di servizio.
   1. Quando il messaggio arriva nel bus di servizio, l'agente locale viene attivato e autenticato al bus di servizio tramite la password complessa generata in precedenza.
   1. L'agente locale preleva il messaggio crittografato e lo decrittografa con la chiave privata.
   1. L'agente locale tenta di impostare la password tramite l'API SetPassword di AD DS. Questo passaggio consente di applicare i criteri password locali di servizi di dominio Active Directory (ad esempio complessità, validità, cronologia e filtri) nel cloud.
* **Criteri di scadenza del messaggio**
   * Se il messaggio si trova nel bus di servizio perché il servizio locale è inattivo, raggiungerà il timeout e verrà rimosso dopo alcuni minuti. Il timeout e la rimozione del messaggio migliorano ulteriormente la sicurezza.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password

Dopo che un utente ha inviato una reimpostazione password, la richiesta di reimpostazione passa attraverso diverse operazioni di crittografia prima di arrivare nell'ambiente locale. Queste operazioni di crittografia garantiscono la massima affidabilità del servizio e sicurezza. Sono descritte come segue:

1. **Crittografia della password con una chiave RSA a 2048 bit**: dopo che un utente ha inviato una password per il writeback in locale, la password inviata viene crittografata con una chiave RSA a 2048 bit.
1. **Crittografia a livello di pacchetto con AES-GCM**: l'intero pacchetto, ovvero la password più i metadati necessari, viene crittografato con AES-GCM. Questa crittografia impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto.
1. **Tutte le comunicazioni avvengono tramite TLS/SSL**: tutte le comunicazioni con il bus di servizio avvengono in un canale SSL/TLS. Questa crittografia protegge i contenuti da terze parti non autorizzate.
1. **Rollover automatico della chiave ogni sei mesi**: tutte le chiavi vengono rivolte ogni sei mesi o ogni volta che il writeback delle password viene disabilitato e quindi riabilitato in Azure ad Connect, per garantire la massima sicurezza e sicurezza del servizio.

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
   * Qualsiasi operazione self-service volontaria di modifica della password dell'utente finale.
   * Qualsiasi operazione self-service forzata di modifica della password dell'utente finale, ad esempio in seguito a scadenza della password.
   * Qualsiasi reimpostazione password self-service dell'utente finale originata dal [portale di reimpostazione password](https://passwordreset.microsoftonline.com).

* **Operazioni degli amministratori supportate**
   * Qualsiasi operazione self-service volontaria di modifica della password dell'amministratore.
   * Qualsiasi operazione self-service forzata di modifica della password dell'amministratore, ad esempio in seguito a scadenza della password.
   * Qualsiasi reimpostazione password self-service dell'amministratore originata dal [portale di reimpostazione password](https://passwordreset.microsoftonline.com).
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di Azure](https://portal.azure.com).
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dall'[API Graph di Microsoft versione beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Operazioni di writeback non supportate

Il writeback delle password non viene eseguito nelle situazioni seguenti:

* **Attività degli utenti finali non supportate**
   * Qualsiasi reimpostazione, da parte dell'utente finale, della propria password tramite PowerShell versione 1, versione 2 o l'API Graph di Microsoft.
* **Operazioni degli amministratori non supportate**
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell versione 1, versione 2 o dall'API Graph di Microsoft (l'[API Graph di Microsoft versione beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) è supportata).
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dall'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).
   * Qualsiasi amministratore non può usare lo strumento di reimpostazione della password per reimpostare la propria password per il writeback delle password.

> [!WARNING]
> L'uso della casella di controllo "Cambiamento obbligatorio password all'accesso successivo" negli strumenti di amministrazione di Active Directory Domain Services locali come Utenti e computer di Active Directory oppure il Centro di amministrazione di Active Directory è supportato come funzionalità di anteprima di Azure AD Connect. Per altre informazioni, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare il writeback della reimpostazione della password self-service, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare il writeback della reimpostazione della password self-service](./tutorial-enable-sspr-writeback.md)