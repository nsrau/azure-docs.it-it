---
title: Writeback della password locale con reimpostazione della password self-service - Azure Active Directory
description: Informazioni su come è possibile riscrivere in un ambiente di directory locale gli eventi di modifica o reimpostazione della password in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393012"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Come funziona il writeback della reimpostazione della password self-service in Azure Active Directory?

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di reimpostare le password nel cloud, ma la maggior parte delle aziende dispone anche di un ambiente Servizi di dominio Active Directory in locale. Il writeback delle password è una funzione abilitata con [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) che consente di riscrivere le modifiche alle password nel cloud in una directory locale esistente in tempo reale. In questa configurazione, quando gli utenti modificano o reimpostano le password utilizzando SSPR nel cloud, le password aggiornate anche written back nell'ambiente di dominio Active Directory locale

Il writeback delle password è supportato negli ambienti che utilizzano i modelli di identità ibrida seguenti:Password writeback is supported in environments that use the following hybrid identity models:

* [Sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticazione pass-through](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Il writeback delle password offre le funzionalità seguenti:

* **Applicazione dei criteri**password di Servizi di dominio Active Directory locale: quando un utente reimposta la password, viene controllata per verificare che soddisfi i criteri di dominio Active Directory locali prima di eseguirne il commit in tale directory. Questa revisione include la verifica della cronologia, della complessità, dell'età, dei filtri password e di qualsiasi altra restrizione per le password definita in Servizi di dominio Active Directory.
* **Nessun ritardo nei feedback**: il writeback delle password è un'operazione sincrona. Gli utenti ricevono immediatamente una notifica se la password non soddisfa il criterio o non possono essere reimpostati o modificati per qualsiasi motivo.
* Supporta le modifiche delle password dal pannello di **accesso e Office 365:** quando gli utenti federati o sincronizzati con hash password vengono a modificare le password scadute o non scadute, tali password vengono riscritte in Servizi di dominio Active Directory.
* Supporta il writeback delle **password quando un amministratore le reimposta dal portale**di Azure: quando un amministratore reimposta la password di un utente nel portale di [Azure,](https://portal.azure.com)se l'utente è federato o la password è sincronizzata, la password viene riscritta in locale. Questa funzionalità non è attualmente supportata nel portale di amministrazione di Office.
* **Non richiede regole del firewall in ingresso:** il writeback delle password usa un inoltro del bus di servizio di Azure come canale di comunicazione sottostante. Tutte le comunicazioni sono in uscita tramite la porta 443.

> [!NOTE]
> Gli account amministratore presenti all'interno di gruppi protetti nell'istanza locale di AD possono essere usati con il writeback delle password. Gli amministratori possono modificare la password nel cloud, ma non possono utilizzare la reimpostazione della password per reimpostare una password dimenticata. Per altre informazioni sui gruppi protetti, vedere l'articolo relativo ad [account e gruppi protetti in Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

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
1. Il servizio cerca quindi l'utente usando l'attributo di ancoraggio cloud. Affinché questa ricerca abbia esito positivo, è necessario che siano soddisfatte le condizioni seguenti:For this lookup to succeed, the following conditions must be met:

   * L'oggetto utente deve esistere nello spazio connettore Active Directory.
   * L'oggetto utente deve essere collegato all'oggetto metaverse (MV) corrispondente.
   * L'oggetto utente deve essere collegato all'oggetto Azure Active Directory Connector corrispondente.
   * Il collegamento dall'oggetto connettore di Active Directory all'oggetto MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento.

   Quando la chiamata proviene dal cloud, il motore di sincronizzazione usa l'attributo **cloudAnchor** per cercare l'oggetto spazio connettore di Azure Active Directory. Quindi segue il collegamento all'oggetto MV e poi segue il collegamento che riporta all'oggetto Active Directory. Poiché possono essere presenti più oggetti Active Directory (più foreste) per lo stesso utente, il motore di sincronizzazione si basa sul collegamento `Microsoft.InfromADUserAccountEnabled.xxx` per scegliere quello corretto.

1. Dopo che l'account utente è stato trovato, viene tentata la reimpostazione della password direttamente nella foresta Active Directory appropriata.
1. Se l'operazione di impostazione della password riesce, l'utente viene informato che la password è stata modificata.

   > [!NOTE]
   > Se l'hash della password dell'utente viene sincronizzato con Azure AD tramite la sincronizzazione dell'hash delle password, è possibile che i criteri password locali siano più deboli dei criteri password cloud. In questo caso vengono applicati i criteri locali. Questi criteri garantiscono che i criteri locali siano applicati nel cloud, indipendentemente dal fatto che si usi la sincronizzazione dell'hash delle password o la federazione per il Single Sign-On.

1. Se l'operazione di impostazione della password non riesce, viene restituito un messaggio di errore con la richiesta all'utente di riprovare. L'operazione potrebbe non riuscire per i seguenti motivi:
    * Il servizio era inattivo.
    * La password selezionata non soddisfa i criteri dell'organizzazione.
    * Non è stato trovato l'utente in Azure Active Directory locale.

   I messaggi di errore forniscono indicazioni agli utenti in modo che possano tentare di risolvere il problema senza l'intervento dell'amministratore.

## <a name="password-writeback-security"></a>Sicurezza del writeback delle password

Il writeback delle password è un servizio altamente sicuro. Per garantire la protezione delle informazioni, viene abilitato un modello di sicurezza a quattro livelli nel modo seguente:To ensure your information is protected, a four-tiered security model is enabled as follows:

* **Inoltro del bus di servizio specifico del tenant**
   * Quando si configura il servizio, viene configurato l'inoltro del bus di servizio specifico del tenant, protetto da una password complessa generata in modo casuale, a cui Microsoft non può mai accedere.
* **Chiave di crittografia bloccata e crittograficamente complessa per le password**
   * Dopo aver creato l'inoltro del bus di servizio, viene creata una chiave simmetrica complessa che viene utilizzata per crittografare la password quando viene trasmessa in rete. Questa chiave si trova solo nell'archivio segreto dell'azienda nel cloud, bloccato in modo sicuro e controllato, come qualsiasi altra password nella directory.
* **Transport Layer Security (TLS) standard di settore**
   1. Quando si verifica nel cloud un'operazione di reimpostazione o modifica della password, la password non crittografata viene crittografata con la chiave pubblica dell'utente.
   1. La password crittografata viene inserita in un messaggio HTTPS inviato tramite un canale crittografato utilizzando i certificati Microsoft TLS/SSL all'inoltro del bus di servizio.
   1. Quando il messaggio arriva nel bus di servizio, l'agente locale viene attivato e autenticato al bus di servizio tramite la password complessa generata in precedenza.
   1. L'agente locale preleva il messaggio crittografato e lo decrittografa con la chiave privata.
   1. L'agente locale tenta di impostare la password tramite l'API SetPassword di AD DS. Questo passaggio consente di applicare i criteri password locali di Active Directory, come complessità, validità, cronologia, filtri e così via, nel cloud.
* **Criteri di scadenza del messaggio**
   * Se il messaggio si trova nel bus di servizio perché il servizio locale è inattivo, raggiungerà il timeout e verrà rimosso dopo alcuni minuti. Il timeout e la rimozione del messaggio migliorano ulteriormente la sicurezza.

### <a name="password-writeback-encryption-details"></a>Informazioni dettagliate sulla crittografia del writeback delle password

Dopo che un utente ha inviato una reimpostazione password, la richiesta di reimpostazione passa attraverso diverse operazioni di crittografia prima di arrivare nell'ambiente locale. Queste operazioni di crittografia garantiscono la massima affidabilità del servizio e sicurezza. Sono descritte come segue:

1. **Crittografia della password con chiave RSA a 2048 bit:** dopo che un utente invia una password da riscrivere in locale, la password inviata stessa viene crittografata con una chiave RSA a 2048 bit.
1. Crittografia a livello di **pacchetto con AES-GCM:** l'intero pacchetto, la password più i metadati richiesti, viene crittografato utilizzando AES-GCM. Questa crittografia impedisce a chiunque abbia accesso diretto al canale del bus di servizio sottostante di visualizzare o manomettere il contenuto.
1. **Tutte le comunicazioni avvengono tramite TLS/SSL**: Tutte le comunicazioni con ServiceBus avviene in un canale SSL/TLS. Questa crittografia protegge i contenuti da terze parti non autorizzate.
1. **Rollover automatico delle chiavi ogni 6 mesi**: ogni 6 mesi o ogni volta che il writeback delle password viene disabilitato e di nuovo abilitato in Azure AD Connect, viene eseguito automaticamente il rollover di tutte le chiavi per garantire i massimi livelli di sicurezza e protezione del servizio.

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
   * Qualsiasi operazione di modifica volontaria della password self-service dell'utente finale.
   * Qualsiasi operazione di modifica della password self-service dell'utente finale, ad esempio la scadenza della password.
   * Qualsiasi reimpostazione della password self-service dell'utente finale proveniente dal portale di [reimpostazione](https://passwordreset.microsoftonline.com)della password.

* **Operazioni degli amministratori supportate**
   * Qualsiasi operazione di modifica volontaria della password self-service dell'amministratore.
   * Qualsiasi operazione di modifica della password di forza self-service dell'amministratore, ad esempio la scadenza della password.
   * Qualsiasi reimpostazione della password self-service dell'amministratore che ha origine dal portale di [reimpostazione](https://passwordreset.microsoftonline.com)della password.
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dal portale di [Azure.](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operazioni di writeback non supportate

Le password non vengono riscritte in nessuna delle seguenti situazioni:

* **Attività degli utenti finali non supportate**
   * Qualsiasi utente finale che reimposta la propria password tramite PowerShell versione 1, versione 2 o l'API Microsoft Graph.
* **Operazioni degli amministratori non supportate**
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore da PowerShell versione 1, versione 2 o dall'API Microsoft Graph.
   * Qualsiasi reimpostazione della password dell'utente finale avviata dall'amministratore dall'interfaccia di amministrazione di [Microsoft 365](https://admin.microsoft.com).

> [!WARNING]
> L'utilizzo della casella di controllo "L'utente deve modificare la password all'accesso successivo" negli strumenti di amministrazione di Servizi di dominio Active Directory locali come Utenti e computer di Active Directory o il Centro di amministrazione di Active Directory è supportato come funzionalità di anteprima di Azure AD Connect. Per altre informazioni, vedere Implementare la [sincronizzazione dell'hash delle password con](../hybrid/how-to-connect-password-hash-synchronization.md)la sincronizzazione di Azure AD Connect.For more information, see Implement password hash synchronization with Azure AD Connect sync .

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare il writeback SSPR, completare l'esercitazione seguente:To get started with SSPR writeback, complete the following tutorial:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare il writeback della password self-service (SSPR)Tutorial: Enable self-service password reset (SSPR) writeback](tutorial-enable-writeback.md)
