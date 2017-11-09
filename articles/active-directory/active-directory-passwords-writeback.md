---
title: Reimpostazione delle password self-service di Azure Ad con writeback delle password | Microsoft Docs
description: Uso di Azure AD e Azure AD Connect per eseguire il writeback delle password nella directory locale
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione della password self-service di Azure AD
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8ce4d6d9024dc4ce3956220eb0678a6295b0b7ab
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="password-writeback-overview"></a>Panoramica del writeback delle password

Il writeback delle password consente di configurare Azure AD per scrivere le password nuovamente in Active Directory locale. Evita di dover configurare e gestire una complessa soluzione di reimpostazione della password self-service locale e offre agli utenti un modo pratico e basato sul cloud per reimpostare le password locali ovunque essi si trovino. Il writeback delle password è un componente di [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) che può essere abilitato e usato dagli attuali sottoscrittori di [Edizioni di Azure Active Directory](active-directory-editions.md) Premium.

Il writeback delle password offre le funzionalità seguenti:

* **Nessun ritardo nei feedback**: il writeback delle password è un'operazione sincrona. Gli utenti vengono informati immediatamente se la password non soddisfa i criteri o non può essere reimpostata o modificata per qualsiasi motivo.
* **Supporta la reimpostazione delle password per gli utenti che usano AD FS o altre tecnologia federate**: con il writeback delle password gli account utente federati potranno gestire le proprie password AD locali dal cloud, purché siano sincronizzati nel tenant di Azure AD.
* **Supporta la reimpostazione delle password per gli utenti che usano la [sincronizzazione degli hash delle password](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)**: quando il servizio di reimpostazione delle password rileva che un account utente sincronizzato è abilitato per la sincronizzazione degli hash delle password, la password locale e la password cloud dell'account vengono reimpostate simultaneamente.
* **Supporta la modifica delle password dal pannello di accesso e da Office 365**: quando gli utenti federati o sincronizzati con password modificano le password scadute o non scadute, queste verranno scritte anche nell'ambiente AD locale.
* **Supporta le writeback delle password quando un amministratore le reimposta dal portale di Azure**: quando un amministratore reimposta la password di un utente nel [portale di Azure](https://portal.azure.com), se tale utente è federato o sincronizzato con password, la password selezionata dall'amministratore verrà impostata anche nell'ambiente AD locale. Ciò non è attualmente supportato nel portale di amministrazione di Office.
* **Applica i criteri delle password AD locali**: quando un utente reimposta la propria password, verificare che soddisfi i criteri AD locali prima di eseguirne il commit nella directory. Sono inclusi cronologia, complessità, validità, filtri delle password e altre restrizioni per le password definite in AD locale.
* **Non richiede alcuna regola del firewall in ingresso**: per il writeback delle password viene usato Inoltro del bus di servizio di Azure come canale di comunicazione sottostante. In questo modo non è necessario aprire porte in ingresso nel firewall per usare questa funzionalità.
* **Non è supportato per gli account utente esistenti all'interno di gruppi protetti in Active Directory locale**: per altre informazioni sui gruppi protetti, vedere [Account protetti e gruppi in Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Funzionamento del writeback delle password

Quando un utente federato e con sincronizzazione di hash della password reimposta o modifica la propria password nel cloud, si verifica quanto segue:

1. Viene verificato il tipo di password usato dall'utente.
    * Se risulta che la password è gestita in locale
        * Si verifica che il servizio di writeback sia attivo e in esecuzione, in questo caso, viene consentito all'utente di continuare
        * Se il servizio di writeback non è attivo, l'utente viene informato che la password non può essere reimpostata in quel momento
2. L'utente supera quindi i controlli di autenticazione appropriati e raggiunge la schermata Reimposta password.
3. L'utente seleziona una nuova password e la conferma.
4. Facendo clic su Invia, la password non crittografata viene crittografata con una chiave simmetrica creata durante il processo di installazione della funzionalità di writeback.
5. Dopo essere stata crittografata, la password viene inclusa in un payload inviato tramite un canale HTTPS al servizio Inoltro del bus di servizio specifico del tenant, che viene configurato automaticamente durante il processo di installazione della funzionalità di writeback. L'inoltro è protetto da una password generata casualmente, nota solo all'installazione locale.
6. Dopo che il messaggio avrà raggiunto il bus di servizio, l'endpoint di reimpostazione della password si attiva automaticamente e rileva la richiesta di reimpostazione in sospeso.
7. Il servizio cerca quindi l'utente in questione usando l'attributo di ancoraggio cloud. Affinché questa ricerca abbia esito positivo:

    * L'oggetto utente deve esistere nello spazio connettore AD
    * L'oggetto utente deve essere collegato all'oggetto MV corrispondente
    * L'oggetto utente deve essere collegato all'oggetto connettore AAD corrispondente.
    * Il collegamento dall'oggetto connettore AD a MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento. <br> <br>
    Alla ricezione della chiamata dal cloud, il motore di sincronizzazione usa l'attributo cloudAnchor per cercare l'oggetto spazio connettore AAD, quindi segue a ritroso il collegamento all'oggetto MV e all'oggetto AD. Poiché è possibile che siano presenti più oggetti AD (più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul collegamento `Microsoft.InfromADUserAccountEnabled.xxx` per scegliere quello corretto.

    > [!Note]
    > In seguito a questa logica, Azure AD Connect deve essere in grado di comunicare con l'emulatore PDC affinché il writeback delle password funzioni. Se è necessario abilitare questa funzione manualmente, è possibile collegare Azure AD Connect all'emulatore PDC facendo clic con il pulsante destro del mouse sulle **proprietà** del connettore di sincronizzazione di Active Directory e quindi scegliendo di **configurare le partizioni di directory**. Cercare quindi la sezione **domain controller connection settings** (impostazioni di connessione controller di dominio) e selezionare la casella **only use preferred domain controllers** (usa solo controller di dominio preferiti). Anche se il controller di dominio preferito non è un emulatore PDC, Azure AD Connect continuerà a tentare una connessione al PD per il writeback delle password.

8. Dopo aver trovato l'account utente, viene tentata la reimpostazione della password direttamente nella foresta Active Directory appropriata.
9. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata.
    > [!NOTE]
    > Se la password dell'utente viene sincronizzata con Azure AD usando la sincronizzazione password, è probabile che i criteri della password locali siano più deboli rispetto ai criteri della password cloud. In questo caso, vengono imposti i criteri locali qualsiasi essi siano, consentendo così alla sincronizzazione dell'hash delle password di sincronizzarsi con l'hash di tale password. Si garantisce in questo modo che i criteri locali siano applicati nel cloud, indipendentemente dal fatto che si usi la sincronizzazione password o la federazione per il Single Sign-On.

10. Se l'operazione di impostazione della password non riesce, l'errore viene restituito all'utente, consentendogli di riprovare.
    * L'operazione potrebbe non riuscire per i motivi seguenti
        * Il servizio è inattivo
        * La password selezionata non soddisfa i criteri dell'organizzazione
        * Non è possibile trovare l'utente in AD locale

    È disponibile un messaggio specifico per molti di questi casi e all'utente viene indicato cosa può fare per risolvere il problema.

## <a name="configuring-password-writeback"></a>Configurazione del writeback delle password

È consigliabile usare la funzionalità di aggiornamento automatico di [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se si vuole usare il writeback delle password.

Gli strumenti DirSync e Azure AD Sync non sono più supportati per l'attivazione del writeback delle password. L'articolo [Aggiornamento da DirSync e Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) contiene altre informazioni per facilitare la transizione.

La procedura seguente presuppone che Azure AD Connect sia già stato configurato nell'ambiente tramite le impostazioni [Rapida](./connect/active-directory-aadconnect-get-started-express.md) o [Personalizzata](./connect/active-directory-aadconnect-get-started-custom.md).

1. Per configurare e abilitare il writeback delle password accedere al server Azure AD Connect e avviare la configurazione guidata di **Azure AD Connect**.
2. Nella schermata iniziale fare clic su **Configura**.
3. Nella schermata Attività aggiuntive fare clic su **Personalizzazione delle opzioni di sincronizzazione** e quindi scegliere **Avanti**.
4. Nella schermata Connessione ad Azure AD immettere le credenziali di amministratore globale e scegliere **Avanti**.
5. Nelle schermate Connessione delle directory e Filtro di domini e unità organizzative è possibile scegliere **Avanti**.
6. Nella schermata Funzionalità facoltative selezionare la casella accanto a **Writeback password** e fare clic su **Avanti**.
   ![Abilitare il writeback delle password in Azure AD Connect][Writeback]
7. Nella schermata Pronto per la configurazione fare clic su **Configura** e attendere il completamento del processo.
8. Quando viene visualizzato il messaggio "La configurazione è stata completata" fare clic su **Esci**

## <a name="active-directory-permissions"></a>Autorizzazioni di Active Directory

L'account specificato nell'utilità di Azure AD Connect deve avere i diritti estesi per le opzioni Reimposta password, Modifica password, Autorizzazioni di scrittura per lockoutTime e Autorizzazioni di scrittura per pwdLastSet, per uno oggetto radice di **ogni dominio** nella foresta **O** nelle Unità organizzative dell'utente che si desidera avere nell'ambito per la reimpostazione della password self-service.

Se non si è certi dell'identità dell'account al quale si fa riferimento, aprire l'interfaccia utente della configurazione di Azure Active Directory Connect e fare clic sull'opzione Visualizza configurazione corrente. L'account a cui è necessario aggiungere le autorizzazioni è elencato in "Directory sincronizzate"

L'impostazione di queste autorizzazioni consente all'account del servizio MA per ogni foresta di gestire le password per conto di account utente all'interno di tale foresta. **Se non si assegnano tali autorizzazioni, anche se il writeback è configurato correttamente, gli utenti visualizzeranno errori durante il tentativo di gestione delle password locali dal cloud.**

> [!NOTE]
> La replica delle autorizzazioni in tutti gli oggetti nella directory può richiedere fino a un'ora o anche più tempo.
>

Per impostare le autorizzazioni appropriate per l'esecuzione del writeback delle password

1. Aprire Utenti e computer di Active Directory con un account con le autorizzazioni appropriate per l'amministrazione del dominio
2. Nel menu Visualizza verificare che l'opzione Funzionalità avanzate sia attivata
3. Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'oggetto che rappresenta la radice del dominio e scegliere le proprietà
    * Fare clic sulla scheda Sicurezza
    * Fare clic su Opzioni avanzate.
4. Fare clic su Aggiungi nella scheda Autorizzazioni
5. Selezionare l'account a cui applicare le autorizzazioni, dalla configurazione di Azure AD Connect
6. Nell'elenco a discesa Applica a selezionare gli oggetti Descendent User objects (Utente discendente)
7. Tra le autorizzazioni selezionare le caselle relative a quanto segue
    * Password senza scadenza
    * Reimpostare la password
    * Modifica della password
    * Scrittura di lockoutTime
    * Scrittura di pwdLastSet
8. Fare clic su Applica/OK per applicare e chiudere le finestre di dialogo aperte.

## <a name="licensing-requirements-for-password-writeback"></a>Requisiti di licenza per il writeback delle password

Per informazioni sulle licenze, vedere [Licenze richieste per il writeback delle password](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) o i siti seguenti

* [Sito sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

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
  * Qualsiasi operazione self-service forzata di modifica della password dell'utente finale, ad esempio in seguito a scadenza della password
  * Qualsiasi reimpostazione della password self-service dell'utente finale originata dal [portale di reimpostazione della password](https://passwordreset.microsoftonline.com)
* **Operazioni degli amministratori supportate**
  * Qualsiasi operazione self-service volontaria di modifica della password dell'amministratore
  * Qualsiasi operazione self-service forzata di modifica della password dell'amministratore, ad esempio in seguito a scadenza della password
  * Qualsiasi reimpostazione della password self-service dell'amministratore originata dal [portale di reimpostazione della password](https://passwordreset.microsoftonline.com)
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di Azure classico](https://manage.windowsazure.com)
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Attività di utenti e amministratori non supportate per il writeback delle password

Il writeback delle password **non** viene eseguito nelle situazioni seguenti:

* **Attività degli utenti finali non supportate**
  * Qualsiasi reimpostazione, da parte dell'utente finale, della propria password tramite PowerShell v1, v2 o l'API Graph di Azure AD
* **Operazioni degli amministratori non supportate**
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal [portale di gestione di Office](https://portal.office.com)
  * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell v1, v2 o dall'API Graph di Azure AD

Microsoft sta lavorando per rimuovere queste limitazioni, ma non è ancora disponibile una sequenza temporale specifica.

## <a name="password-writeback-security-model"></a>Modello di sicurezza del writeback delle password

Il writeback delle password è un servizio altamente sicuro.  Per garantire che le informazioni siano protette, viene abilitato un modello di protezione a 4 livelli descritto di seguito.

* **Inoltro del bus di servizio specifico del tenant**
  * Quando si configura il servizio, viene configurato automaticamente l'inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password**
  * Dopo la creazione di Inoltro del bus di servizio, viene creata una chiave asimmetrica complessa, usata per la crittografia della password durante la trasmissione. Questa chiave si trova solo nell'archivio segreto dell’azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi password nella directory.
* **TLS standard di settore**
  1. Quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata automaticamente con la chiave pubblica dell'utente.
  2. Viene quindi inclusa in un messaggio HTTPS inviato tramite un canale crittografato con certificati SSL Microsoft a Inoltro del bus di servizio.
  3. Quando il messaggio arriva nel Bus di servizio, l'agente locale viene attivato e autenticato al Bus di servizio tramite la password complessa generata in precedenza.
  4. L'agente locale preleva il messaggio crittografato, lo decrittografa con la chiave privata generata.
  5. L'agente locale tenta di impostare la password tramite l'API SetPassword di AD DS.
     * Questo passaggio consente di applicare i criteri password locali di AD, che includono complessità, validità, cronologia, filtri e così via, nel cloud.
* **Criteri di scadenza del messaggio** 
  * Se il messaggio si trova nel Bus di servizio perché il servizio locale è inattivo, raggiungerà il timeout e verrà rimosso dopo alcuni minuti per aumentare ulteriormente la protezione.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password

Di seguito vengono descritti i passaggi di crittografia seguiti per una richiesta di reimpostazione della password dopo l'invio da parte dell'utente, prima di raggiungere l'ambiente locale, per garantire massimi livelli di sicurezza e affidabilità del servizio.

* **Passaggio 1 - Crittografia della password con una chiave RSA a 2048 bit**: quando un utente invia una password per il writeback in locale, prima di tutto la password inviata viene crittografata con una chiave RSA a 2048 bit.
* **Passaggio 2 - Crittografia a livello di pacchetto con AES-GCM**: l'intero pacchetto (password + metadati necessari) viene quindi crittografato con AES-GCM. Questa crittografia impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto.
* **Passaggio 3 - Tutte le comunicazioni avvengono tramite TLS/SSL**: tutte le comunicazioni con ServiceBus avvengono in un canale SSL/TLS. Questa crittografia protegge i contenuti da terze parti non autorizzate.
* **Rollover automatico delle chiavi ogni 6 mesi**: ogni 6 mesi o ogni volta che il writeback delle password viene disabilitato e di nuovo abilitato in Azure AD Connect, viene eseguito automaticamente il rollover di tutte le chiavi per garantire i massimi livelli di sicurezza e protezione del servizio.

### <a name="password-writeback-bandwidth-usage"></a>Utilizzo della larghezza di banda per il writeback delle password

Il writeback delle password è un servizio a larghezza di banda ridotta, che invia le richieste all'agente locale solo nelle circostanze seguenti:

1. Due messaggi vengono inviati durante l'abilitazione o disabilitazione della funzionalità tramite Azure AD Connect.
2. Viene inviato un messaggio viene ogni 5 minuti come heartbeat del servizio per la durata dell'esecuzione del servizio.
3. Vengono inviati due messaggi ogni volta che viene inviata una nuova password
    * Il primo messaggio, come richiesta per eseguire l'operazione
    * Il secondo messaggio, che contiene il risultato dell'operazione e viene inviato nelle circostanze seguenti:
        * Ogni volta che viene inviata una nuova password durante la reimpostazione self-service della password utente.
        * Ogni volta che viene inviata una nuova password durante un'operazione di modifica della password utente.
        * Ogni volta che viene inviata una nuova password durante una reimpostazione della password utente avviata dall'amministratore, solo dal portale di amministrazione di Azure.

#### <a name="message-size-and-bandwidth-considerations"></a>Considerazioni sulle dimensioni dei messaggi e sulla larghezza di banda

Le dimensioni di ogni messaggio illustrato in precedenza sono in genere inferiori a 1 KB. Anche in caso di carichi estremi, il servizio di writeback delle password stesso usa qualche kilobit di larghezza di banda al secondo. Poiché ogni messaggio viene inviato in tempo reale, solo quando necessario per un'operazione di aggiornamento della password, e poiché le dimensioni dei messaggi sono così ridotte, l'utilizzo della larghezza di banda da parte della funzionalità di writeback è effettivamente troppo basso per avere un impatto reale significativo.

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
