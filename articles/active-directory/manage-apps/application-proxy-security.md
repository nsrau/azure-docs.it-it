---
title: Considerazioni relative alla sicurezza per il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta considerazioni relative alla sicurezza quando si usa il proxy applicazione di Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481229"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD

Questo articolo illustra i componenti che vengono usati per proteggere la sicurezza degli utenti e delle applicazioni quando si usa il proxy applicazione di Azure Active Directory.

Il diagramma seguente illustra in che modo Azure AD consente l'accesso remoto sicuro alle applicazioni locali.

 ![Diagramma dell'accesso remoto sicuro con il proxy applicazione di Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Vantaggi di sicurezza

Il proxy applicazione di Azure AD offre i vantaggi di sicurezza seguenti:

### <a name="authenticated-access"></a>Accesso autenticato 

Se si sceglie di usare la preautenticazione di Azure Active Directory, solo le connessioni autenticate possono accedere alla rete.

Il proxy applicazione di Azure AD fa affidamento sul servizio token di sicurezza di Azure AD per tutta l'autenticazione.  La preautenticazione, per sua natura, blocca un numero significativo di attacchi anonimi perché solo le identità autenticate possono accedere all'applicazione back-end.

Se si sceglie Pass-through come metodo di preautenticazione, non si otterrà questo vantaggio. 

### <a name="conditional-access"></a>Accesso condizionale

Applicare controlli dei criteri più completi prima che vengano stabilite connessioni alla rete.

Con [l'accesso condizionale](../conditional-access/overview.md)è possibile definire restrizioni sul traffico autorizzato ad accedere alle applicazioni back-end. È possibile, ad esempio, creare criteri per definire restrizioni in base alla posizione, al livello di autenticazione e al profilo di rischio.

È anche possibile usare l'accesso condizionale per configurare i criteri di Multi-Factor Authentication, aggiungendo un altro livello di sicurezza alle autenticazioni degli utenti. Inoltre, le applicazioni possono essere instradate a Microsoft Cloud App Security tramite Azure AD l'accesso condizionale per fornire controlli e monitoraggio in tempo reale, tramite criteri di [accesso](https://docs.microsoft.com/cloud-app-security/access-policy-aad) e di [sessione](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Terminazione di traffico

Tutto il traffico viene terminato nel cloud.

Il proxy applicazione di Azure AD è un proxy inverso, quindi tutto il traffico verso le applicazioni back-end viene terminato nel servizio. La sessione può essere ristabilita solo con il server back-end e ciò significa che i server back-end non sono esposti al traffico HTTP diretto. Questa configurazione consente una protezione migliore da attacchi mirati.

### <a name="all-access-is-outbound"></a>Tutti gli accessi sono in uscita 

Non è necessario aprire alcuna connessione in ingresso nella rete aziendale.

I connettori proxy di applicazione usano solo connessioni in uscita per il servizio proxy di applicazione di Azure AD e, pertanto, non è necessario aprire le porte del firewall per le connessioni in ingresso. I proxy tradizionali richiedono una rete perimetrale (nota anche come *subnet schermata*o *DMZ*) e possono accedere a connessioni non autenticate al perimetro della rete. *demilitarized zone* Questo scenario richiede investimenti in prodotti web application firewall per analizzare il traffico e proteggere l'ambiente. Con il proxy applicazione non è necessario disporre di una rete perimetrale perché tutte le connessioni sono in uscita e su un canale sicuro.

Per altre informazioni sui connettori, vedere [Understand Azure AD Application Proxy connectors](application-proxy-connectors.md) (Informazioni sui connettori proxy di applicazione di Azure AD).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Machine Learning e analisi di livello cloud 

Ottenere una protezione all'avanguardia.

Dal momento che fa parte di Azure Active Directory, il proxy applicazione può sfruttare [Azure AD Identity Protection](../active-directory-identityprotection.md) con i dati provenienti da Microsoft Security Response Center e dalla Digital Crimes Unit. Insieme si identificano in modo proattivo gli account compromessi e si offre la protezione dagli accessi ad alto rischio. Vengono presi in considerazione numerosi fattori per determinare quali tentativi di accesso sono ad alto rischio. Questi fattori includono l'uso di flag per contrassegnare i dispositivi infettati, l'anonimizzazione delle reti e i percorsi atipici o improbabili.

Molti di questi eventi e segnalazioni sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM (Security Information and Event Management, Sistema di gestione delle informazioni e degli eventi di sicurezza).

### <a name="remote-access-as-a-service"></a>Accesso remoto come servizio

Non è necessario preoccuparsi di mantenere e applicare patch ai server locali.

Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Il proxy di applicazione di Azure AD è un servizio Internet di Microsoft e, quindi, sarà sempre possibile ottenere le patch di sicurezza e gli aggiornamenti più recenti.

Per migliorare la sicurezza delle applicazioni pubblicate dal proxy applicazione di Azure AD, vengono bloccate l'indicizzazione e l'archiviazione delle applicazioni da parte dei robot dell'agente di ricerca Web. Ogni volta che un robot dell'agente di ricerca Web prova a recuperare le impostazioni dei robot per un'app pubblicata, il proxy applicazione risponde con un file robots.txt che include `User-agent: * Disallow: /`.

#### <a name="azure-ddos-protection-service"></a>Servizio protezione DDoS di Azure

Le applicazioni pubblicate tramite il proxy di applicazione sono protette da attacchi DDoS (Distributed Denial of Service). **Protezione DDoS di Azure** è un servizio offerto con la piattaforma Azure per proteggere le risorse di Azure da attacchi di tipo Denial of Service. Il livello di servizio **Basic** viene abilitato automaticamente, garantendo il monitoraggio del traffico always on e la mitigazione in tempo reale degli attacchi comuni a livello di rete. È disponibile anche un livello **standard** , che offre funzionalità aggiuntive di mitigazione ottimizzate in modo specifico per le risorse di rete virtuale di Azure. Per informazioni dettagliate, vedere [Panoramica di protezione DDoS di Azure standard](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

## <a name="under-the-hood"></a>dietro le quinte

Il proxy applicazione di Azure AD è costituito da due parti:

* Servizio basato sul cloud: servizio eseguito in Azure in cui vengono stabilite le connessioni esterne client/utente.
* [Connettore locale](application-proxy-connectors.md): componente locale in ascolto delle richieste provenienti dal servizio proxy di applicazione di Azure AD e in grado di gestire le connessioni alle applicazioni interne. 

Un flusso tra il connettore e il servizio proxy applicazione viene stabilito quando:

* Il connettore viene configurato per la prima volta.
* Il connettore reperisce le informazioni di configurazione dal servizio proxy di applicazione.
* Un utente accede a un'applicazione pubblicata.

>[!NOTE]
>Tutte le comunicazioni avvengono tramite TLS e hanno sempre origine nel connettore per il servizio proxy di applicazione. Il servizio è solo in uscita.

Il connettore usa un certificato client per l'autenticazione al servizio proxy applicazione per quasi tutte le chiamate. L'unica eccezione a questo processo è il passaggio di configurazione iniziale in cui viene stabilito il certificato client.

### <a name="installing-the-connector"></a>Installazione del connettore

Quando il connettore viene configurato per la prima volta, si verificano gli eventi di flusso seguenti:

1. La registrazione del connettore al servizio avviene durante l'installazione del connettore. Agli utenti viene chiesto di immettere le credenziali di amministratore di Azure AD.Il token acquisito dalla procedura di autenticazione viene quindi presentato al servizio proxy di applicazione di Azure AD.
2. Il servizio proxy di applicazione valuta il token Controlla se l'utente è un amministratore dell'azienda nel tenant.Se l'utente non è un amministratore, il processo viene terminato.
3. Il connettore genera una richiesta di certificato client e la passa con il token al servizio proxy di applicazione, che a sua volta verifica il token e firma la richiesta del certificato client.
4. Il connettore usa questo certificato client per la futura comunicazione con il servizio proxy applicazione.
5. Il connettore esegue un pull iniziale dei dati di configurazione del sistema dal servizio usando il certificato client ed è quindi pronto ad accettare le richieste.

### <a name="updating-the-configuration-settings"></a>Aggiornamento delle impostazioni di configurazione

Ogni volta che il servizio proxy applicazione aggiorna le impostazioni di configurazione, si verificano gli eventi di flusso seguenti:

1. Il connettore si connette all'endpoint di configurazione all'interno del servizio proxy applicazione usando il suo certificato client.
2. Dopo che il certificato client è stato convalidato, il servizio proxy applicazione restituisce i dati di configurazione al connettore, ad esempio il gruppo di connettori di cui fa parte il connettore.
3. Se il certificato corrente ha più di 180 giorni, il connettore genera una nuova richiesta di certificato, aggiornando di fatto il certificato client ogni 180 giorni.

### <a name="accessing-published-applications"></a>Accesso alle applicazioni pubblicate

Quando gli utenti accedono a un'applicazione pubblicata, tra il servizio proxy di applicazione e il connettore del proxy di applicazione si verificano gli eventi seguenti:

1. Il servizio autentica l'utente per l'app
2. Il servizio inserisce la richiesta nella coda del connettore
3. Un connettore elabora la richiesta ricevuta dalla coda
4. Il connettore attende una risposta
5. Il servizio trasmette i dati all'utente

Per altre informazioni sugli eventi che si verificano in ognuno di questi passaggi, continuare la lettura.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. il servizio autentica l'utente per l'app

Se l'app è configurata per l'utilizzo di PassThrough come metodo di autenticazione, gli eventi descritti in questa sezione non si verificano.

Se l'app è configurata per eseguire la pre-autenticazione con Azure AD, l'utente viene reindirizzato al servizio token di sicurezza di Azure AD per eseguire l'autenticazione e si verificano gli eventi seguenti:

1. Il proxy di applicazione controlla i requisiti dei criteri di accesso condizionale per l'applicazione specifica. Questo passaggio verifica che l'utente sia stato assegnato all'applicazione. Se è necessaria la verifica in due passaggi, la sequenza di autenticazione richiede all'utente di usare un secondo metodo di autenticazione.

2. Dopo avere superato tutti i controlli, il servizio token di sicurezza di Azure AD rilascia un token firmato per l'applicazione e l'utente viene reindirizzato al servizio proxy applicazione.

3. Il proxy applicazione verifica che il token sia stato rilasciato per correggere l'applicazione. Vengono eseguiti anche altri controlli, come verificare che il token sia stato firmato da Azure AD e si trovi ancora nella finestra valida.

4. Il proxy applicazione imposta un cookie di autenticazione crittografato per indicare che l'autenticazione all'applicazione è stata completata. Questo cookie include un timestamp di scadenza basato sul token di Azure AD e su altri dati, ad esempio il nome utente su cui si basa l'autenticazione. Questo cookie viene crittografato con una chiave privata nota solo al servizio proxy applicazione.

5. Il proxy applicazione reindirizza l'utente all'URL originariamente richiesto.

Se qualsiasi parte dei passaggi di preautenticazione non ha esito positivo, la richiesta dell'utente viene negata e viene visualizzato un messaggio che indica l'origine del problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. il servizio inserisce una richiesta nella coda del connettore

I connettori mantengono aperta una connessione in uscita diretta al servizio proxy di applicazione. Quando viene ricevuta una richiesta, il sevizio la mette in coda su una delle connessioni aperte affinché il connettore la accetti.

La richiesta include elementi dell'applicazione, come le intestazioni della richiesta e i dati del cookie crittografato, l'utente che effettua la richiesta e l'ID richiesta. Con la richiesta vengono inviati i dati del cookie crittografato, non il cookie di autenticazione.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. il connettore elabora la richiesta dalla coda. 

A seconda della richiesta, il proxy applicazione eseguirà una delle azioni seguenti:

* Se la richiesta è un'operazione semplice, ad esempio non sono presenti dati all'interno del corpo come avviene in una richiesta RESTful *GET*, il connettore esegue una connessione alla risorsa interna di destinazione e attende una risposta.

* Se la richiesta contiene dati associati nel corpo, ad esempio un'operazione RESTful *POST*, il connettore esegue una connessione in uscita usando il certificato client verso l'istanza del proxy applicazione. Esegue questa connessione per richiedere i dati e aprire una connessione alla risorsa interna. Dopo la ricezione della richiesta dal connettore, il servizio proxy applicazione inizia ad accettare contenuto dall'utente e inoltra i dati al connettore. Il connettore, a sua volta, inoltra i dati alla risorsa interna.

#### <a name="4-the-connector-waits-for-a-response"></a>4. il connettore attende una risposta.

Dopo aver completato la richiesta e la trasmissione di tutto il contenuto al back-end, il connettore attende una risposta.

Dopo aver ricevuto la risposta, il connettore esegue una connessione in uscita al servizio proxy applicazione per restituire i dettagli dell'intestazione e avviare il flusso dei dati restituiti.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. il servizio trasmette i dati all'utente. 

In questa fase è possibile che vengano eseguite alcune operazioni di elaborazione dell'applicazione. Se è il proxy di applicazione è stato configurato in modo da convertire le intestazioni o gli URL nell'applicazione, in questa fase vengono eseguite tutte le operazioni di elaborazione necessarie.


## <a name="next-steps"></a>Passaggi successivi

[Considerazioni relative alla topologia di rete quando si usa il proxy applicazione Azure AD](application-proxy-network-topology.md)

[Informazioni sui connettori del proxy di applicazione Azure AD](application-proxy-connectors.md)
