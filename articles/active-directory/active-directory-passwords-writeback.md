---
title: Reimpostazione delle password self-service di Azure Ad con writeback delle password | Microsoft Docs
description: Usare Azure AD e Azure AD Connect per eseguire il writeback delle password in una directory locale
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione password self-service di Azure AD
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a7a81f51e0873da38f25deb687e4d03ccf67e11a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="password-writeback-overview"></a>Panoramica del writeback delle password

Con il writeback delle password è possibile configurare Azure Active Directory (Azure AD) in modo che scriva le password nuovamente in Active Directory locale. Il writeback delle password evita di dover configurare e gestire una complessa soluzione di reimpostazione password self-service (SSPR) locale e offre agli utenti un modo pratico e basato sul cloud per reimpostare le password locali ovunque essi si trovino. Il writeback delle password è un componente di [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) che può essere abilitato e usato dagli attuali sottoscrittori di [Edizioni di Azure Active Directory](active-directory-whatis.md) Premium.

Il writeback delle password offre le funzionalità seguenti:

* **Offre un feedback senza nessun ritardo**: il writeback delle password è un'operazione sincrona. Gli utenti vengono informati immediatamente se la password non soddisfa i criteri o non può essere reimpostata o modificata per qualsiasi motivo.
* **Supporta la reimpostazione password per gli utenti che usano Active Directory Federation Services (AD FS) o altre tecnologia federate**: con il writeback delle password gli account utente federati potranno gestire le proprie password AD locali dal cloud, purché siano sincronizzati nel tenant di Active Directory.
* **Supporta la reimpostazione password per gli utenti che usano** [la sincronizzazione degli hash delle password](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): quando il servizio di reimpostazione password rileva che un account utente sincronizzato è abilitato per la sincronizzazione degli hash delle password, la password locale e la password cloud dell'account vengono reimpostate simultaneamente.
* **Supporta la reimpostazione delle password per gli utenti che usano l'autenticazione pass-through**: con il writeback delle password gli account con autenticazione pass-through potranno gestire le password di Active Directory locali dal cloud purché siano sincronizzati nel tenant di Azure AD.
* **Supporta la modifica delle password dal pannello di accesso e da Office 365**: quando gli utenti federati o sincronizzati con password modificano le password scadute o non scadute, queste verranno scritte anche nell'ambiente Active Directory locale.
* **Supporta il writeback delle password quando un amministratore le reimposta dal portale di Azure**: quando un amministratore reimposta la password di un utente nel [portale di Azure](https://portal.azure.com), se tale utente è federato o sincronizzato con password, la password selezionata dall'amministratore verrà impostata anche nell'ambiente Active Directory locale. Questa funzionalità non è attualmente supportata nel portale di amministrazione di Office.
* **Applica i criteri delle password Active Directory locali**: quando un utente reimposta la propria password, si verifica che soddisfi i criteri Active Directory locali prima di eseguirne il commit nella directory. Questo esame include il controllo di cronologia, complessità, validità, filtri delle password e altre restrizioni per le password definite in Active Directory locale.
* **Non richiede regole del firewall in entrata**: il writeback delle password usa un bus di servizio di Azure come canale di comunicazione sottostante. Non è necessario aprire porte in ingresso nel firewall per poter usare questa funzionalità.
* **Non è supportata per gli account utente esistenti all'interno di gruppi protetti in Active Directory locale**: per altre informazioni sui gruppi protetti, vedere [Account protetti e gruppi in Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Funzionamento del writeback delle password

Quando un utente federato e con sincronizzazione di hash della password reimposta o modifica la propria password nel cloud, si verifica quanto segue:

1. Viene verificato il tipo di password usato dall'utente. Se risulta che la password è gestita in locale:
   * Si verifica che il servizio di writeback sia attivo e in esecuzione. Se è in esecuzione, viene consentito all'utente di continuare.
   * Se il servizio di writeback non è attivo, l'utente viene informato che la password non può essere reimpostata in quel momento.
2. L'utente supera quindi i controlli di autenticazione appropriati e raggiunge la pagina **Reimposta password**.
3. L'utente seleziona una nuova password e la conferma.
4. Quando l'utente seleziona **Invia**, la password non crittografata viene crittografata con una chiave simmetrica creata durante il processo di installazione della funzionalità di writeback.
5. Dopo essere stata crittografata, la password viene inclusa in un payload inviato tramite un canale HTTPS al servizio Inoltro del bus di servizio specifico del tenant, che viene configurato automaticamente durante il processo di installazione della funzionalità di writeback. L'inoltro è protetto da una password generata casualmente, nota solo all'installazione locale.
6. Dopo che il messaggio ha raggiunto il bus di servizio, l'endpoint di reimpostazione della password si attiva automaticamente e rileva la richiesta di reimpostazione in sospeso.
7. Il servizio cerca quindi l'utente usando l'attributo di ancoraggio cloud. Affinché questa ricerca abbia esito positivo:

    * L'oggetto utente deve esistere nello spazio connettore Active Directory.
    * L'oggetto utente deve essere collegato all'oggetto metaverse (MV) corrispondente.
    * L'oggetto utente deve essere collegato all'oggetto Azure Active Directory Connector corrispondente.
    * Il collegamento dall'oggetto connettore di Active Directory all'oggetto MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento. <br> <br>
    Quando la chiamata proviene dal cloud, il motore di sincronizzazione usa l'attributo **cloudAnchor** per cercare l'oggetto spazio connettore di Azure Active Directory. Quindi segue il collegamento all'oggetto MV e poi segue il collegamento che riporta all'oggetto Active Directory. Poiché possono essere presenti più oggetti Active Directory (più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul collegamento `Microsoft.InfromADUserAccountEnabled.xxx` per scegliere quello corretto.

    > [!Note]
    > Come risultato di questa logica, affinché il writeback delle password funzioni, Azure AD Connect deve essere in grado di comunicare con l'emulatore PDC . Se è necessario abilitare manualmente questa opzione, è possibile connettere Azure AD Connect all'emulatore PDC. Fare doppio clic sulle **proprietà** del connettore di sincronizzazione di Active Directory, quindi selezionare **configure directory partitions** (configura partizioni di directory). Cercare la sezione **domain controller connection settings** (impostazioni di connessione controller di dominio) e selezionare la casella **only use preferred domain controllers** (usa solo controller di dominio preferiti). Anche se il controller di dominio preferito non è un emulatore PDC, Azure AD Connect continua a tentare una connessione al PD per il writeback delle password.

8. Dopo che l'account utente è stato trovato, viene tentata la reimpostazione della password direttamente nella foresta Active Directory appropriata.
9. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata.
    > [!NOTE]
    > Se la password dell'utente viene sincronizzata con Azure AD usando la sincronizzazione password, è probabile che i criteri della password locali siano più deboli rispetto ai criteri della password cloud. In questo caso, vengono imposti i criteri locali qualsiasi essi siano, usando invece la sincronizzazione dell'hash delle password per sincronizzare l'hash di tale password. Questi criteri garantiscono che i criteri locali siano applicati nel cloud, indipendentemente dal fatto che si usi la sincronizzazione password o la federazione per il Single Sign-On.

10. Se l'operazione di impostazione della password non riesce, viene restituito un errore all'utente, consentendogli di riprovare. L'operazione potrebbe non riuscire perché:
    * Il servizio era inattivo.
    * La password selezionata non rispettava i criteri dell'organizzazione.
    * Si potrebbe non trovare l'utente in Active Directory locale.

    È disponibile un messaggio specifico per molti di questi casi e all'utente viene indicato cosa può fare per risolvere il problema.

## <a name="configure-password-writeback"></a>Configurare il writeback delle password

È consigliabile usare la funzionalità di aggiornamento automatico di [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se si vuole usare il writeback delle password.

DirSync e Azure AD Sync non sono più supportati come modo per abilitare il writeback delle password. Per altre informazioni utili sulla transizione, vedere [Aggiornamento da DirSync e Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

La procedura seguente presuppone che Azure AD Connect sia già stato configurato nell'ambiente tramite le impostazioni [Rapida](./connect/active-directory-aadconnect-get-started-express.md) o [Personalizzata](./connect/active-directory-aadconnect-get-started-custom.md).

1. Per configurare e abilitare il writeback delle password accedere al server Azure AD Connect e avviare la configurazione guidata di **Azure AD Connect**.
2. Nella pagina di **benvenuto** selezionare **Configura**.
3. Nella pagina **Attività aggiuntive** selezionare **Personalizzazione delle opzioni di sincronizzazione** e fare clic su **Avanti**.
4. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale e selezionare **Avanti**.
5. Nelle pagine di filtro **Connessione delle directory** e **Dominio/unità organizzativa** selezionare **Avanti**.
6. Nella pagina **Funzionalità facoltative** selezionare la casella accanto a **Writeback password** e selezionare **Avanti**.
   ![Abilitare il writeback delle password in Azure AD Connect][Writeback]
7. Nella pagina **Pronto per la configurazione** fare clic su **Configura** e attendere il completamento del processo.
8. Quando la configurazione termina, selezionare **Esci**.

Per le attività di risoluzione dei problemi comuni correlate al writeback delle password, vedere la sezione [Risolvere i problemi relativi al writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) nell'articolo sulla risoluzione dei problemi.

## <a name="active-directory-permissions"></a>Autorizzazioni di Active Directory

L'account specificato nell'utilità di Azure AD Connect deve avere i seguenti elementi impostati se si vuole essere nell'ambito per SSPR:

* **Reimpostazione della password** 
* **Cambia password** 
* **Autorizzazioni di scrittura** su `lockoutTime`  
* **Autorizzazioni di scrittura** su `pwdLastSet`
* **Diritti estesi** su uno fra:
   * L'oggetto radice di *ogni dominio* in tale foresta
   * Le unità organizzative (OU) utente che si vuole siano nell'ambito per SSPR

Se non si è certi di quale sia l'account a cui l'account descritto fa riferimento, aprire l'interfaccia utente della configurazione di Azure Active Directory Connect e selezionare l'opzione **Visualizza configurazione corrente**. L'account a cui è necessario aggiungere le autorizzazioni è elencato in **Directory sincronizzate**.

Se si impostano queste autorizzazioni, l'account del servizio MA per ogni foresta può gestire le password per conto di account utente all'interno di tale foresta. 

> [!IMPORTANT]
> Se non si assegnano tali autorizzazioni, anche se il writeback è configurato correttamente, gli utenti visualizzeranno errori durante il tentativo di gestione delle password locali dal cloud.
>

> [!NOTE]
> La replica delle autorizzazioni in tutti gli oggetti nella directory potrebbe richiedere fino a un'ora o anche più tempo.
>

Per impostare le autorizzazioni appropriate per l'esecuzione del writeback delle password, eseguire la procedura seguente:

1. Aprire Utenti e computer di Active Directory con un account con le autorizzazioni appropriate per l'amministrazione del dominio.
2. Nel menu **Visualizza** verificare che l'opzione **Funzionalità avanzate** sia attivata.
3. Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'oggetto che rappresenta la radice del dominio e selezionare **Proprietà** > **Sicurezza** > **Avanzate**.
4. Nella scheda **Autorizzazioni** selezionare **Aggiungi**.
5. Selezionare l'account a cui applicare le autorizzazioni, dalla configurazione di Azure AD Connect.
6. Nell'elenco a discesa **Applica a** selezionare gli oggetti **Descendent User** (Utente discendente).
7. Sotto **Autorizzazioni** selezionare le caselle per le opzioni seguenti:
    * **Reimpostazione della password**
    * **Cambia password**
    * **Scrittura di lockoutTime**
    * **Scrittura di pwdLastSet**
8. Selezionare **Applica/OK** per applicare le modifiche e chiudere le finestre di dialogo aperte.

## <a name="licensing-requirements-for-password-writeback"></a>Requisiti di licenza per il writeback delle password

Per informazioni sulle licenze, vedere [Licenze richieste per il writeback delle password](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) o i siti seguenti:

* [Sito sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Modalità di autenticazione locale supportate per il writeback delle password

Il writeback delle password supporta i tipi di password utente seguenti:

* **Utenti solo cloud**: il writeback delle password non si applica in questa situazione, perché non c'è una password locale.
* **Utenti con sincronizzazione delle password**: il writeback delle password è supportato.
* **Utenti federati**: il writeback delle password è supportato.
* **Utenti con autenticazione pass-through**: il writeback delle password è supportato.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Attività di utenti e amministratori supportate per il writeback delle password

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

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Attività di utenti e amministratori non supportate per il writeback delle password

Il writeback delle password *non* viene eseguito nelle situazioni seguenti:

* **Attività degli utenti finali non supportate**
  * Qualsiasi reimpostazione, da parte dell'utente finale, della propria password tramite PowerShell versione 1, versione 2 o l'API Graph di Azure AD
* **Operazioni degli amministratori non supportate**
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di gestione di Office](https://portal.office.com)
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell versione 1, versione 2 o dall'API Graph di Azure AD

Microsoft sta lavorando per rimuovere queste limitazioni, ma non è ancora disponibile una sequenza temporale specifica.

## <a name="password-writeback-security-model"></a>Modello di sicurezza del writeback delle password

Il writeback delle password è un servizio altamente sicuro. Per garantire che le informazioni siano protette, viene abilitato un modello di protezione a quattro livelli descritto così:

* **Inoltro del bus di servizio specifico del tenant**
  * Quando si configura il servizio, viene configurato automaticamente l'inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password**
  * Dopo la creazione dell'inoltro del bus di servizio, viene creata una chiave asimmetrica complessa, utilizzata per la crittografia della password durante la trasmissione. Questa chiave si trova solo nell'archivio segreto dell'azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi altra password nella directory.
* **Transport Layer Security (TLS) standard di settore**
  1. Quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata automaticamente con la chiave pubblica dell'utente.
  2. Viene inclusa in un messaggio HTTPS inviato tramite un canale crittografato mediante certificati SSL Microsoft al proprio inoltro del bus di servizio.
  3. Quando il messaggio arriva nel bus di servizio, l'agente locale viene attivato e autenticato al bus di servizio tramite la password complessa generata in precedenza.
  4. L'agente locale preleva il messaggio crittografato e lo decrittografa con la chiave privata generata.
  5. L'agente locale tenta di impostare la password tramite l'API SetPassword di AD DS. Questo passaggio consente di applicare i criteri password locali di Active Directory, come complessità, validità, cronologia, filtri e così via, nel cloud.
* **Criteri di scadenza del messaggio** 
  * Se il messaggio si trova nel bus di servizio perché il servizio locale è inattivo, raggiungerà il timeout e verrà rimosso dopo alcuni minuti. Il timeout e la rimozione del messaggio migliorano ulteriormente la sicurezza.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password

Dopo che un utente ha inviato una reimpostazione password, la richiesta di reimpostazione passa attraverso diverse operazioni di crittografia prima di arrivare nell'ambiente locale. Queste operazioni di crittografia garantiscono la massima affidabilità del servizio e sicurezza. Sono descritte come segue:

* **Passaggio 1 - Crittografia della password con una chiave RSA a 2048 bit**: dopo che un utente ha inviato una password per il writeback in locale, la password inviata viene crittografata con una chiave RSA a 2048 bit.
* **Passaggio 2 - Crittografia a livello di pacchetto con AES-GCM**: l'intero pacchetto, ovvero la password più i metadati necessari, viene crittografato con AES-GCM. Questa crittografia impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto.
* **Passaggio 3 - Tutte le comunicazioni avvengono tramite TLS/SSL**: tutte le comunicazioni con ServiceBus avvengono in un canale SSL/TLS. Questa crittografia protegge i contenuti da terze parti non autorizzate.
* **Rollover della chiave automatico ogni sei mesi**: ogni sei mesi o ogni volta che il writeback delle password viene disabilitato e poi riabilitato in Azure AD Connect. Il rollover di tutte le chiavi viene eseguito automaticamente per garantire la massima sicurezza e protezione del servizio.

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

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Abilitare il writeback delle password in Azure AD Connect"
