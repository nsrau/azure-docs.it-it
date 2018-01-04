---
title: Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery | Documentazione Microsoft
description: Questo argomento illustra le considerazioni sulla sicurezza e sulla privacy correlate a Cloud App Discovery.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3ebcb5b3b4a84f7a5c25caa3f6b245f97bc8049f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery
Questo argomento descrive le modalità di raccolta, elaborazione e protezione dei dati in Azure Active Directory Cloud App Discovery. Microsoft si impegna a proteggere la privacy e i dati degli utenti e si avvale di procedure estremamente sicure per la gestione dei servizi e del ciclo di vita di sviluppo software. La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft.

> [!TIP] 
> Osservare le funzionalità del nuovo Cloud App Discovery senza agenti in Azure Active Directory (Azure AD), ottimizzate dall'[integrazione con Microsoft Cloud App Security](https://portal.cloudappsecurity.com). 

## <a name="overview"></a>Panoramica
Cloud App Discovery è una funzionalità di Azure AD ed è ospitata in Microsoft Azure.  
Cloud App Discovery Endpoint Agent viene usato per raccogliere dati di individuazione applicazioni da computer gestiti dal reparto IT. I dati raccolti vengono inviati in modo sicuro su un canale crittografato al servizio Azure AD Cloud App Discovery. I dati di Cloud App Discovery per l'organizzazione saranno quindi visibili nel portale di Azure. 

![Funzionamento di Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

Le sezioni seguenti seguono il flusso sicuro di informazioni dall'organizzazione al servizio Cloud App Discovery e infine al portale di Cloud App Discovery.

## <a name="collecting-data-from-your-organization"></a>Raccolta di dati dall'organizzazione
Per usare la funzionalità Cloud App Discovery di Azure Active Directory per ottenere informazioni approfondite sulle applicazioni usate dai dipendenti dell'organizzazione, sarà prima di tutto necessario distribuire Cloud App Discovery Endpoint Agent di Azure AD nei computer dell'organizzazione.

Gli amministratori del tenant di Azure Active Directory o i rispettivi delegati possono scaricare il pacchetto di installazione dell'agente dal portale di Azure. L'agente può essere installato manualmente oppure può essere installato in più computer dell'organizzazione tramite SCCM o Criteri di gruppo.

Per altre istruzioni sulle opzioni di distribuzione, vedere la [guida alla distribuzione di Criteri di gruppo in Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### <a name="data-collected-by-the-agent"></a>Dati raccolti dall'agente
Le informazioni incluse nell'elenco seguente vengono raccolte dall'agente quando viene stabilita una connessione a un'applicazione Web. Le informazioni vengono raccolte solo per le applicazioni configurate dall'amministratore per l'individuazione. È possibile modificare l'elenco di app cloud di cui l'agente effettua il monitoraggio tramite Cloud App Discovery di Azure AD nel [portale di Microsoft Azure](https://portal.azure.com/), facendo clic su **Impostazioni**->**Raccolta dati**->**Elenco Raccolta di app**. 

**Categoria di informazioni**: informazioni utente  
**Descrizione**: il nome utente di Windows del processo che ha effettuato una richiesta all'applicazione Web di destinazione (ad esempio DOMINIO\nomeutente), oltre al SID (Security Identifier) di Windows dell'utente.

**Categoria delle informazioni**: informazioni sui processi  
**Descrizione**: il nome del processo che ha effettuato la richiesta all'applicazione Web di destinazione (ad esempio "iexplore.exe").

**Categoria delle informazioni**: informazioni sui computer  
**Descrizione**: il nome NetBIOS del computer in cui è installato l'agente.

**Categoria delle informazioni**: informazioni sul traffico app  
**Descrizione**: le informazioni sulla connessione elencate di seguito.

* Indirizzi IP di origine (computer locale) e destinazione e numeri di porta
* Indirizzo IP pubblico dell'organizzazione attraverso cui viene trasmessa la richiesta.
* Ora della richiesta.
* Volume del traffico inviato e ricevuto.
* Versione IP (4 o 6)
* Solo per connessioni TLS: nome host di destinazione dall'estensione Indicazione nome server o dal certificato del server.

Informazioni HTTP seguenti:

* Metodo (GET, POST e così via)
* Protocollo (HTTP/1.1 e così via)
* Stringa agente utente
* Nome host
* URI di destinazione (esclusa la stringa di query)
* Informazioni sul tipo di contenuto
* Informazioni sull'URL di referrer (esclusa la stringa di query)

> [!NOTE]
> Le informazioni HTTP precedenti vengono raccolte per tutte le connessioni non crittografate.
> Per le connessioni TLS queste informazioni vengono acquisite solo se l'impostazione Ispezione approfondita è attivata nel portale. Questa impostazione è attivata per impostazione predefinita.
> Per informazioni dettagliate, vedere di seguito e vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 

Oltre ai dati sull'attività di rete, l'agente raccoglie anche informazioni anonime sugli elementi seguenti:
* Configurazione hardware e software
* Report di errori
* Dati sul modo in cui viene usato l'agente.


### <a name="how-the-agent-works"></a>Funzionamento dell'agente
L'installazione dell'agente include due componenti:

* Un componente per la modalità utente
* Un componente per il driver della modalità kernel (driver della Piattaforma filtro Windows)

Quando l'agente viene installato per la prima volta, archivia nel computer un certificato attendibile specifico del computer e lo usa quindi per stabilire una connessione sicura al servizio Cloud App Discovery. L'agente recupera periodicamente la configurazione dei criteri dal servizio Cloud App Discovery tramite questa connessione sicura. I criteri includono ad esempio informazioni sulle applicazioni cloud da monitorare e sulla necessità di abilitare o meno l'aggiornamento automatico.

Quando il traffico Web viene inviato e ricevuto nel computer da Chrome o da Internet Explorer, l'agente di Cloud App Discovery analizza il traffico ed estrae i metadati pertinenti (vedere la sezione **Dati raccolti dall'agente** precedente).  
L'agente carica ogni minuto i metadati raccolti nel servizio Cloud App Discovery usando un canale crittografato.

Il componente driver intercetta il traffico crittografato e si inserisce nel flusso crittografato. Per altri dettagli, vedere la sezione **Intercettazione dei dati da connessioni crittografate (Ispezione approfondita)** più avanti.

### <a name="respecting-user-privacy"></a>Rispetto della privacy degli utenti
Il nostro obiettivo consiste nell'offrire agli amministratori gli strumenti necessari per trovare un equilibrio tra informazioni dettagliate sull'utilizzo delle applicazioni e la privacy degli utenti, in base alle esigenze dell'organizzazione. Per questo scopo, nella pagina Impostazioni del portale sono disponibili le manopole seguenti:

* **Raccolta dati**: gli amministratori possono scegliere di specificare le applicazioni o le categorie di applicazioni per cui ottenere i dati relativi all'individuazione.
* **Ispezione approfondita**: gli amministratori possono scegliere di specificare se è l'agente a raccogliere i dati relativi al traffico HTTP per le connessioni SSL/TLS (operazione anche chiamata **ispezione approfondita**). Per altre informazioni, vedere la sezione successiva.
* **Opzioni per il consenso**: gli amministratori possono usare il portale di Cloud app Discovery per scegliere se informare gli utenti che l'agente effettua la raccolta dei dati e se richiedere il consenso dell'utente prima che l'agente inizi a raccogliere i dati utente.

Cloud App Discovery Endpoint Agent raccoglie solo le informazioni indicate nella sezione **Dati raccolti dall'agente** precedente.

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Intercettazione dei dati da connessioni crittografate (Ispezione approfondita)
Come accennato in precedenza, gli amministratori possono configurare l'agente in modo che effettui il monitoraggio dei dati da connessioni crittografate ('Ispezione approfondita'). Il protocollo TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) è uno dei protocolli Internet utilizzati più di frequente. La crittografia delle comunicazioni tramite protocollo TLS, consente ai clienti di stabilire un canale di comunicazione sicuro e privato con un server Web. Il protocollo TLS fornisce la protezione essenziale per passare le credenziali di autenticazione e impedire la divulgazione di informazioni riservate.

Anche se il canale crittografato protetto end-to-end offerto da TLS consente di tutelare la sicurezza e la privacy, questo protocollo viene spesso usato per scopi dannosi o non autorizzati. È per questo motivo che in realtà TLS viene spesso definito come il "protocollo universale di bypass del firewall". Il problema dipende dal fatto che la maggior parte dei firewall non è in grado di esaminare le comunicazioni TLS perché i dati a livello di applicazione sono crittografati con SSL. Gli autori di attacchi che conoscono questa limitazione sfruttano quindi il protocollo TLS per recapitare payload dannosi agli utenti nella certezza che persino i firewall a livello di applicazione più intelligenti non riconoscono TLS e devono semplicemente inoltrare le comunicazioni TLS tra host. Spesso gli utenti finali sfruttano il protocollo TLS per ignorare i controlli di accesso applicati da firewall e server proxy aziendali, usandolo per connettersi a proxy pubblici e per il tunneling tramite il firewall di protocolli non TLS che altrimenti verrebbero bloccati da appositi criteri.

L'ispezione approfondita consente all'agente di App Cloud Discovery di fungere da intermediario attendibile. Quando viene effettuata una richiesta client per accedere a una risorsa protetta tramite HTTPS, il driver Endpoint Agent intercetta la connessione e stabilisce una nuova connessione al server di destinazione per recuperare il certificato SSL per conto del client. L'agente verifica quindi che il certificato sia attendibile (controllando che non sia stato revocato ed effettuando altre verifiche del certificato) e, se i controlli vengono superati Endpoint Agent copia le informazioni dal certificato del server per crearne uno proprio, noto come certificato di intercettazione, usando tali informazioni. Il certificato di intercettazione viene quindi firmato immediatamente da Endpoint Agent con un certificato radice, che viene infine installato nell'archivio certificati attendibili di Windows. Il certificato radice autofirmato è contrassegnato come non esportabile e viene sottoposto ad ACL per gli amministratori. Non deve mai lasciare il computer in cui è stato creato. Quando l'applicazione client dell'utente finale riceve il certificato di intercettazione, lo considererà attendibile perché riuscirà a convalidare la catena di certificati fino al certificato radice. Questo processo è quasi completamente trasparente dal punto di vista dell'utente finale, ma occorre tenere presente alcuni aspetti, come descritto di seguito.

Quando l'ispezione approfondita è abilitata, Cloud App Discovery Endpoint Agent può decrittografare e ispezionare le comunicazioni crittografate tramite TLS, consentendo così al servizio di ridurre i dati non significativi e fornendo informazioni dettagliate sull'utilizzo delle app cloud crittografate.

#### <a name="a-word-of-caution"></a>Precisazione importante
Prima di attivare l'ispezione approfondita si consiglia di comunicare le proprie intenzioni all'ufficio legale e al reparto risorse umane per ottenerne il consenso. Per ovvi motivi l'ispezione delle comunicazioni crittografate private di un utente finale può comportare problemi di tutela della privacy. Prima di implementare l'ispezione approfondita in un ambiente di produzione, assicurarsi che i criteri relativi alla sicurezza e alle modalità di utilizzo delle informazioni accettati in ambito aziendale siano stati aggiornati in modo da indicare che le comunicazioni crittografate verranno ispezionate. Può inoltre essere necessario implementare le notifiche utente ed escludere i siti ritenuti sensibili (ad esempio quelli bancari o medici) se si configura Cloud App Discovery per includerli nel monitoraggio. Come indicato in precedenza, gli amministratori possono usare il portale di Cloud App Discovery per scegliere se informare gli utenti che l'agente effettua la raccolta dei dati e se richiedere il consenso dell'utente prima che l'agente inizi a raccogliere i dati dell'utente.

### <a name="known-issues-and-drawbacks"></a>Problemi noti e svantaggi
In alcuni casi, l'intercettazione TLS potrebbe influire negativamente sull'esperienza dell'utente finale:

* Con i certificati di convalida estesa, la barra degli indirizzi del Web browser assume un colore verde per indicare che si sta visitando un sito Web attendibile. L'ispezione TLS non può duplicare la convalida estesa del certificato rilasciato al client, quindi i siti Web che usano i certificati di convalida estesa funzionano normalmente ma la barra degli indirizzi non risulterà verde.  
* L'associazione della chiave pubblica, nota anche come associazione del certificato, è progettata per proteggere gli utenti da attacchi man-in-the-middle e autorità di certificazione non autorizzate. Quando il certificato radice di un sito aggiunto non corrisponde a una delle Autorità di certificazione valide note, il browser impedisce la connessione e restituisce un errore. Dal momento che l'intercettazione TLS è di fatto paragonabile a un attacco man-in-the-middle, queste connessioni non riusciranno.
* Se gli utenti fanno clic sull'icona con il lucchetto nella barra degli indirizzi del browser per ispezionare le informazioni del sito, non visualizzeranno una catena che termina con l'autorità di certificazione usata per firmare il certificato del sito Web ma una catena di certificati che termina con l'archivio certificati attendibili di Windows.

Per ridurre il riprodursi di questi problemi, viene tenuta traccia dei servizi cloud e delle applicazioni client che in genere usano la convalida estesa o l'associazione della chiave pubblica e indicano a Endpoint Agent di evitare di intercettare le connessioni interessate. Anche in questi casi, si continueranno a ricevere report sull'utilizzo di queste app cloud e sul volume di dati trasferiti, ma poiché non viene effettuata l'ispezione approfondita, non saranno disponibili dettagli sul modo in cui le app sono state usate.

## <a name="sending-data-to-cloud-app-discovery"></a>Invio di dati a Cloud App Discovery
Dopo la raccolta da parte dell'agente, i metadati vengono memorizzati nella cache del computer per al massimo un minuto o fino a quando i dati memorizzati nella cache non raggiungono una dimensione pari a 5 MB. Vengono quindi compressi e inviati tramite una connessione sicura al servizio Cloud App Discovery.

Se l'agente non riesce a comunicare con il servizio Cloud App Discovery per qualsiasi motivo, i metadati raccolti vengono archiviati in una cache dei file locale, a cui possono accedere solo gli utenti con privilegi elevati nel computer, ad esempio il gruppo Administrators.  
L'agente prova automaticamente a inviare di nuovo i metadati memorizzati nella cache fino a che non vengono ricevuti correttamente dal servizio Cloud App Discovery.

## <a name="receiving-the-data-at-the-service-end"></a>Ricezione dei dati sul lato servizio
Gli agenti eseguono l'autenticazione per il servizio Cloud App Discovery usando il certificato di autenticazione client specifico del computer indicato in precedenza e inoltrano i dati su un canale crittografato.  
La pipeline di analisi del servizio Cloud App Discovery elabora i metadati separatamente per ogni cliente, partizionandoli logicamente in tutte le fasi della pipeline di analisi.
I metadati analizzati sono alla base dei diversi report disponibili nel portale.

I metadati non elaborati e i metadati analizzati vengono archiviati per un massimo di 180 giorni. I clienti possono anche scegliere di acquisire i metadati analizzati nell'account di archiviazione BLOB di Azure desiderato.
Ciò risulta utile per l'analisi offline dei metadati, oltre che per la conservazione più duratura dei dati.

## <a name="accessing-the-data-using-the-azure-portal"></a>Accesso ai dati mediante il portale di Azure
Nel tentativo di proteggere i metadati raccolti, per impostazione predefinita solo gli amministratori globali del tenant possono accedere alla funzionalità Cloud App Discovery del portale di Azure.  
Gli amministratori possono tuttavia scegliere di delegare l'accesso ad altri utenti o gruppi.

> [!NOTE]
> Per informazioni dettagliate, vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 


Qualsiasi utente che accede ai dati nel portale deve avere una licenza per Azure AD Premium.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

