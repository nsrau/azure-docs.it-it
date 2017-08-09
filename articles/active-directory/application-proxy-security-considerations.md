---
title: Considerazioni relative alla sicurezza per il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta considerazioni relative alla sicurezza quando si usa il proxy applicazione di Azure AD
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: f1ef6c3cc3ad2eda9fbcf79bf729918a847d27d7
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD

Questo articolo spiega come il proxy di applicazione di Azure Active Directory fornisca un servizio sicuro per la pubblicazione e l'accesso alle applicazioni in remoto.

Il diagramma seguente illustra in che modo Azure AD consente l'accesso remoto sicuro alle applicazioni locali.

 ![Diagramma dell'accesso remoto sicuro con il proxy applicazione di Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Vantaggi di sicurezza

Il proxy applicazione di Azure AD offre i vantaggi di sicurezza seguenti:

### <a name="authenticated-access"></a>Accesso autenticato 

Se si sceglie di usare la preautenticazione di Azure Active Directory, solo le connessioni autenticate possono accedere alla rete.

Il proxy applicazione di Azure AD fa affidamento sul servizio token di sicurezza di Azure AD per tutta l'autenticazione.  La preautenticazione, per sua natura, blocca un numero significativo di attacchi anonimi perché solo le identità autenticate possono accedere all'applicazione back-end.

Se si sceglie Pass-through come metodo di preautenticazione, non si otterrà questo vantaggio. 

### <a name="conditional-access"></a>Accesso condizionale

Applicare controlli dei criteri più completi prima che vengano stabilite connessioni alla rete.

Con l'[accesso condizionale](active-directory-conditional-access-azuread-connected-apps.md) è possibile definire restrizioni sul tipo di traffico che può accedere alle applicazioni back-end. È possibile, ad esempio, creare criteri per definire restrizioni in base alla posizione, al livello di autenticazione e al profilo di rischio.

È possibile usare l'accesso condizionale anche per configurare criteri di autenticazione a più fattori e aggiungere così un altro livello di sicurezza alle autenticazioni utente. 

### <a name="traffic-termination"></a>Terminazione di traffico

Tutto il traffico viene terminato nel cloud.

Il proxy applicazione di Azure AD è un proxy inverso, quindi tutto il traffico verso le applicazioni back-end viene terminato nel servizio. La sessione può essere ristabilita solo con il server back-end e ciò significa che i server back-end non sono esposti al traffico HTTP diretto. Questa configurazione consente una protezione migliore da attacchi mirati.

### <a name="all-access-is-outbound"></a>Tutti gli accessi sono in uscita 

Non è necessario aprire alcuna connessione in ingresso nella rete aziendale.

I connettori di Azure AD usano solo connessioni in uscita per il servizio proxy di applicazione di Azure AD e, pertanto, non è necessario aprire le porte del firewall per le connessioni in ingresso. I proxy tradizionali richiedono una ***rete perimetrale***e consentono l'accesso a connessioni non autenticate al perimetro della rete. Questo scenario richiedeva molti altri investimenti in prodotti Web Application Firewall per analizzare il traffico e offrire protezione aggiuntiva all'ambiente. Con il proxy applicazione non è necessario disporre di una rete perimetrale perché tutte le connessioni sono in uscita e su un canale sicuro.

### <a name="cloud-scale-analytics-and-machine-learning"></a>Machine Learning e analisi di livello cloud 

Ottenere una protezione all'avanguardia.

[Azure AD Identity Protection](active-directory-identityprotection.md) con intelligence basata su Machine Learning e feed di dati provenienti dalla Digital Crimes Unit e da Microsoft Security Response Center. Insieme identifichiamo in modo proattivo gli account compromessi e offriamo una protezione in tempo reale contro gli accessi ad alto rischio. Vengono presi in considerazione diversi fattori, ad esempio l'accesso da dispositivi infetti e tramite reti anonime, da posizioni atipiche e improbabili.

Molti di questi eventi e segnalazioni sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM (Security Information and Event Management, Sistema di gestione delle informazioni e degli eventi di sicurezza).

### <a name="remote-access-as-a-service"></a>Accesso remoto come servizio

Non è necessario preoccuparsi di mantenere e applicare patch ai server locali.

Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Il proxy di applicazione di Azure AD è un servizio Internet di Microsoft e, quindi, sarà sempre possibile ottenere le patch di sicurezza e gli aggiornamenti più recenti.

Per migliorare la sicurezza delle applicazioni pubblicate dal proxy applicazione di Azure AD, vengono bloccate l'indicizzazione e l'archiviazione delle applicazioni da parte dei robot dell'agente di ricerca Web. Ogni volta che un robot dell'agente di ricerca Web prova a recuperare le impostazioni dei robot per un'app pubblicata, il proxy applicazione risponde con un file robots.txt che include `User-agent: * Disallow: /`.

## <a name="under-the-hood"></a>Dietro le quinte

Il proxy applicazione di Azure AD è costituito da due parti:

* Servizio basato sul cloud: servizio eseguito in Azure in cui vengono stabilite le connessioni esterne client/utente.
* [Connettore locale](application-proxy-understand-connectors.md): componente locale in ascolto delle richieste provenienti dal servizio proxy di applicazione di Azure AD e in grado di gestire le connessioni alle applicazioni interne. 

Un flusso tra il connettore e il servizio proxy applicazione viene stabilito quando:

* Il connettore viene configurato per la prima volta.
* Il connettore reperisce le informazioni di configurazione dal servizio proxy di applicazione.
* Un utente accede a un'applicazione pubblicata.

>[!NOTE]
>Tutte le comunicazioni si verificano su SSL e hanno sempre origine dal connettore verso il servizio proxy applicazione. Il servizio è solo in uscita.

Il connettore usa un certificato client per l'autenticazione al servizio proxy applicazione per quasi tutte le chiamate. L'unica eccezione a questo processo è il passaggio di configurazione iniziale in cui viene stabilito il certificato client.

### <a name="installing-the-connector"></a>Installazione del connettore

Quando il connettore viene configurato per la prima volta, si verificano gli eventi di flusso seguenti:

1. La registrazione del connettore al servizio avviene durante l'installazione del connettore. Agli utenti viene chiesto di immettere le credenziali di amministratore di Azure AD. Il token acquisito dalla procedura di autenticazione viene quindi presentato al servizio proxy di applicazione di Azure AD.
2. Il servizio proxy di applicazione valuta il token per verificare che l'utente sia un amministratore aziendale nel tenant per cui è stato rilasciato il token. Se l'utente non è un amministratore, il processo viene terminato.
3. Il connettore genera una richiesta di certificato client e la passa con il token al servizio proxy di applicazione, che a sua volta verifica il token e firma la richiesta del certificato client.
4. Il connettore usa questo certificato client per la futura comunicazione con il servizio proxy applicazione.
5. Il connettore esegue un pull iniziale dei dati di configurazione del sistema dal servizio usando il certificato client ed è quindi pronto ad accettare le richieste.

### <a name="updating-the-configuration-settings"></a>Aggiornamento delle impostazioni di configurazione

Ogni volta che il servizio proxy applicazione aggiorna le impostazioni di configurazione, si verificano gli eventi di flusso seguenti:

1. Il connettore si connette all'endpoint di configurazione all'interno del servizio proxy applicazione usando il suo certificato client.
2. Dopo che il certificato client è stato convalidato, il servizio proxy applicazione restituisce i dati di configurazione al connettore, ad esempio il gruppo di connettori di cui il connettore deve far parte.
3. Se il certificato corrente ha più di 180 giorni, il connettore genera una nuova richiesta di certificato, aggiornando di fatto il certificato client ogni 180 giorni.

### <a name="accessing-published-applications"></a>Accesso alle applicazioni pubblicate

Quando gli utenti accedono a un'applicazione pubblicata, tra il servizio proxy di applicazione e il connettore del proxy di applicazione si verificano gli eventi seguenti:

1. [Il servizio verifica le impostazioni di configurazione per l'app](#the-service-checks-the-configuration-settings-for-the-app)
2. [Il servizio inserisce la richiesta nella coda del connettore](#The-service-places-a-request-in-the-connector-queue)
3. [Un connettore elabora la richiesta ricevuta dalla coda](#the-connector-receives-the-request-from-the-queue)
4. [Il connettore attende una risposta](#the-connector-waits-for-a-response)
5. [Il servizio invia i dati all'utente](#the-service-streams-data-to-the-user)

Per altre informazioni sugli eventi che si verificano in ognuno di questi passaggi, continuare la lettura.


#### <a name="1-the-service-checks-the-configuration-settings-for-the-app"></a>1. Il servizio verifica le impostazioni di configurazione per l'app

Se l'app è configurata per l'utilizzo di PassThrough come metodo di autenticazione, gli eventi descritti in questa sezione non si verificano.

Se l'app è configurata per eseguire la pre-autenticazione con Azure AD, l'utente viene reindirizzato al servizio token di sicurezza di Azure AD per eseguire l'autenticazione e si verificano gli eventi seguenti:

1. Il proxy applicazione verifica gli eventuali requisiti dei criteri di accesso condizionale per l'applicazione specifica. Questo passaggio verifica che l'utente sia stato assegnato all'applicazione. Se è necessaria la verifica in due passaggi, la sequenza di autenticazione richiede all'utente di usare un secondo metodo di autenticazione.

2. Dopo avere superato tutti i controlli, il servizio token di sicurezza di Azure AD rilascia un token firmato per l'applicazione e l'utente viene reindirizzato al servizio proxy applicazione.

3. Il proxy applicazione verifica che il token sia stato rilasciato per correggere l'applicazione. Vengono eseguiti anche altri controlli, come verificare che il token sia stato firmato da Azure AD e si trovi ancora nella finestra valida.

4. Il proxy applicazione imposta un cookie di autenticazione crittografato per indicare che l'autenticazione all'applicazione è stata completata. Questo cookie include un timestamp di scadenza basato sul token di Azure AD e su altri dati, ad esempio il nome utente su cui si basa l'autenticazione. Questo cookie viene crittografato con una chiave privata nota solo al servizio proxy applicazione.

5. Il proxy applicazione reindirizza l'utente all'URL originariamente richiesto.

Se qualsiasi parte dei passaggi di preautenticazione non ha esito positivo, la richiesta dell'utente viene negata e viene visualizzato un messaggio che indica l'origine del problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Il servizio inserisce la richiesta nella coda del connettore

I connettori mantengono aperta una connessione in uscita diretta al servizio proxy di applicazione. Quando viene ricevuta una richiesta, il sevizio la mette in coda su una delle connessioni aperte affinché il connettore la accetti.

La richiesta include elementi dell'applicazione, come le intestazioni della richiesta e i dati del cookie crittografato, l'utente che effettua la richiesta e l'ID richiesta. Con la richiesta vengono inviati i dati del cookie crittografato, non il cookie di autenticazione.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Il connettore elabora la richiesta ricevuta dalla coda. 

A seconda della richiesta, il proxy applicazione eseguirà una delle azioni seguenti:

* Se la richiesta è un'operazione semplice, ad esempio non sono presenti dati all'interno del corpo come avviene in una richiesta RESTful *GET*, il connettore esegue una connessione alla risorsa interna di destinazione e attende una risposta.

* Se la richiesta contiene dati associati nel corpo, ad esempio un'operazione RESTful *POST*, il connettore esegue una connessione in uscita usando il certificato client verso l'istanza del proxy applicazione. Esegue questa connessione per richiedere i dati e aprire una connessione alla risorsa interna. Dopo la ricezione della richiesta dal connettore, il servizio proxy applicazione inizia ad accettare contenuto dall'utente e inoltra i dati al connettore. Il connettore, a sua volta, inoltra i dati alla risorsa interna.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Il connettore attende una risposta.

Dopo aver completato la richiesta e la trasmissione di tutto il contenuto al back-end, il connettore attende una risposta.

Dopo aver ricevuto la risposta, il connettore esegue una connessione in uscita al servizio proxy applicazione per restituire i dettagli dell'intestazione e avviare il flusso dei dati restituiti.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Il servizio invia i dati all'utente. 

In questa fase è possibile che vengano eseguite alcune operazioni di elaborazione dell'applicazione. Se è il proxy di applicazione è stato configurato in modo da convertire le intestazioni o gli URL nell'applicazione, in questa fase vengono eseguite tutte le operazioni di elaborazione necessarie.


## <a name="next-steps"></a>Passaggi successivi

[Considerazioni relative alla topologia di rete quando si usa il proxy applicazione di Azure AD](application-proxy-network-topology-considerations.md)

[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)

