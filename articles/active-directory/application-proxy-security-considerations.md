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
ms.date: 04/12/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 52704b3259ddad85db12b8bb81c4992ece2f37a4
ms.contentlocale: it-it
ms.lasthandoff: 04/21/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD

Questo articolo spiega come il proxy applicazione di Azure Active Directory (Azure AD) fornisca un servizio sicuro per la pubblicazione e l'accesso alle applicazioni in remoto.

Il diagramma seguente illustra in che modo Azure AD consente l'accesso remoto sicuro alle applicazioni locali.

 ![Diagramma dell'accesso remoto sicuro con il proxy applicazione di Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

Il proxy applicazione di Azure AD offre i vantaggi di sicurezza seguenti:

**Accesso autenticato:** solo le connessioni autenticate possono accedere alla rete.

* Il proxy applicazione di Azure AD fa affidamento sul servizio token di sicurezza di Azure AD per tutta l'autenticazione.  La preautenticazione, per sua natura, blocca un numero significativo di attacchi anonimi perché solo le identità autenticate possono accedere all'applicazione back-end.

**Accesso condizionale:** applicare controlli dei criteri più completi prima che vengano stabilite connessioni alla rete.

* Con l'[accesso condizionale](active-directory-conditional-access-azuread-connected-apps.md) è possibile definire restrizioni relative a quale traffico è consentito l'accesso alle applicazioni back-end, in base alla posizione, il livello di autenticazione e il profilo di rischio utente.

**Terminazione del traffico:** tutto il traffico viene terminato nel cloud.

* Il proxy applicazione di Azure AD è un proxy inverso, quindi tutto il traffico verso le applicazioni back-end viene terminato nel servizio. La sessione può essere ristabilita solo con il server back-end e ciò significa che i server back-end non sono esposti al traffico HTTP diretto. Ad esempio è possibile mitigare più facilmente gli attacchi.

**Tutti gli accessi sono in uscita:** non è necessario aprire alcuna connessione in ingresso alla rete aziendale.

* I connettori di Azure AD mantengono le connessioni in uscita per il servizio proxy di applicazione di Azure AD, quindi non è necessario aprire porte del firewall per le connessioni in ingresso. I proxy tradizionali richiedono una ***rete perimetrale***e consentono l'accesso a connessioni non autenticate al perimetro della rete. Questo scenario determinava la necessità di molti altri investimenti in prodotti di Web application firewall (WAF) per analizzare il traffico e offrire protezione aggiunta all'ambiente. Con il proxy applicazione non è necessario disporre di una rete perimetrale perché tutte le connessioni sono in uscita e su un canale sicuro.

**Analisi di sicurezza e intelligence basata su Machine Learning:** protezione all'avanguardia.

* [Azure AD Identity Protection](active-directory-identityprotection.md) con intelligence basata su Machine Learning e feed di dati provenienti dalla Digital Crimes Unit e da Microsoft Security Response Center. Insieme identifichiamo in modo proattivo gli account compromessi e offriamo una protezione in tempo reale contro gli accessi ad alto rischio. Vengono presi in considerazione diversi fattori, ad esempio l'accesso da dispositivi infetti e tramite reti anonime, da posizioni atipiche e improbabili.
* Molti di questi eventi e segnalazioni sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM (Security Information and Event Management, Sistema di gestione delle informazioni e degli eventi di sicurezza).

**Accesso remoto come servizio:** non è necessario preoccuparsi di mantenere e applicare patch ai server locali.

* Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Il proxy applicazione di Azure AD è un servizio Internet di Microsoft, quindi si otterranno sempre le patch di sicurezza e gli aggiornamenti più recenti.
* Per migliorare la sicurezza delle applicazioni pubblicate dal proxy applicazione di Azure AD, vengono bloccate l'indicizzazione e l'archiviazione delle applicazioni da parte dei robot dell'agente di ricerca Web. Ogni volta che un robot dell'agente di ricerca Web prova a recuperare le impostazioni dei robot per un'app pubblicata, il proxy applicazione risponde con un file robots.txt che include `User-agent: * Disallow: /`.

## <a name="under-the-hood"></a>Dietro le quinte

Il proxy applicazione di Azure AD è costituito da due parti:

* Servizio basato sul cloud: servizio in cui vengono eseguite le connessioni esterne client/utente.
* Connettore proxy applicazione di Azure AD: si tratta di un componente locale in ascolto per le richieste provenienti dal servizio proxy applicazione di Azure AD e che gestisce le connessioni alle applicazioni interne. 

Un flusso tra il connettore e il servizio proxy applicazione viene stabilito quando:

* Il connettore viene configurato per la prima volta.
* Il connettore reperisce le informazioni di configurazione dal servizio proxy applicazione, incluso il gruppo di cui ogni connettore è membro.
* Un utente accede a un'applicazione pubblicata.

>[!NOTE]
>Tutte le comunicazioni si verificano su SSL e hanno sempre origine dal connettore verso il servizio proxy applicazione. Il servizio è solo in uscita.

Il connettore usa un certificato client per l'autenticazione al servizio proxy applicazione per quasi tutte le chiamate. L'unica eccezione a questo processo è il passaggio di configurazione iniziale in cui viene stabilito il certificato client.

### <a name="installing-the-connector"></a>Installazione del connettore

Quando il connettore viene configurato per la prima volta, si verificano gli eventi di flusso seguenti:

1. La registrazione del connettore al servizio avviene durante l'installazione del connettore. Attualmente agli utenti viene chiesto di immettere le credenziali di amministratore di Azure AD. Il token acquisito viene quindi presentato al servizio proxy applicazione di Azure AD.
2. Il proxy applicazione valuta il token per verificare che l'utente sia membro del ruolo di amministratore aziendale nel tenant per cui è stato rilasciato il token. Se l'utente non è un membro del ruolo di amministratore, il processo viene terminato.
3. Il connettore genera una richiesta di certificato client e la passa con il token al servizio proxy applicazione che a sua volta verifica il token e firma la richiesta del certificato client.
4. Il connettore usa questo certificato client per la futura comunicazione con il servizio proxy applicazione.
5. Il connettore esegue un pull iniziale dei dati di configurazione del sistema dal servizio usando il certificato client ed è quindi pronto ad accettare le richieste.

### <a name="updating-the-configuration-settings"></a>Aggiornamento delle impostazioni di configurazione

Ogni volta che il servizio proxy applicazione aggiorna le impostazioni di configurazione, si verificano gli eventi di flusso seguenti:

1. Il connettore si connette all'endpoint di configurazione all'interno del servizio proxy applicazione usando il suo certificato client.
2. Dopo che il certificato client è stato convalidato, il servizio proxy applicazione restituisce i dati di configurazione al connettore, ad esempio il gruppo di connettori di cui il connettore deve far parte.
3. Se il certificato corrente ha più di 30 giorni, il connettore genera una nuova richiesta di certificato, sostituendo di fatto il certificato client ogni 30 giorni.

### <a name="accessing-published-applications"></a>Accesso alle applicazioni pubblicate

Quando gli utenti accedono a un'applicazione pubblicata, si verificano gli eventi di flusso seguenti:

#### <a name="the-application-proxy-service-checks-the-configuration-settings-for-the-app"></a>Il servizio proxy applicazione verifica le impostazioni di configurazione per l'app. 

Se l'app è configurata per l'uso della preautenticazione con Azure AD, l'utente viene reindirizzato al servizio token di sicurezza di Azure AD per l'autenticazione. Questo passaggio viene ignorato se si configura l'app usando il pass-through.

1. Il proxy applicazione verifica gli eventuali requisiti dei criteri di accesso condizionale per l'applicazione specifica. Questo passaggio verifica che l'utente sia stato assegnato all'applicazione. Se è necessaria la verifica in due passaggi, la sequenza di autenticazione richiede all'utente di usare un secondo metodo di autenticazione.

2. Dopo avere superato tutti i controlli, il servizio token di sicurezza di Azure AD rilascia un token firmato per l'applicazione e l'utente viene reindirizzato al servizio proxy applicazione.

3. Il proxy applicazione verifica che il token sia stato rilasciato per correggere l'applicazione. Vengono eseguiti anche altri controlli, come verificare che il token sia stato firmato da Azure AD e si trovi ancora nella finestra valida.

4. Il proxy applicazione imposta un cookie di autenticazione crittografato per indicare che l'autenticazione all'applicazione è stata completata. Questo cookie include un timestamp di scadenza basato sul token di Azure AD e su altri dati, ad esempio il nome utente su cui si basa l'autenticazione. Questo cookie viene crittografato con una chiave privata nota solo al servizio proxy applicazione.

5. Il proxy applicazione reindirizza l'utente all'URL originariamente richiesto.

Se qualsiasi parte dei passaggi di preautenticazione non ha esito positivo, la richiesta dell'utente viene negata e viene visualizzato un messaggio che indica l'origine del problema.


#### <a name="application-proxy-places-a-request-in-the-appropriate-queue-for-an-on-premises-connector-to-handle"></a>Il proxy applicazione inserisce quindi una richiesta nella coda appropriata perché un connettore locale possa gestirla. 

Tutte le richieste provenienti dal connettore sono in uscita verso il servizio proxy applicazione. I connettori mantengono aperta una connessione in uscita al proxy applicazione. Quando viene ricevuta una richiesta, il proxy applicazione la mette in coda su una delle connessioni aperte perché il connettore la accetti.

La richiesta include elementi dell'applicazione, come le intestazioni della richiesta e i dati del cookie crittografato, l'utente che effettua la richiesta e l'ID richiesta. Il cookie di autenticazione crittografato non viene tuttavia inviato al connettore.

#### <a name="the-connector-receives-the-request-from-the-queue"></a>Il connettore riceve la richiesta dalla coda. 

A seconda della richiesta, il proxy applicazione eseguirà una delle azioni seguenti:

* Il connettore conferma se può identificare l'applicazione. Se non può identificare l'applicazione, il connettore stabilisce una connessione al servizio proxy applicazione per raccogliere dettagli sull'applicazione e memorizza l'applicazione nella cache locale.

* Se la richiesta è un'operazione semplice, ad esempio non sono presenti dati all'interno del corpo come avviene in una richiesta RESTful *GET*, il connettore esegue una connessione alla risorsa interna di destinazione e attende una risposta.

* Se la richiesta contiene dati associati nel corpo, ad esempio un'operazione RESTful *POST*, il connettore esegue una connessione in uscita usando il certificato client verso l'istanza del proxy applicazione. Esegue questa connessione per richiedere i dati e aprire una connessione alla risorsa interna. Dopo la ricezione della richiesta dal connettore, il servizio proxy applicazione inizia ad accettare contenuto dall'utente e inoltra i dati al connettore. Il connettore, a sua volta, inoltra i dati alla risorsa interna.

#### <a name="after-the-request-and-transmission-of-all-content-to-the-back-end-is-complete-the-connector-waits-for-a-response"></a>Dopo aver completato la richiesta e la trasmissione di tutto il contenuto al back-end, il connettore attende una risposta.

Dopo aver ricevuto la risposta, il connettore esegue una connessione in uscita al servizio proxy applicazione per restituire i dettagli dell'intestazione e avviare il flusso dei dati restituiti.

#### <a name="application-proxy-streams-the-data-to-the-user"></a>Il proxy applicazione invia i dati all'utente in un flusso. 

In questa fase è possibile che siano eseguite alcune operazioni di elaborazione delle intestazioni, in base alle esigenze e a quanto definito dall'applicazione.


## <a name="next-steps"></a>Passaggi successivi

[Considerazioni relative alla topologia di rete quando si usa il proxy applicazione di Azure AD](application-proxy-network-topology-considerations.md)

[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)

