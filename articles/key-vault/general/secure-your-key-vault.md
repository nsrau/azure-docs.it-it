---
title: Proteggere l'accesso a un insieme di credenziali delle chiavi
description: Modello di accesso per Azure Key Vault, inclusi Active Directory l'autenticazione e gli endpoint delle risorse.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 9516a32e89b9ad671cf705c8f520c73e28801c19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320592"
---
# <a name="secure-access-to-a-key-vault"></a>Proteggere l'accesso a un insieme di credenziali delle chiavi

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di accesso a Key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

## <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce: il **piano di gestione** e il **piano dati**. Il piano di gestione consente di gestire l'insieme di credenziali delle chiavi stesso. Le operazioni in questo piano includono la creazione e l'eliminazione di insiemi di credenziali delle chiavi, il recupero delle proprietà dell'insieme di credenziali delle chiavi e l'aggiornamento dei criteri di accesso. Il piano dati consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in uno dei piani, tutti i chiamanti (utenti o applicazioni) devono disporre di autenticazione e autorizzazione appropriate. L'autenticazione stabilisce l'identità del chiamante. L'autorizzazione determina le operazioni che il chiamante può eseguire.

Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo di Azure (RBAC) e il piano dati usa un criterio di accesso Key Vault e l'anteprima di Azure.

## <a name="active-directory-authentication"></a>Autenticazione di Azure Active Directory

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Tutti i chiamanti in entrambi i piani devono essere registrati in questo tenant ed eseguire l'autenticazione per accedere all'insieme di credenziali delle chiavi. In entrambi i casi, le applicazioni possono accedere a un insieme di credenziali delle chiavi in due modi:

- **Solo applicazione**: l'applicazione rappresenta un servizio o un processo in background. Questa identità è lo scenario più comune per le applicazioni che devono periodicamente accedere a certificati, chiavi o segreti dall'insieme di credenziali delle chiavi. Per il corretto funzionamento di questo scenario, `objectId` è necessario specificare l'oggetto dell'applicazione nei criteri di accesso `applicationId` e _non_ deve essere specificato o deve essere `null` .
- **Solo utente**: l'utente accede all'insieme di credenziali delle chiavi da qualsiasi applicazione registrata nel tenant. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. Per il corretto funzionamento di questo scenario, `objectId` è necessario specificare il parametro dell'utente nei criteri di accesso `applicationId` e _non_ deve essere specificato o deve essere `null` .
- **Application-Plus-User** (noto anche come _identità composta_): l'utente deve accedere all'insieme di credenziali delle chiavi da un'applicazione specifica _e_ l'applicazione deve usare il flusso di autenticazione (OBO) per rappresentare l'utente. Per il corretto funzionamento di questo scenario `applicationId` , `objectId` è necessario specificare sia che nei criteri di accesso. `applicationId`Identifica l'applicazione richiesta e `objectId` identifica l'utente. Attualmente questa opzione non è disponibile per il piano dati Azure RBAC (anteprima).

In tutti i tipi di accesso, l'applicazione esegue l'autenticazione con Azure AD. L'applicazione usa qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-scenarios.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa del piano per la concessione dell'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

## <a name="resource-endpoints"></a>Endpoint delle risorse

Le applicazioni accedono ai piani tramite endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per usare le chiavi in un insieme di credenziali delle chiavi, l'accesso al piano dati viene concesso usando un criterio di accesso Key Vault o il controllo degli accessi in base al ruolo di Azure Per concedere a un utente l'accesso in lettura alle proprietà e ai tag dell'insieme di credenziali delle chiavi, ma non ai dati (chiavi, segreti o certificati), concedere l'accesso al piano di gestione con il controllo degli accessi in base al ruolo.

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo&nbsp;di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br><br> **21Vianet per Azure Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 | Creare, leggere, aggiornare ed eliminare insiemi di credenziali delle chiavi<br><br>Impostare i criteri di accesso dell'insieme di credenziali delle chiavi<br><br>Impostare tag per l'insieme di credenziali delle chiavi | Controllo degli accessi in base al ruolo di Azure |
| Piano dati | **Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **21Vianet per Azure Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure US Gov:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 | Chiavi: crittografia, decrittografia, wrapKey, unwrapKey, firma, verifica, recupero, elenco, creazione, aggiornamento, importazione, eliminazione, ripristino, backup, ripristino, eliminazione<br><br> Certificati: managecontacts, getissuers, listissuers, seemittenti, deleteissuers, manageissuers, Get, List, create, Import, Update, DELETE, Recover, backup, Restore, Purge<br><br>  Segreti: Get, List, set, DELETE, Recover, backup, Restore, Purge | Criteri di accesso Key Vault o RBAC di Azure (anteprima)|

## <a name="management-plane-and-azure-rbac"></a>Piano di gestione e RBAC di Azure

Nel piano di gestione si usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per autorizzare le operazioni che possono essere eseguite da un chiamante. Nel modello di controllo degli accessi in base al ruolo di Azure ogni sottoscrizione di Azure ha un'istanza di Azure AD. È possibile concedere l'accesso a utenti, gruppi e applicazioni da questa directory. Viene concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager.

Creare un insieme di credenziali delle chiavi in un gruppo di risorse e gestire l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. Si concede l'accesso a un livello di ambito specifico assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Sottoscrizione**: un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse**: un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse nel gruppo di risorse.
- **Risorsa specifica**: un ruolo di Azure assegnato a una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Piano dati e criteri di accesso

È possibile concedere l'accesso al piano dati impostando Key Vault criteri di accesso per un insieme di credenziali delle chiavi. Per impostare i criteri di accesso, un utente, un gruppo o un'applicazione deve avere le autorizzazioni `Contributor` per il piano di gestione per l'insieme di credenziali delle chiavi.

È possibile concedere l'accesso a un utente, un gruppo o un'applicazione per eseguire operazioni specifiche relative a segreti o chiavi in un insieme di credenziali delle chiavi. Key Vault supporta fino a 1.024 voci di criteri di accesso per un insieme di credenziali delle chiavi. Per concedere a più utenti l'accesso al piano dati, creare un gruppo di sicurezza di Azure AD e aggiungere gli utenti a tale gruppo.

È possibile visualizzare l'elenco completo delle operazioni di insieme di credenziali e segrete qui: [Key Vault riferimento all'operazione](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> I criteri di accesso dell'insieme di credenziali delle chiavi concedono autorizzazioni separate per chiavi, segreti e certificati.  Le autorizzazioni di accesso per chiavi, segreti e certificati vengono definite a livello di insieme di credenziali. 

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi si applicano a livello di insieme di credenziali. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.
I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Piano dati e RBAC di Azure (anteprima)

Il controllo degli accessi in base al ruolo di Azure è un modello di autorizzazione alternativo per controllare l'accesso al piano dati Azure Key Vault, che può essere abilitato nei singoli insiemi di credenziali delle chiavi. Il modello di autorizzazione RBAC di Azure è esclusivo e una volta impostato, i criteri di accesso dell'insieme di credenziali diventano inattivi. Azure Key Vault definisce un set di ruoli predefiniti di Azure che comprende i set comuni di autorizzazioni utilizzate per accedere a chiavi, segreti o certificati.

Quando un ruolo di Azure viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello della sottoscrizione, al gruppo di risorse, all'insieme di credenziali delle chiavi o a una chiave, un segreto o un certificato. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

I vantaggi principali dell'uso dell'autorizzazione RBAC di Azure per i criteri di accesso dell'insieme di credenziali sono la gestione centralizzata del controllo degli accessi e l'integrazione con Privileged Identity Management (PIM). Privileged Identity Management fornisce l'attivazione del ruolo basata sul tempo e sull'approvazione per attenuare i rischi di autorizzazioni di accesso eccessive, inutili o usate in modo improprio per le risorse di importanza strategica.


## <a name="firewalls-and-virtual-networks"></a>Firewall e reti virtuali

Per un ulteriore livello di sicurezza, è possibile configurare i firewall e le regole della rete virtuale. È possibile configurare reti virtuali e firewall di Key Vault per negare l'accesso al traffico da tutte le reti, incluso il traffico Internet, per impostazione predefinita. È possibile concedere l'accesso al traffico proveniente da reti virtuali specifiche di Azure e intervalli di indirizzi IP Internet pubblici, creando un limite di rete protetta per le applicazioni.

Di seguito sono riportati alcuni esempi di uso degli endpoint del servizio:

* Si usa Key Vault per archiviare chiavi di crittografia, segreti dell'applicazione, certificati e si vuole bloccare l'accesso all'insieme di credenziali delle chiavi dalla rete Internet pubblica.
* Si vuole bloccare l'accesso all'insieme di credenziali delle chiavi in modo che solo l'applicazione o un breve elenco di host designati possano connettesi all'insieme di credenziali delle chiavi.
* Si ha un'applicazione in esecuzione nella rete virtuale di Azure e la rete virtuale è bloccata per tutto il traffico in ingresso e in uscita. L'applicazione deve comunque connettersi a Key Vault per recuperare segreti o certificati o usare le chiavi di crittografia.

> [!NOTE]
> I firewall di Key Vault e le regole di rete virtuale si applicano solo al piano dati di Key Vault. Le operazioni del piano di controllo Key Vault, ad esempio le operazioni di creazione, eliminazione e modifica, l'impostazione di criteri di accesso, la configurazione del firewall e delle regole di rete virtuale, non sono interessate dai firewall e dalle regole di rete virtuale.

## <a name="private-endpoint-connection"></a>Connessione all'endpoint privato

In caso di necessità di bloccare completamente Key Vault l'esposizione al pubblico, è possibile usare un endpoint privato di Azure. Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Scenari comuni per l'uso del collegamento privato per i servizi di Azure:

- **Accesso privato ai servizi nella piattaforma Azure**: connettere la rete virtuale ai servizi in Azure senza un indirizzo IP pubblico all'origine o alla destinazione. I provider possono offrire i servizi nella loro rete virtuale e i clienti possono accedervi nella loro rete virtuale locale. La piattaforma di Collegamento privato gestirà la connettività tra l'utente e i servizi tramite la rete backbone di Azure. 
 
- **Reti locali e con peering**: accedere ai servizi in esecuzione in Azure dall'ambiente locale tramite peering privato ExpressRoute, tunnel VPN e reti virtuali con peering usando endpoint privati. Non è necessario configurare il peering pubblico o attraversare Internet per raggiungere il servizio. Collegamento privato offre un modo sicuro per eseguire la migrazione dei carichi di lavoro ad Azure.
 
- **Protezione dall'esfiltrazione di dati**: un endpoint privato viene mappato a un'istanza di una risorsa PaaS invece che all'intero servizio. Gli utenti possono connettersi solo alla risorsa specifica. L'accesso a qualsiasi altra risorsa del servizio è bloccato. Questo meccanismo offre protezione dai rischi di perdita dei dati. 
 
- **Copertura globale**: è possibile connettersi privatamente a servizi in esecuzione in altre aree. La rete virtuale dell'utente può pertanto trovarsi nell'area A e connettersi ai servizi con Collegamento privato nell'area B.  
 
- **Estensione a servizi personalizzati**: la stessa esperienza e le stesse funzionalità possono essere sfruttate per offrire privatamente un servizio personalizzato agli utenti in Azure. Posizionando un servizio dietro Azure Load Balancer, è possibile abilitarlo per Collegamento privato. L'utente potrà quindi connettersi direttamente al servizio usando un endpoint privato nella propria rete virtuale. È possibile gestire queste richieste di connessione con un semplice flusso di chiamate di approvazione. Collegamento privato di Azure funziona anche per utenti e servizi appartenenti a tenant di Azure Active Directory diversi. 

## <a name="example"></a>Esempio

In questo esempio si sta sviluppando un'applicazione che usa un certificato per TLS/SSL, archiviazione di Azure per archiviare i dati e una chiave RSA a 2.048 bit per la crittografia dei dati in archiviazione di Azure. L'applicazione viene eseguita in una macchina virtuale di Azure (o un set di scalabilità di macchine virtuali). È possibile usare un insieme di credenziali delle chiavi per archiviare i segreti dell'applicazione. È possibile archiviare il certificato bootstrap usato dall'applicazione per l'autenticazione con Azure AD.

È necessario l'accesso alle chiavi e ai segreti seguenti:
- **Certificato TLS/SSL**: Usato per TLS/SSL.
- **Chiave di archiviazione**: usata per accedere all'account di archiviazione.
- **Chiave RSA a 2.048 bit**: usata per la chiave di crittografia dei dati a wrapping/Unwrap mediante archiviazione di Azure.
- **Identità gestita dall'applicazione**: usata per l'autenticazione con Azure ad. Dopo aver concesso l'accesso Key Vault, l'applicazione può recuperare la chiave di archiviazione e il certificato.

È necessario definire i ruoli seguenti per specificare chi può gestire, distribuire e controllare l'applicazione:
- **Team responsabile della sicurezza**: personale IT che lavora nell'ufficio del responsabile della sicurezza o collaboratori analoghi. Il team responsabile della sicurezza si occupa della salvaguardia di segreti. I segreti possono includere certificati TLS/SSL, chiavi RSA per la crittografia, stringhe di connessione e chiavi dell'account di archiviazione.
- **Sviluppatori e operatori**: il personale che sviluppa l'applicazione e la distribuisce in Azure. I membri di questo team non fanno parte del personale responsabile della sicurezza. Non devono avere accesso ai dati sensibili, come i certificati TLS/SSL e le chiavi RSA. Solo l'applicazione da loro distribuita deve avere accesso ai dati sensibili.
- **Revisori**: questo ruolo è per i collaboratori che non sono membri del personale IT generico o di sviluppo. Queste persone verificano l'uso e la gestione di certificati, chiavi e segreti per garantire la conformità agli standard di sicurezza.

C'è un altro ruolo che non rientra nell'ambito dell'applicazione: l'amministratore della sottoscrizione (o del gruppo di risorse). L'amministratore della sottoscrizione configura le autorizzazioni di accesso iniziali per il team responsabile della sicurezza. L'accesso al team responsabile della sicurezza viene concesso tramite un gruppo di risorse che contiene le risorse necessarie per l'applicazione.

È necessario autorizzare le operazioni seguenti per i ruoli:

**Team responsabile della sicurezza**
- Creare insiemi di credenziali delle chiavi.
- Attivare la registrazione per gli insiemi di credenziali delle chiavi.
- Aggiungere chiavi e segreti.
- Creare i backup delle chiavi per il ripristino di emergenza.
- Impostare Key Vault criteri di accesso e assegnare i ruoli per concedere le autorizzazioni a utenti e applicazioni per operazioni specifiche.
- Aggiornare periodicamente chiavi e segreti.

**Sviluppatori e operatori**
- Ottenere i riferimenti dal team di sicurezza per i certificati bootstrap e TLS/SSL (identificazioni personali), la chiave di archiviazione (URI del segreto) e la chiave RSA (URI chiave) per l'incapsulamento/unwrapping.
- Sviluppare e distribuire l'applicazione per accedere ai certificati e ai segreti a livello di codice.

**Revisori**
- Esaminare i log di Key Vault per verificare che chiavi e segreti siano usati in modo appropriato e conformemente agli standard di sicurezza dei dati.

Nella tabella seguente sono riepilogate le autorizzazioni di accesso per i ruoli e l'applicazione.

| Ruolo | Autorizzazioni del piano di gestione | Autorizzazioni del piano dati-criteri di accesso all'insieme di credenziali | Autorizzazioni del piano dati-RBAC di Azure (anteprima)  |
| --- | --- | --- | --- |
| Team responsabile della sicurezza | Collaboratore di Key Vault | Certificati: tutte le operazioni <br> Chiavi: tutte le operazioni <br> Segreti: tutte le operazioni | Amministratore Key Vault (anteprima) |
| Sviluppatori e&nbsp;operatori | Autorizzazione di distribuzione dell'insieme di credenziali delle chiavi<br><br> **Nota**: Questa autorizzazione consente alle macchine virtuali distribuite di recuperare i segreti da un insieme di credenziali delle chiavi. | nessuno | nessuno |
| Revisori | nessuno | Certificati: elenco <br> Chiavi: list<br>Segreti: list<br><br> **Nota**: Questa autorizzazione consente ai revisori di esaminare gli attributi (tag e date di attivazione e scadenza) per le chiavi e i segreti che non vengono riportati nei log. | Lettore di Key Vault (anteprima) |
| Account di archiviazione di Azure | nessuno | Chiavi: Get, List, wrapKey, unwrapKey <br> | Crittografia del servizio di crittografia Key Vault |
| Applicazione | nessuno | Segreti: Get, List <br> Certificati: Get, List | Lettore di Key Vault (anteprima), utente segreto Key Vault (anteprima) |

Oltre alle autorizzazioni per l'insieme di credenziali delle chiavi, i tre i ruoli dei team devono poter accedere ad altre risorse. Per distribuire le macchine virtuali (o la funzionalità app Web del servizio app Azure), gli sviluppatori e gli operatori necessitano dell'accesso deploy. I revisori necessitano dell'accesso in lettura all'account di archiviazione in cui vengono archiviati i log dell'insieme di credenziali delle chiavi.

Questo esempio illustra uno scenario semplice. Gli scenari reali possono essere più complessi. È possibile modificare le autorizzazioni per l'insieme di credenziali delle chiavi in base alle esigenze. In questo esempio si presuppone che il team responsabile della sicurezza fornisca i riferimenti a chiavi e segreti (URI e identificazioni personali) usati dal personale DevOps nelle applicazioni. Sviluppatori e operatori non necessitano di alcun accesso al piano dati. In questo articolo è stato analizzato in particolare come proteggere l'insieme di credenziali delle chiavi.

> [!NOTE]
> Questo esempio mostra come l'accesso all'insieme di credenziali delle chiavi viene bloccato nell'ambiente di produzione. Gli sviluppatori devono avere una sottoscrizione o un gruppo di risorse personale su cui dispongono di autorizzazioni complete per gestire gli insiemi di credenziali delle chiavi, le macchine virtuali e l'account di archiviazione in cui sviluppano l'applicazione.

## <a name="resources"></a>Risorse

* [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire l'autenticazione con Azure Key Vault](authentication.md)

[Assegnare un criterio di accesso Key Vault](assign-access-policy-portal.md)

[Assegnare il ruolo di Azure per accedere a chiavi, segreti e certificati](rbac-guide.md)

[Configurare i firewall e le reti virtuali Key Vault](network-security.md)

[Stabilire una connessione di collegamento privata a Key Vault](private-link-service.md)
