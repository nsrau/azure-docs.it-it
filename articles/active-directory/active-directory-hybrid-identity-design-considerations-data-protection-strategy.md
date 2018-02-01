---
title: "Progettazione di una soluzione ibrida di gestione delle identità: strategia di protezione dei dati in Azure | Microsoft Docs"
description: "Viene definita la strategia di protezione dati in modo che la soluzione ibrida di gestione delle identità soddisfi i requisiti aziendali definiti."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: d43be976f9b3fae7f3cbec1a0033f1a401ede896
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definire una strategia di protezione dei dati per la soluzione ibrida di gestione delle identità
In questa attività verrà definita la strategia di protezione dati in modo che la soluzione ibrida di gestione delle identità soddisfi i requisiti aziendali definiti in:

* [Determinare i requisiti di protezione dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinare i requisiti di gestione del contenuto](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinare i requisiti di controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinare i requisiti di risposta agli eventi imprevisti](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definire le opzioni di protezione dati
Come descritto in [Determinare i requisiti di sincronizzazione della directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), è possibile sincronizzare Microsoft Azure AD con l'istanza locale di Active Directory Domain Services. Questa integrazione consente alle organizzazioni di usare Azure Active Directory per verificare le credenziali degli utenti quando tentano di accedere alle risorse aziendali. Questa operazione può essere eseguita per entrambi gli scenari: dati inattivi in locale e nel cloud. L'accesso ai dati in Azure AD richiede l'autenticazione utente tramite un servizio token di sicurezza.

Dopo aver eseguito l'autenticazione, il nome dell'entità utente (UPN) viene letto dal token di autenticazione. Il sistema di autorizzazione determina quindi la partizione replicata e il contenitore corrispondenti al dominio dell'utente. Le informazioni relative a esistenza, stato abilitato e ruolo dell'utente vengono usate dal sistema di autorizzazione per determinare se l'accesso richiesto al tenant di destinazione è autorizzato per l'utente in questa sessione. Determinate azioni autorizzate (in particolare, creazione di utenti e reimpostazione della password) creano un audit trail che può essere usato da un amministratore del tenant per la gestione della conformità o svolgere indagini.

Non sempre l'operazione di spostamento di dati dal data center locale al servizio di archiviazione di Azure tramite una connessione Internet potrebbe risultare fattibile, a causa del volume di dati, della disponibilità della larghezza di banda o di altre considerazioni. Il [servizio di importazione/esportazione di Archiviazione di Azure](../storage/common/storage-import-export-service.md) fornisce un'opzione basata su hardware per spostare grandi quantità di dati nell'archivio BLOB o per recuperarli da esso. Consente di inviare unità disco rigido [crittografate tramite BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) direttamente in un data center di Azure dove gli operatori cloud caricano il contenuto nell'account di archiviazione o scaricano i dati di Azure nelle unità per restituirle all'utente. Per questo processo vengono accettati solo i dischi crittografati, tramite una chiave BitLocker generata dal servizio stesso durante la configurazione del processo. La chiave BitLocker viene fornita ad Azure separatamente, per consentire la condivisione della chiave fuori banda.

Poiché gli scenari in cui vengono riscontrati dati in transito sono diversi, è importante sapere che Microsoft Azure usa [reti virtuali](https://azure.microsoft.com/documentation/services/virtual-network/) per isolare il traffico dei tenant l'uno dall'altro, adottando misure quali firewall a livello di host e di guest, filtro dei pacchetti IP, blocco delle porte ed endpoint HTTPS. Tuttavia, la maggior parte delle comunicazioni interne di Azure, incluse quelle da infrastruttura a infrastruttura e da infrastruttura a cliente (locale), sono crittografate. Un altro importante scenario è quello relativo alle comunicazioni tra data center di Azure. In questo caso, Microsoft gestisce le reti per assicurarsi che nessuna macchina virtuale possa rappresentare o rimanere in ascolto sull'indirizzo IP di un'altra. Viene usata l'autenticazione TLS/SSL per l'accesso all'archiviazione di Azure o ai database SQL o per la connessione ai servizi cloud. In questo caso, l'amministratore del cliente dovrà ottenere un certificato TLS/SSL e distribuirlo nella propria infrastruttura tenant. Il traffico di dati tra macchine virtuali nella stessa distribuzione o tra tenant in una singola distribuzione tramite rete virtuale di Microsoft Azure può essere protetto usando protocolli di comunicazione crittografati, ad esempio HTTPS, SSL/TLS o altri.

A seconda delle risposte fornite alle domande nella sezione [Determinare i requisiti di protezione dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), sarà possibile stabilire come si vogliono proteggere i dati e qual è il supporto che la soluzione di gestione delle identità può offrire in tal senso. La tabella seguente illustra le opzioni supportate da Azure disponibili per ogni scenario di protezione dati.

| Opzioni di protezione dati | Inattivi nel cloud | Inattivi in locale | In transito |
| --- | --- | --- | --- |
| Crittografia unità BitLocker |X |X | |
| SQL Server per la crittografia dei database |X |X | |
| Crittografia da VM a VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Per altre informazioni sulle garanzie relative alla conformità per ogni servizio di Azure, vedere [Conformità in base al prodotto](https://azure.microsoft.com/support/trust-center/services/) nel [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/).
> Poiché le opzioni per la protezione dei dati usano un approccio multilivello, il confronto tra tali opzioni non è applicabile per questa attività. Assicurarsi di usare tutte le opzioni disponibili per ogni stato dei dati.
>
>

## <a name="define-content-management-options"></a>Definire le opzioni di gestione del contenuto
Uno dei vantaggi derivanti dall'uso di Azure AD per gestire un'infrastruttura ibrida di gestione delle identità sta nel fatto che il processo è completamente trasparente dal punto di vista dell'utente finale. L'utente tenta di accedere a una risorsa condivisa, la risorsa richiede l'autenticazione, quindi l'utente deve inviare una richiesta di autenticazione ad Azure AD per ottenere il token e accedere alla risorsa. Questo processo viene eseguito in background, senza alcuna interazione dell'utente. È anche possibile concedere l'autorizzazione a un [gruppo](active-directory-manage-groups.md#getting-started-with-access-management) di utenti per consentire loro di eseguire determinate azioni comuni.

Le organizzazioni che si preoccupano circa la privacy dei dati, richiedono in genere la classificazione dei dati per la soluzione da adottare. Se l'infrastruttura locale corrente adotta già la classificazione dei dati, è possibile usare Azure AD come repository principale per le identità degli utenti. Uno strumento comune usato in locale per la classificazione dati è [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) per Windows Server 2012 R2. Questo strumento consente di identificare, classificare e proteggere i dati nei file server del cloud privato. Per questo scopo, è anche possibile usare la funzionalità di [classificazione file automatica](https://technet.microsoft.com/library/hh831672.aspx) in Windows Server 2012.

Se l'organizzazione non ha implementato una soluzione di classificazione dei dati ma ha la necessità di proteggere i file sensibili senza aggiungere nuovi server locali, è disponibile Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS usa infatti criteri di crittografia, identità e autorizzazione in grado di proteggere file e posta elettronica e può essere usato su più dispositivi, inclusi telefoni, tablet e PC. Poiché Azure RMS è un servizio cloud, per poter condividere contenuti protetti con altre organizzazioni non è necessario configurare esplicitamente dei trust con queste ultime. Se queste organizzazioni dispongono già di una directory di Office 365 o di Azure AD, la collaborazione tra organizzazioni è supportata automaticamente. È anche possibile sincronizzare solo gli attributi della directory richiesti da Azure RMS per supportare un'identità comune per gli account Active Directory locali usando i servizi di sincronizzazione di Azure Active Directory (AAD Sync) o Azure AD Connect.

Per un'efficace gestione del contenuto, è necessario sapere chi accede a una determinata risorsa, di conseguenza la soluzione di gestione delle identità deve includere funzionalità di registrazione avanzate. Azure AD fornisce la registrazione delle informazioni seguenti per un periodo di oltre 30 giorni:

* Modifiche all'appartenenza dei ruoli, ad esempio utenti aggiunti al ruolo amministratore globale
* Aggiornamenti delle credenziali, ad esempio modifiche delle password
* Gestione dei domini, ad esempio verifica di un dominio personalizzato, rimozione di un dominio
* Aggiunta o rimozione di applicazioni
* Gestione di utenti, ad esempio aggiunta, rimozione o aggiornamento di un utente
* Aggiunta o rimozione di licenze

> [!NOTE]
> Per altre informazioni sulle funzionalità di registrazione in Azure, vedere l'articolo relativo alla [gestione dei log di controllo e sicurezza di Microsoft Azure](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) .
> A seconda delle risposte fornite alle domande nella sezione [Determinare i requisiti di gestione del contenuto](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), sarà possibile stabilire come si vuole che il contenuto venga gestito nella soluzione ibrida di gestione delle identità. Sebbene tutte le opzioni esposte nella tabella 6 possano essere integrate con Azure AD, è importante definire quella più appropriata in base alle esigenze aziendali.
>
>

| Opzioni di gestione del contenuto | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Centralizzata locale (server Active Directory Rights Management) |Controllo completo sull'infrastruttura server responsabile della classificazione dei dati  <br> Funzionalità predefinita in Windows Server, senza necessità di licenza o sottoscrizione supplementare <br> Può essere integrato con Azure AD in uno scenario ibrido <br> Supporta la funzionalità Information Rights Management (IRM) nei servizi Microsoft Online come Exchange Online e SharePoint Online, nonché Office 365 <br> Supporta i prodotti server Microsoft locali, ad esempio Exchange Server, SharePoint Server e file server che eseguono Windows Server e Infrastruttura di classificazione file. |Manutenzione più elevata (aggiornamenti, configurazioni e potenziali aggiornamenti di versione), poiché il server è di proprietà del reparto IT <br> Richiede un'infrastruttura di server locale<br> Non usa le funzionalità di Azure in modo nativo |
| Centralizzata nel cloud (Azure RMS) |Più facile da gestire rispetto alla soluzione locale  <br> Può essere integrato con Servizi di dominio Active Directory in uno scenario ibrido <br>  Completamente integrato con Azure Active Directory <br> Non richiede un server locale per distribuire il servizio <br> Supporta i prodotti server Microsoft locali, ad esempio Exchange Server, SharePoint Server e file server che eseguono Windows Server e Infrastruttura di classificazione file <br> Il reparto IT ha il controllo completo sulla chiave del tenant grazie alla funzionalità BYOK (Bring Your Own Key). |L'organizzazione deve avere una sottoscrizione cloud che supporta RMS  <br> L'organizzazione deve avere una directory di Azure AD per supportare l'autenticazione utente per RMS |
| Ibrida (soluzione Azure RMS integrata con un server Active Directory Rights Management locale) |Questo scenario combina i vantaggi di entrambe le opzioni precedenti, centralizzata locale e nel cloud. |L'organizzazione deve avere una sottoscrizione cloud che supporta RMS  <br> L'organizzazione deve avere una directory di Azure AD per supportare l'autenticazione utente per RMS, <br> Richiede una connessione tra il servizio cloud di Azure e l'infrastruttura locale |

## <a name="define-access-control-options"></a>Definire le opzioni di controllo di accesso
Le funzionalità di autenticazione, autorizzazione e controllo di accesso disponibili in Azure AD consentono alla società di usare un repository delle identità centralizzato e al contempo permettono a utenti e partner di usare l'accesso Single Sign-On (SSO), come illustrato nella figura seguente:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gestione centralizzata e integrazione completa con altre directory

Azure Active Directory offre l'accesso Single Sign-On a migliaia di applicazioni SaaS e di applicazioni Web locali. Per altre informazioni sull'accesso Single Sign-On con provider di terze parti testati da Microsoft, vedere l'[elenco di compatibilità per la federazione di Azure Active Directory: provider di identità di terze parti che possono essere usati per implementare l'accesso Single Sign-On](https://msdn.microsoft.com/library/azure/jj679342.aspx). Questa funzionalità consente alle organizzazioni di implementare una vasta gamma di scenari B2B mantenendo il controllo della gestione delle identità e degli accessi. Tuttavia, durante il processo di progettazione B2B è importante comprendere il metodo di autenticazione che verrà usato dal partner e verificare se questo metodo è supportato da Azure. Attualmente, i metodi seguenti sono supportati da Azure AD:

* SAML (Security Assertion Markup Language)
* OAuth
* Kerberos
* Tokens
* Certificati

> [!NOTE]
> Per informazioni dettagliate su ogni protocollo e sulle relative funzionalità in Azure, leggere la pagina relativa ai [protocolli di autenticazione di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) .
>
>

Grazie al supporto di Azure AD, le applicazioni aziendali per dispositivi mobili possono usare la stessa esperienza di autenticazione semplificata a Servizi mobili per consentire ai dipendenti di accedere alle applicazioni per dispositivi mobili con le proprie credenziali Active Directory aziendali. Con questa funzionalità, Azure AD è supportato come un provider di identità in Servizi mobili, insieme agli altri provider di identità già supportati, ad esempio Account Microsoft, ID Facebook, ID Google e ID Twitter. Se le app locali usano le credenziali dell'utente che risiedono nell'istanza di Active Directory Domain Services locale, l'accesso da partner e utenti provenienti dal cloud dovrebbe essere trasparente. È possibile gestire il controllo di accesso condizionale dell'utente alle applicazioni Web (basate sul cloud), alle API Web, ai servizi cloud Microsoft, alle applicazioni SaaS di terze parti e alle applicazioni client (per dispositivi mobili) native e usufruire al contempo delle funzionalità di sicurezza, controllo e creazione di report da un'unica posizione centralizzata. È tuttavia consigliabile convalidare questa implementazione in un ambiente non di produzione o con un numero limitato di utenti.

> [!TIP]
> È importante tenere presente che in Azure AD non è disponibile la funzionalità Criteri di gruppo come in Servizi di dominio Active Directory. Per imporre criteri per i dispositivi, sarà necessario adottare una soluzione per la gestione di dispositivi mobili, ad esempio [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Dopo aver eseguito l'autenticazione dell'utente tramite Azure AD, è importante valutare il livello di accesso che verrà assegnato all'utente. Il livello di accesso che verrà assegnato all'utente per una risorsa può variare. Sebbene Azure AD possa aggiungere un ulteriore livello di sicurezza controllando l'accesso ad alcune risorse, tenere presente che potrebbe essere stato definito un elenco di controllo di accesso separato per la risorsa stessa, ad esempio per l'accesso ai file che risiedono in un file server. La figura seguente riassume i livelli di controllo di accesso che possono essere impostati in uno scenario ibrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

Ogni interazione nel diagramma illustrato nella figura X rappresenta uno scenario di controllo di accesso che può essere gestito da Azure AD. Di seguito è mostrata una descrizione di ogni scenario:

1. Accesso condizionale alle applicazioni ospitate in locale: è possibile usare i dispositivi registrati con criteri di accesso per le applicazioni configurate per l'uso di AD FS con Windows Server 2012 R2. Per altre informazioni su come configurare l'accesso condizionale in locale, vedere [Configurazione dell'accesso condizionale in locale usando il servizio Registrazione del dispositivo di Azure Active Directory](active-directory-conditional-access-azure-portal.md).

2. Controllo di accesso al portale di Azure: Azure consente inoltre di controllare l'accesso al portale tramite il controllo di accesso basato sui ruoli (RBAC)). Questo metodo permette all'azienda di limitare il numero di operazioni che possono essere eseguite da un soggetto nel portale di Azure. L'uso del controllo degli accessi in base al ruolo per controllare gli accessi al portale, permette agli amministratori IT di delegare l'accesso tramite gli approcci di gestione dell'accesso seguenti:

    * Assegnazione di ruolo basata su gruppo: è possibile assegnare l'accesso ai gruppi di Azure AD che possono essere sincronizzati dall'istanza di Active Directory locale. Questo consente di sfruttare gli investimenti esistenti dell'organizzazione in termini di strumenti e processi per la gestione dei gruppi. È anche possibile usare la funzionalità di delega della gestione dei gruppi disponibile in Azure AD Premium.
    * Uso dei ruoli predefiniti in Azure: sono disponibili tre ruoli, proprietario, collaboratore e lettore, per garantire che utenti e gruppi siano autorizzati a eseguire solo le attività necessarie per svolgere il proprio lavoro.
    * Accesso granulare alle risorse: è possibile assegnare ruoli a utenti e gruppi per una sottoscrizione, un gruppo di risorse o una singola risorsa di Azure specifica, ad esempio un sito Web o un database. In questo modo, è possibile fare in modo che gli utenti possano accedere a tutte le risorse necessarie e non abbiano accesso alle risorse che invece non devono gestire.

> [!NOTE]
> Gli sviluppatori che creano applicazioni e che vogliono personalizzare il controllo di accesso per tali applicazioni, possono usare i ruoli applicazione di Azure AD per l'autorizzazione. Per informazioni su come progettare l'app per usare questa funzionalità, vedere l' [esempio WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) .
>
>

3. Accesso condizionale per applicazioni di Office 365 con Microsoft Intune: gli amministratori IT possono eseguire il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli operatori dei sistemi informativi che usano dispositivi conformi di accedere ai servizi. Per altre informazioni, vedere [Criteri di accesso condizionale dei dispositivi per i servizi di Office 365](active-directory-conditional-access-device-policies.md).

4. Accesso condizionale per app SaaS: [questa funzionalità](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) consente di configurare regole di accesso con autenticazione a più fattori per ogni applicazione e di bloccare l'accesso agli utenti di reti non attendibili. La regola di autenticazione a più fattori può essere applicata a tutti gli utenti assegnati all'applicazione oppure solo agli utenti inclusi in gruppi di sicurezza specificati. Gli utenti possono essere esclusi dal requisito di autenticazione a più fattori se accedono all'applicazione da un indirizzo IP all'interno della rete dell'organizzazione.

Poiché le opzioni per il controllo di accesso usano un approccio multilivello, il confronto tra tali opzioni non è applicabile per questa attività. Assicurarsi di usare tutte le opzioni disponibili per ogni scenario che richiede di controllare l'accesso alle risorse.

## <a name="define-incident-response-options"></a>Definire le opzioni di risposta agli eventi imprevisti
Azure AD può supportare il reparto IT nelle procedure di identificazione di potenziali rischi di sicurezza nell'ambiente mediante il monitoraggio delle attività dell'utente. Il reparto IT può usare i report di uso e accesso di Azure Active Directory per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore della directory può determinare le aree in cui possono risiedere i potenziali rischi per la sicurezza in modo da poterne pianificare adeguatamente l'attenuazione.  [sottoscrizione Azure AD Premium](active-directory-get-started-premium.md) include un set di report di sicurezza che consentono al reparto IT di ottenere tali informazioni. I [report di Azure AD](active-directory-view-access-usage-reports.md) sono classificati nel modo seguente:

* **Report anomalie**: contengono eventi di accesso considerati anomali. L'obiettivo è rendere gli utenti consapevoli di tale attività e consentire loro di essere in grado di stabilire se un evento è sospetto.
* **Report applicazioni integrate**: fornisce informazioni dettagliate sul modo in cui vengono usate le applicazioni cloud nell'organizzazione. Azure Active Directory offre l'integrazione con migliaia di applicazioni cloud.
* **Report di errori**: segnalano gli errori che possono verificarsi durante il provisioning di account in applicazioni esterne.
* **Report specifici dell'utente**: visualizzano i dati del dispositivo/dell'attività di accesso per un utente specifico.
* **Log attività**: contengono un record di tutti gli eventi controllati nelle ultime 24 ore, negli ultimi 7 giorni o negli ultimi 30 giorni, nonché le modifiche alle attività del gruppo e le attività di registrazione e di reimpostazione password.

> [!TIP]
> Un altro report che può risultare utile per il team che gestisce le risposte per gli eventi imprevisti è il report [Utenti con credenziali perse](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) . Questo report evidenzia eventuali corrispondenze tra l'elenco delle credenziali perse e il tenant in uso.
>
>

Altri importanti report predefiniti disponibili in Azure AD che possono risultare utili per fornire le risposte durante l'analisi degli eventi imprevisti sono:

* **Attività di reimpostazione password**: fornisce all'amministratore informazioni dettagliate sul modo in cui la funzionalità di reimpostazione della password viene usata all'interno dell'organizzazione.
* **Attività di registrazione per la reimpostazione delle password**: fornisce informazioni dettagliate sugli utenti che hanno registrato i propri metodi per la reimpostazione delle password e sui metodi selezionati.
* **Attività del gruppo**: fornisce la cronologia delle modifiche apportate al gruppo, ad esempio utenti aggiunti o rimossi, avviate nel pannello di accesso.

Oltre alle funzionalità di creazione di report principali disponibili in Azure AD Premium che possono essere usate durante un processo di analisi per fornire le risposte relative agli eventi imprevisti, il reparto IT può usare anche il report di controllo che permette di ottenere le informazioni seguenti:

* Modifiche all'appartenenza dei ruoli, ad esempio utenti aggiunti al ruolo amministratore globale
* Aggiornamenti delle credenziali, ad esempio modifiche delle password
* Gestione dei domini, ad esempio verifica di un dominio personalizzato, rimozione di un dominio
* Aggiunta o rimozione di applicazioni
* Gestione di utenti, ad esempio aggiunta, rimozione o aggiornamento di un utente
* Aggiunta o rimozione di licenze

Poiché le opzioni per le risposte agli eventi imprevisti usano un approccio multilivello, il confronto tra tali opzioni non è applicabile per questa attività. Assicurarsi di usare tutte le opzioni disponibili per ogni scenario che richiede l'uso della funzionalità di creazione di report di Azure AD come parte del processo di risposta agli eventi imprevisti della società.

## <a name="next-steps"></a>Passaggi successivi
[Determinare le attività per la soluzione ibrida di gestione delle identità](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
