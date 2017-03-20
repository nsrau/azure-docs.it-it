---
title: 'Altre informazioni: Gestione delle password di Azure Active Directory | Documentazione Microsoft'
description: Argomenti avanzati sulla gestione delle password di Azure Active Directory, ad esempio come funziona il writeback delle password, sicurezza del writeback delle password, come funziona il portale di reimpostazione della password e quali dati vengono usati per la reimpostazione della password.
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: aff8831a48d2283daa727db6a8f47a1ff4b8eb4c
ms.lasthandoff: 03/07/2017


---
# <a name="learn-more-about-password-management"></a>Altre informazioni sulla gestione delle password
> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
>
>

Se si è già distribuita la funzionalità di gestione delle password o se si è semplicemente interessati agli aspetti tecnici essenziali del funzionamento prima della distribuzione, in questa sezione viene fornita una panoramica dei concetti tecnici alla base del servizio. Verranno trattati gli argomenti seguenti:

* [**Funzionamento del portale di reimpostazione delle password**](#how-does-the-password-reset-portal-work)
* [**Panoramica del writeback delle password**](#password-writeback-overview)
 * [Funzionamento del writeback delle password](#how-password-writeback-works)
* [**Scenari supportati per il writeback delle password**](#scenarios-supported-for-password-writeback)
 * [Client Azure AD Connect, Azure AD Sync e DirSync supportati](#supported-clients)
 * [Licenze richieste per il writeback delle password](#licenses-required-for-password-writeback)
 * [Modalità di autenticazione locale supportate per il writeback delle password](#on-premises-authentication-modes-supported-for-password-writeback)
 * [Attività di utenti e amministratori supportate per il writeback delle password](#user-and-admin-operations-supported-for-password-writeback)
 * [Attività di utenti e amministratori non supportate per il writeback delle password](#user-and-admin-operations-not-supported-for-password-writeback)
* [**Modello di sicurezza del writeback delle password**](#password-writeback-security-model)
 * [Informazioni dettagliate sulla crittografia del writeback delle password](#password-writeback-encryption-details)
 * [Utilizzo della larghezza di banda per il writeback delle password](#password-writeback-bandwidth-usage)
* [**Distribuzione e gestione dei dati di reimpostazione della password per gli utenti e accesso a tali dati**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [Dati usati per la reimpostazione della password](#what-data-is-used-by-password-reset)
 * [Distribuzione della reimpostazione della password senza richiedere la registrazione dell'utente finale](#deploying-password-reset-without-requiring-end-user-registration)
 * [Cosa accade quando un utente si registra per la reimpostazione della password?](#what-happens-when-a-user-registers)
 * [Come accedere ai dati di reimpostazione della password per gli utenti](#how-to-access-password-reset-data-for-your-users)
 * [Impostazione dei dati di reimpostazione della password con PowerShell](#setting-password-reset-data-with-powershell)
 * [Lettura dei dati di reimpostazione della password con PowerShell](#reading-password-reset-data-with-powershell)
* [**Come funziona la reimpostazione della password per gli utenti B2B?**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>Funzionamento del portale di reimpostazione delle password
Quando un utente accede al portale di reimpostazione della password, viene avviato un flusso di lavoro per stabilire se l'account utente è valido e se l'utente dispone della licenza per usare la funzionalità e per determinare l'organizzazione a cui l'utente appartiene e la posizione in cui viene gestita la password dell'utente.  Leggere i passaggi seguenti per informazioni sulla logica alla base della pagina di reimpostazione della password.

1. L'utente fa clic sul collegamento relativo all'impossibilità di accedere al proprio account o accede direttamente alla pagina [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. L'utente immette un ID utente e un captcha.
3. Azure AD verifica se l'utente è in grado di usare questa funzionalità tramite le seguenti operazioni:
   * Verifica che questa funzionalità sia abilitata per l'utente e che all'utente sia assegnata una licenza Azure AD.
     * Se la funzionalità non è abilitata o se l'utente non dispone della licenza, per reimpostare la password l'utente deve rivolgersi al proprio amministratore.
   * Verifica che per l'account dell'utente siano stati definiti i dati di test corretti, in conformità con i criteri dell'amministratore.
     * Se i criteri prevedono un solo test, viene verificato se per l'utente sono definiti i dati appropriati per almeno uno dei test abilitati dai criteri dell'amministratore.
       * Se l'utente non è configurato, verrà invitato a contattare l'amministratore per reimpostare la password.
     * Se i criteri prevedono due test, viene verificato se per l'utente sono definiti i dati appropriati per almeno due dei test abilitati dai criteri dell'amministratore.
       * Se l'utente non è configurato, verrà invitato a contattare l'amministratore per reimpostare la password.
   * Verifica se la gestione della password dell'utente viene eseguita in locale o meno (con federazione o con sincronizzazione degli hash delle password).
     * Se il writeback è stato distribuito e la password dell'utente è gestita in locale, l'utente può continuare con il processo di autenticazione e di reimpostazione della password.
     * Se il writeback non è stato distribuito e la password dell'utente viene gestita in locale, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.
4. Se viene stabilito che l'utente è in grado di reimpostare la password, viene avviata la procedura di reimpostazione.

Per altre informazioni su come distribuire il writeback delle password, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).

## <a name="password-writeback-overview"></a>Panoramica del writeback delle password
Writeback password è un componente di [Azure Active Directory Connect](connect/active-directory-aadconnect.md) che può essere abilitato e usato dagli attuali sottoscrittori di Azure Active Directory Premium. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Il writeback delle password consente di configurare il tenant cloud per scrivere automaticamente le password nuovamente in Active Directory locale.  Evita di dover configurare e gestire una complessa soluzione di reimpostazione della password self-service locale, e offre agli utenti un modo pratico e basato sul cloud per reimpostare le password locali ovunque essi si trovino.  Di seguito sono riportate altre informazioni su alcune delle funzionalità principali del writeback delle password:

* **Feedback immediato.**  Il writeback delle password è un'operazione sincrona.  Gli utenti saranno informati immediatamente se la password non soddisfa i criteri o non può essere reimpostata o modificata per qualsiasi motivo.
* **Supporta la reimpostazione delle password per gli utenti con ADFS o altre tecnologie di federazione.**  Con il writeback delle password gli account utente federati potranno gestire le proprie password AD locali dal cloud, purché siano sincronizzati nel tenant di Azure AD.
* **Supporta la reimpostazione delle password per gli utenti con la sincronizzazione degli hash delle password.** Quando il servizio di reimpostazione delle password rileva che un account utente sincronizzato è abilitato per la sincronizzazione degli hash delle password, la password locale e la password cloud dell'account vengono reimpostate simultaneamente.
* **Supporta la modifica delle password dal pannello di accesso e da Office 365.**  Quando gli utenti federati o sincronizzati con password modificano le password scadute o non scadute, queste verranno scritte anche nell'ambiente AD locale.
* **Supporta il writeback delle password in caso di reimpostazione da parte degli amministratori nel** [**portale di gestione di Azure**](https://manage.windowsazure.com).  Quando un amministratore reimposta la password di un utente nel [portale di gestione di Azure](https://manage.windowsazure.com), se tale utente è federato o sincronizzato con password, la password selezionata dall'amministratore verrà impostata anche nell'ambiente AD locale.  Ciò non è attualmente supportato nel portale di amministrazione di Office.
* **Applica i criteri password AD locali.**  Quando un utente Reimposta la propria password, verificare che soddisfi i criteri AD locali prima di eseguirne il commit a tale directory.  Sono inclusi cronologia, complessità, validità, filtri delle password e altre restrizioni per le password definite in AD locale.
* **Non richiede regole del firewall per il traffico in ingresso.**  Per il writeback delle password viene usato Inoltro del bus di servizio di Azure come canale di comunicazione sottostante. In questo modo non è necessario aprire porte in ingresso nel firewall per usare questa funzionalità.
* **Non è supportato per gli account utente esistenti nei gruppi protetti in Active Directory locale.** Per altre informazioni sui gruppi protetti, vedere l'articolo relativo ad [account e gruppi protetti in Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Funzionamento del writeback delle password
Il writeback delle password include tre componenti principali:

* Servizio cloud Reimpostazione password (integrato anche nelle pagine di modifica delle password di Azure AD)
* Inoltro del bus di servizio di Azure specifico del tenant
* Endpoint di reimpostazione delle password locale

Interagiscono come descritto nel diagramma seguente:

  ![][001]

Quando un utente federato e con sincronizzazione di hash della password reimposta o modifica la propria password nel cloud, si verifica quanto segue:

1. Viene verificato il tipo di password usato dall'utente.  Se risulta che la password è gestita in locale, viene verificato se il servizio di writeback è in esecuzione.  In questo caso viene consentito all'utente di procedere; in caso contrario, l'utente viene informato che la password non può essere reimpostata qui.
2. L'utente supera quindi i controlli di autenticazione appropriati e raggiunge la schermata Reimposta password.
3. L'utente seleziona una nuova password e la conferma.
4. Facendo clic su Invia, la password non crittografata viene crittografata con una chiave simmetrica creata durante il processo di installazione della funzionalità di writeback.
5. Dopo essere stata crittografata, la password viene inclusa in un payload inviato tramite un canale HTTPS al servizio Inoltro del bus di servizio specifico del tenant (che viene configurato automaticamente durante il processo di installazione della funzionalità di writeback).  L'inoltro è protetto da una password generata casualmente, nota solo all'installazione locale.
6. Dopo che il messaggio avrà raggiunto il bus di servizio, l'endpoint di reimpostazione della password si attiva automaticamente e rileva la richiesta di reimpostazione in sospeso.
7. Il servizio cerca quindi l'utente in questione usando l'attributo di ancoraggio cloud.  Perché la ricerca abbia esito positivo, l'oggetto utente deve esistere nello spazio connettore AD ed essere collegato all'oggetto MV corrispondente e all'oggetto connettore AAD corrispondente. Infine, la sincronizzazione individuerà l'account utente solo se il collegamento dell'oggetto connettore AD all'oggetto MV include la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` .  Questo è necessario perché, alla ricezione della chiamata dal cloud, il motore di sincronizzazione usa l'attributo cloudAnchor per cercare l'oggetto spazio connettore AAD, quindi segue a ritroso il collegamento all'oggetto MV e quindi all'oggetto AD. Poiché è possibile che siano presenti più oggetti AD (più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul collegamento `Microsoft.InfromADUserAccountEnabled.xxx` per scegliere quello corretto.
8. Dopo aver trovato l'account utente, viene tentata la reimpostazione della password direttamente nella foresta Active Directory appropriata.
9. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata e che può proseguire.
10. Se l'operazione di impostazione della password non riesce, l'errore viene restituito all'utente, consentendogli di riprovare.  L'operazione potrebbe non riuscire perché il servizio è inattivo, la password selezionata non soddisfa i criteri dell'organizzazione, l'utente non viene trovato in Active Directory locale o per altri motivi.  È disponibile un messaggio specifico per molti di questi casi e all'utente viene indicato cosa può fare per risolvere il problema.

## <a name="scenarios-supported-for-password-writeback"></a>Scenari supportati per il writeback delle password
La sezione seguente descrive gli scenari supportati per le diverse versioni delle funzionalità di sincronizzazione.  In generale, è sempre consigliabile usare la funzionalità di aggiornamento automatico di Azure AD Connect oppure installare la versione più recente di [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) se si vuole usare il writeback delle password.

* [**Client Azure AD Connect, Azure AD Sync e DirSync supportati**](#supported-clients)
* [**Licenze richieste per il writeback delle password**](#licenses-required-for-password-writeback)
* [**Modalità di autenticazione locale supportate per il writeback delle password**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**Attività di utenti e amministratori supportate per il writeback delle password**](#user-and-admin-operations-supported-for-password-writeback)
* [**Attività di utenti e amministratori non supportate per il writeback delle password**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>Client supportati
È sempre consigliabile usare la funzionalità di aggiornamento automatico di Azure AD Connect oppure installare la versione più recente di [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) se si vuole usare il writeback delle password.

* **DirSync (qualsiasi versione > 1.0.6862)** - _NON SUPPORTATO_: supporta solo funzionalità di writeback di base e non è più supportato dal gruppo di prodotti 
* **Azure AD Sync** - _DEPRECATO_: supporta solo funzionalità di writeback di base e non offre le funzionalità di sblocco degli account, la registrazione avanzata e i miglioramenti dell'affidabilità apportati in Azure AD Connect È quindi **fortemente** consigliabile eseguire l'aggiornamento.
* **Azure AD Connect** - _COMPLETAMENTE SUPPORTATO_: supporta tutte le funzionalità di writeback. Eseguire l'aggiornamento alla versione più recente per ottenere le migliori nuove funzionalità e il massimo livello possibile di stabilità e affidabilità

### <a name="licenses-required-for-password-writeback"></a>Licenze richieste per il writeback delle password
Per usare il writeback delle password, è necessario avere una delle licenze seguenti assegnate nel tenant.

* **Azure AD P1 Premium**: nessuna limitazione per l'utilizzo del writeback delle password
* **Azure AD P2 Premium**: nessuna limitazione per l'utilizzo del writeback delle password
* **Enterprise Moblity Suite**: nessuna limitazione per l'utilizzo del writeback delle password
* **Enterprise Cloud Suite**: nessuna limitazione per l'utilizzo del writeback delle password

Non è consentito usare il writeback delle password con qualsiasi piano di licenza di Office 365, versione di valutazione o a pagamento. Per usare questa funzionalità, è necessario eseguire l'aggiornamento a uno dei piani indicati in precedenza. 

Non è prevista l'abilitazione del writeback delle password per qualsiasi SKU di Office 365.

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modalità di autenticazione locale supportate per il writeback delle password
Il writeback delle password funziona per i tipi di password utente seguenti:

* **Utenti solo cloud**: il writeback delle password non si applica in questa situazione, perché non c'è una password locale
* **Utenti con sincronizzazione delle password**: il writeback delle password è supportato
* **Utenti federati**: il writeback delle password è supportato
* **Utenti con autenticazione pass-through**: il writeback delle password è supportato

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Attività di utenti e amministratori supportate per il writeback delle password
Il writeback delle password viene eseguito in tutte le situazioni seguenti:

* **Attività degli utenti finali supportate**
 * Qualsiasi operazione self-service volontaria di modifica della password dell'utente finale
 * Qualsiasi operazione self-service forzata di modifica della password dell'utente finale (ad esempio in seguito a scadenza della password)
 * Qualsiasi reimpostazione della password self-service dell'utente finale originata dal [portale di reimpostazione della password](https://passwordreset.microsoftonline.com)
* **Operazioni degli amministratori supportate**
 * Qualsiasi operazione self-service volontaria di modifica della password dell'amministratore
 * Qualsiasi operazione self-service forzata di modifica della password dell'amministratore (ad esempio in seguito a scadenza della password)
 * Qualsiasi reimpostazione della password self-service dell'amministratore originata dal [portale di reimpostazione della password](https://passwordreset.microsoftonline.com)
 * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di gestione di Azure classico](https://manage.windowsazure.com)
 * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Attività di utenti e amministratori non supportate per il writeback delle password
Il writeback delle password non viene eseguito nelle situazioni seguenti:

* **Attività degli utenti finali non supportate**
 * Qualsiasi reimpostazione, da parte dell'utente finale, della propria password usando PowerShell v1, v2 o l'API Graph di Azure AD
* **Operazioni degli amministratori non supportate**
 * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di gestione di Office](https://portal.office.com)
 * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell v1, v2 o dall'API Graph di Azure AD
 
Microsoft sta lavorando per rimuovere queste limitazioni, ma non è ancora disponibile una sequenza temporale specifica.

## <a name="password-writeback-security-model"></a>Modello di sicurezza del writeback delle password
Il writeback delle password è un servizio altamente sicuro e affidabile.  Per garantire che le informazioni siano protette, viene abilitato un modello di protezione a 4 livelli descritto di seguito.

* **Inoltro del bus di servizio specifico del tenant** - quando si configura il servizio, viene configurato automaticamente Inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password** - dopo la creazione di Inoltro del bus di servizio, viene creata una chiave asimmetrica complessa, usata per la crittografia della password durante la trasmissione.  Questa chiave si trova solo nell'archivio segreto dell’azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi password nella directory.
* **TLS standard di settore** : quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata automaticamente con la chiave pubblica dell'utente.  Viene quindi inclusa in un messaggio HTTPS inviato tramite un canale crittografato con certificati SSL Microsoft a Inoltro del bus di servizio.  Dopo l'arrivo del messaggio arriva nel bus di servizio, l'agente locale si riattiva, si autentica con il bus di servizio usando la password complessa generata in precedenza, preleva il messaggio crittografato, lo decrittografa con la chiave privata generata e quindi tenta di impostare la password tramite l'API SetPassword di Servizi di dominio Active Directory.  Questo passaggio consente di applicare i criteri password locali di AD (complessità, validità, cronologia, filtri e così via) nel cloud.
* **Criteri di scadenza del messaggio** : se per qualche motivo il messaggio rimane nel bus di servizio poiché il servizio locale non è disponibile, verrà infine applicato il timeout e il messaggio verrà rimosso dopo alcuni minuti, per assicurare una sicurezza maggiore.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password
Di seguito vengono descritti i passaggi di crittografia applicati a una richiesta di reimpostazione della password dopo l'invio da parte dell'utente, ma prima di raggiungere l'ambiente locale, per garantire massimi livelli di sicurezza e affidabilità del servizio.

* **Passaggio 1 - Crittografia della password con una chiave RSA a 2048 bit**: quando un utente invia una password per il writeback in locale, prima di tutto la password inviata viene crittografata con una chiave RSA a 2048 bit. 

* **Passaggio 2 - Crittografia a livello di pacchetto con AES-GCM**: l'intero pacchetto (password + metadati necessari) viene quindi crittografato con AES-GCM. Ciò impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto. 

* **Passaggio 3 - Tutte le comunicazioni avvengono su TLS/SSL**: tutte le comunicazioni con il bus di servizio avvengono inoltre in un canale SSL/TLS. Ciò consente di proteggere i contenuti da terze parti non autorizzate.

* **Passaggio 4 - Rollover automatico delle chiavi ogni 6 mesi**: infine, automaticamente ogni 6 mesi o ogni volta che il writeback delle password viene disabilitato e abilitato di nuovo in Azure AD Connect, viene eseguito il rollover di tutte le chiavi per garantire i massimi livelli di sicurezza e protezione del servizio.

### <a name="password-writeback-bandwidth-usage"></a>Utilizzo della larghezza di banda per il writeback delle password

Il writeback delle password è un servizio a larghezza di banda estremamente ridotta, che invia le richieste all'agente locale solo nelle circostanze seguenti:

1. Due messaggi vengono inviati durante l'abilitazione o disabilitazione della funzionalità tramite Azure AD Connect.
2. Un messaggio viene inviato una volta ogni 5 minuti come heartbeat del servizio per la durata dell'esecuzione del servizio.
3. Due messaggi vengono inviati ogni volta che viene inviata una nuova password, un messaggio come richiesta di eseguire l'operazione e un messaggio successivo che contiene il risultato dell'operazione. Questi messaggi vengono inviati nelle circostanze seguenti.
4. Ogni volta che viene inviata una nuova password durante la reimpostazione self-service della password utente.
5. Ogni volta che viene inviata una nuova password durante un'operazione di modifica della password utente.
6. Ogni volta che viene inviata una nuova password durante una reimpostazione della password utente avviata dall'amministratore (solo dal portale di amministrazione di Azure)

#### <a name="message-size-and-bandwidth-considerations"></a>Considerazioni sulle dimensioni dei messaggi e sulla larghezza di banda

Le dimensioni di ogni messaggio illustrato in precedenza sono in genere inferiori a 1 KB. Anche in caso di carichi estremi, quindi, il writeback delle password stesso utilizzerà al massimo qualche kilobit di larghezza di banda al secondo. Poiché ogni messaggio viene inviato in tempo reale, solo quando necessario per un'operazione di aggiornamento della password, e poiché le dimensioni dei messaggi sono così ridotte, l'utilizzo della larghezza di banda da parte della funzionalità di writeback è effettivamente troppo basso per avere un impatto reale significativo.

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>Distribuzione e gestione dei dati di reimpostazione della password per gli utenti e accesso a tali dati
È possibile gestire i dati di reimpostazione della password per gli utenti e accedere a tali dati tramite Azure AD Connect, PowerShell, Graph o le esperienze di registrazione.  È anche possibile distribuire la reimpostazione della password all'intera organizzazione senza richiedere la registrazione da parte degli utenti, usando le opzioni descritte di seguito.

  * [Dati usati per la reimpostazione della password](#what-data-is-used-by-password-reset)
  * [Distribuzione della reimpostazione della password senza richiedere la registrazione dell'utente finale](#deploying-password-reset-without-requiring-end-user-registration)
  * [Cosa accade quando un utente si registra per la reimpostazione della password?](#what-happens-when-a-user-registers)
  * [Come accedere ai dati di reimpostazione della password per gli utenti](#how-to-access-password-reset-data-for-your-users)
  * [Impostazione dei dati di reimpostazione della password con PowerShell](#setting-password-reset-data-with-powershell)
  * [Lettura dei dati di reimpostazione della password con PowerShell](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>Dati usati per la reimpostazione della password
La seguente tabella indica il punto e la modalità d'uso dei dati durante la reimpostazione della password e consente di decidere quali opzioni di autenticazione sono appropriate per l'organizzazione. La tabella illustra inoltre i requisiti di formattazione per i casi in cui vengono forniti dati per conto di utenti da percorsi di input che non prevedono la convalida dei dati.

> [!NOTE]
> Il metodo di contatto basato sul telefono dell'ufficio non viene visualizzato nel portale di registrazione perché gli utenti non sono attualmente in grado di modificare questa proprietà nella directory. Questo valore può essere impostato solo dagli amministratori.
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome del metodo di contatto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento dati di Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento dati di Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Punto di uso e impostazione</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisiti relativi al formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefono ufficio</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>Questa proprietà può essere sincronizzata con l'attributo PhoneNumber in Azure Active Directory e usata immediatamente per la reimpostazione della password SENZA richiedere che l'utente esegua prima la registrazione.</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>ad esempio: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usato in:</p>
              <p>Portale di reimpostazione delle password</p>
              <p>Impostabile in:</p>
              <p>PhoneNumber è impostabile in PowerShell, DirSync, nel portale di gestione di Azure e nel portale di amministrazione di Office</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (ad esempio +1 1234567890)</p>
              <ul>
                <li class="unordered">
Deve includere un codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere un prefisso (dove applicabile).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere un segno + prima del codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere uno spazio tra il codice paese e il resto del numero.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Gli interni non sono supportati; gli eventuali interni specificati verranno cancellati dal numero prima di effettuare la chiamata.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Cellulare</p>
            </td>
            <td>
              <p>Mobile</p>
              <p>Questa proprietà può essere sincronizzata con l'attributo MobilePhone in Azure Active Directory e usata immediatamente per la reimpostazione della password SENZA richiedere che l'utente esegua prima la registrazione.</p>
              <p>Al momento, non è possibile sincronizzare questa proprietà con AuthenticationPhone.</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OPPURE</p>
              <p>MobilePhone</p>
              <p>Il campo Telefono per l'autenticazione viene usato se sono presenti dati, in caso contrario viene usato il campo relativo al telefono cellulare.</p>
              <p>ad esempio: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Usato in:</p>
              <p>Portale di reimpostazione delle password</p>
              <p>Portale di registrazione</p>
              <p>Impostabile in: </p>
              <p>AuthenticationPhone è impostabile nel portale di registrazione per la reimpostazione della password o nel portale di registrazione per l'autenticazione a più fattori.</p>
              <p>È possibile impostare MobilePhone da PowerShell, da Azure AD Connect, dal portale di gestione di Azure e dal portale di amministrazione di Office</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (ad esempio +1 1234567890)</p>
              <ul>
                <li class="unordered">
Deve includere un codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere un prefisso (dove applicabile).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere un segno + prima del codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve includere uno spazio tra il codice paese e il resto del numero.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Gli interni non sono supportati; gli eventuali interni specificati verranno ignorati quando viene effettuata la chiamata.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Indirizzo di posta elettronica alternativo</p>
            </td>
            <td>
              <p>Non disponibile</p>
              <p>Al momento, non è possibile sincronizzare i valori da Active Directory alla proprietà AuthenticationEmail o AlternateEmailAddresses[0]. </p>
              <p>È possibile usare PowerShell per impostare AlternateEmailAddresses[0]. Le istruzioni per questa operazione sono disponibili nella sezione sotto questa tabella.</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OPPURE</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>Il campo Indirizzo di posta elettronica per l'autenticazione viene usato se sono presenti dati, in caso contrario viene usato il campo Indirizzo di posta elettronica alternativo.</p>
              <p>Nota: il campo Indirizzo di posta elettronica alternativo è specificato come una matrice di stringhe nella directory.  In questa matrice viene usata la prima voce.</p>
              <p>Un esempio è Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>Usato in:</p>
              <p>Portale di reimpostazione delle password</p>
              <p>Portale di registrazione</p>
              <p>Impostabile in: </p>
              <p>AuthenticationEmail è impostabile nel portale di registrazione per la reimpostazione della password o nel portale di registrazione per l'autenticazione a più fattori.</p>
              <p>AlternateEmail è impostabile in PowerShell, nel portale di gestione di Azure e nel portale di amministrazione di Office.</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> o 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
I messaggi di posta elettronica devono seguire la formattazione standard prevista.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Gli indirizzi di posta elettronica Unicode sono supportati.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Domande di sicurezza e risposte</p>
            </td>
            <td>
              <p>Non disponibile</p>
              <p>Al momento, non è possibile sincronizzare le domande o le risposte di sicurezza da Active Directory.</p>
            </td>
            <td>
              <p>Non disponibili per la modifica diretta nella directory.</p>
              <p>L'impostazione è possibile solo durante il processo di registrazione utente finale per la reimpostazione della password.</p>
            </td>
            <td>
              <p>Usato in:</p>
              <p>Portale di reimpostazione delle password</p>
              <p>Portale di registrazione </p>
              <p>Impostabile in: </p>
              <p>Le domande di sicurezza possono essere impostate solo nel portale di gestione di Azure.</p>
              <p>Le risposte alle domande di sicurezza per un determinato utente possono essere impostate solo nel portale di registrazione.</p>
            </td>
            <td>
              <p>Le domande di sicurezza possono essere costituite da un numero di caratteri compreso tra 3 e 200.</p>
              <p>Le risposte di sicurezza possono essere costituite da un numero di caratteri compreso tra 3 e 40.</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>Distribuzione della reimpostazione della password senza richiedere la registrazione dell'utente finale
Se si vuole distribuire la reimpostazione della password senza richiedere agli utenti di eseguire la registrazione, è possibile farlo in modo semplice attenendosi a una delle due opzioni seguenti. Ciò può risultare utile per sbloccare l'uso di SSPR da parte di un numero elevato di utenti pur consentendo loro di convalidare tali informazioni tramite il processo di registrazione.

Molti dei più importanti clienti Microsoft attualmente scelgono queste opzioni per iniziare a usare rapidamente la reimpostazione della password.

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>Sincronizzare i numeri di telefono con Azure AD Connect
Se si sincronizzano i dati con uno o entrambi i campi seguenti, è possibile usarli immediatamente per la reimpostazione della password, senza richiedere agli utenti di eseguire prima la registrazione:

* Cellulare
* Telefono ufficio

Per informazioni sulle proprietà che devono essere aggiornate in locale, vedere la sezione [Dati usati per la reimpostazione della password](#what-data-is-used-by-password-reset) e cercare i campi indicati sopra.  

Assicurarsi che tutti i numeri di telefono siano nel formato "+ 1 1234567890" per poter funzionare correttamente con il sistema.

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>Impostare numeri di telefono o indirizzi di posta elettronica con PowerShell
Se si impostano uno o più dei campi seguenti, è possibile usarli immediatamente per la reimpostazione della password, senza richiedere agli utenti di eseguire prima la registrazione:

* Cellulare
* Telefono ufficio
* Indirizzo di posta elettronica alternativo

Per informazioni su come impostare queste proprietà con PowerShell, vedere la sezione [Impostazione dei dati di reimpostazione della password con PowerShell](#setting-password-reset-data-with-powershell).

Assicurarsi che tutti i numeri di telefono siano nel formato "+ 1 1234567890" per poter funzionare correttamente con il sistema.

### <a name="what-happens-when-a-user-registers"></a>Cosa accade quando un utente si registra?
Quando un utente si registra, i campi seguenti verranno **sempre** impostati nella pagina di registrazione:

* Telefono per l'autenticazione
* Indirizzo di posta elettronica per l'autenticazione
* Domande di sicurezza e risposte

Se è stato specificato un valore per **Cellulare** o **Indirizzo di posta elettronica alternativo**, gli utenti possono usare immediatamente queste informazioni per reimpostare le password, anche se non hanno effettuato la registrazione per il servizio.  Inoltre, gli utenti visualizzano (e possono modificare) tali valori quando si registrano per la prima volta.  Tuttavia, dopo aver completato la registrazione, questi valori vengono salvati in modo permanente rispettivamente nei campi **Telefono per l'autenticazione** e **Indirizzo di posta elettronica per l'autenticazione**.

### <a name="how-to-access-password-reset-data-for-your-users"></a>Come accedere ai dati di reimpostazione della password per gli utenti
#### <a name="data-settable-via-synchronization"></a>Dati impostabili tramite la sincronizzazione
I campi seguenti possono essere sincronizzati in locale:

* Cellulare
* Telefono ufficio

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>Dati impostabili con Azure AD PowerShell e Azure AD Graph
I campi seguenti possono essere impostati usando Azure AD PowerShell e l'API Graph di Azure AD:

* Indirizzo di posta elettronica alternativo
* Cellulare
* Telefono ufficio

#### <a name="data-settable-with-registration-ui-only"></a>Dati impostabili solo con l'interfaccia utente della registrazione 
I campi seguenti sono accessibili solo tramite l'interfaccia utente della registrazione SSPR (https://aka.ms/ssprsetup):

* Domande di sicurezza e risposte

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>Dati leggibili con Azure AD PowerShell e Azure AD Graph
I campi seguenti sono accessibili con Azure AD PowerShell e l'API Graph di Azure AD:

* Indirizzo di posta elettronica alternativo
* Cellulare
* Telefono ufficio
* Telefono per l'autenticazione
* Indirizzo di posta elettronica per l'autenticazione

### <a name="setting-password-reset-data-with-powershell"></a>Impostazione dei dati di reimpostazione della password con PowerShell
È possibile impostare i valori dei campi seguenti con Azure AD PowerShell.

* Indirizzo di posta elettronica alternativo
* Cellulare
* Telefono ufficio

**_PowerShell V1_**

Per iniziare, è prima necessario [scaricare e installare il modulo di Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Al termine dell'installazione è possibile eseguire la procedura seguente per configurare ogni campo.

**_PowerShell V2_**

Per iniziare, è prima necessario [scaricare e installare il modulo di Azure AD V2 PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Al termine dell'installazione è possibile eseguire la procedura seguente per configurare ogni campo.

Per eseguire rapidamente l'installazione da versioni recenti di PowerShell che supportano Install-Module, eseguire questi comandi (la prima riga verifica semplicemente se l'installazione è già stata eseguita):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>Indirizzo di posta elettronica alternativo - Come impostare l'indirizzo di posta elettronica alternativo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>Cellulare - Come impostare il cellulare con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>Telefono ufficio - Come impostare il telefono dell'ufficio con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>Lettura dei dati di reimpostazione della password con PowerShell
È possibile leggere i valori dei campi seguenti con Azure AD PowerShell.

* Indirizzo di posta elettronica alternativo
* Cellulare
* Telefono ufficio
* Telefono per l'autenticazione
* Indirizzo di posta elettronica per l'autenticazione

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>Indirizzo di posta elettronica alternativo - Come leggere l'indirizzo di posta elettronica alternativo con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>Cellulare - Come leggere il cellulare con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell v2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>Telefono ufficio - Come leggere il telefono dell'ufficio con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>Telefono per l'autenticazione - Come leggere il telefono per l'autenticazione con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>Indirizzo di posta elettronica per l'autenticazione - Come leggere l'indirizzo di posta elettronica per l'autenticazione con PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>Come funziona la reimpostazione della password per gli utenti B2B?
La modifica e la reimpostazione della password sono completamente supportate con tutte le configurazioni B2B.  Leggere di seguito per informazioni sui tre casi B2B espliciti supportati dalla reimpostazione della password.

1. **Utenti di un'organizzazione partner con un tenant di Azure AD esistente**: se l'organizzazione partner ha un tenant di Azure AD esistente, verranno **rispettati i criteri di reimpostazione della password abilitati in tale tenant**. Affinché la reimpostazione della password funzioni, l'organizzazione partner deve semplicemente assicurarsi che sia abilitata la reimpostazione della password in modalità self-service di AD Azure, senza alcun costo aggiuntivo per i clienti di Office&365;. Per l'abilitazione, seguire i passaggi nella guida [Introduzione alla gestione delle password](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Utenti che hanno usato l'[iscrizione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)**: se l'organizzazione partner ha usato la funzionalità di [iscrizione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup) per accedere a un tenant, gli utenti avranno a disposizione la reimpostazione con l'indirizzo di posta elettronica registrato.
3. **Utenti B2B**: tutti i nuovi utenti B2B creati usando le nuove [funzionalità B2B di Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) potranno anche reimpostare le password con l'indirizzo di posta elettronica registrato durante il processo di invito.
 
Per testare queste opzioni, passare a http://passwordreset.microsoftonline.com con uno di questi utenti partner.  Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funzionerà come previsto.  Altre informazioni sui dati usati per la reimpostazione della password in modalità self-service sono disponibili nella panoramica in [Dati usati per la reimpostazione della password](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset).

## <a name="next-steps"></a>Passaggi successivi
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md) : risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

