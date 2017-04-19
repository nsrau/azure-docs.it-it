---
title: Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD | Microsoft Docs
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 4019b379939e43e4e974552f1f0be245c5b1d33e
ms.lasthandoff: 04/17/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-by-using-azure-ad-application-proxy"></a>Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD

>[!NOTE]
>Il proxy applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Questo articolo spiega come il proxy applicazione di Azure Active Directory (Azure AD) fornisca un servizio sicuro per la pubblicazione e l'accesso alle applicazioni in remoto.

Il proxy applicazione Azure AD offre i vantaggi di sicurezza seguenti:

**Accesso autenticato:** solo le connessioni autenticate possono accedere alla rete.

* Il proxy applicazione di Azure AD fa affidamento sul servizio token di sicurezza di Azure AD per tutta l'autenticazione. Per le applicazioni pubblicate con preautenticazione, non è consentito il passaggio di alcun traffico dal servizio proxy applicazione all'ambiente senza un token valido del servizio token di sicurezza.
* La preautenticazione, per sua natura, blocca un numero significativo di attacchi anonimi perché solo le identità autenticate possono accedere all'applicazione back-end.

**Accesso condizionale:** applicare controlli dei criteri più completi prima che vengano stabilite connessioni alla rete.

* Con l'accesso condizionale è possibile definire restrizioni più complete per determinare il traffico che può accedere alle applicazioni back-end. È possibile definire restrizioni in base alla posizione, al livello di autenticazione e al profilo di rischio.
* Questa funzionalità consente di creare barriere aggiuntive per gli utenti malintenzionati. Per altre informazioni sull'accesso condizionale, vedere [Introduzione all'accesso condizionale di Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-conditional-access-azuread-connected-apps).

**Terminazione del traffico:** tutto il traffico viene terminato nel cloud.

* Il proxy applicazione di Azure AD è un proxy inverso, quindi tutto il traffico verso le applicazioni back-end viene terminato nel servizio. La sessione può essere ristabilita solo con il server back-end e ciò significa che i server back-end non sono esposti al traffico HTTP diretto. Ad esempio è possibile mitigare più facilmente gli attacchi.

**Tutti gli accessi sono in uscita:** non è necessario aprire alcuna connessione in ingresso alla rete aziendale.

* I connettori di Azure AD mantengono le connessioni in uscita per il servizio proxy di applicazione di Azure AD, quindi non è necessario aprire porte del firewall per le connessioni in ingresso.
* Gli approcci tradizionali richiedevano una rete perimetrale, nota anche come *DMZ*****, e l'apertura di un accesso a connessioni non autenticate al perimetro della rete. Questo scenario determinava la necessità di molti altri investimenti in prodotti di Web application firewall (WAF) per analizzare il traffico e offrire protezione aggiunta all'ambiente. Con il proxy applicazione è possibile evitare questo scenario. È anche possibile prendere in considerazione di operare senza rete perimetrale perché tutte le connessioni sono in uscita e su un canale sicuro.

**Analisi di sicurezza e intelligence basata su Machine Learning:** protezione all'avanguardia.

* Azure AD Identity Protection con intelligence basata su Machine Learning e feed di dati provenienti dalla Digital Crimes Unit e da Microsoft Security Response Center. Insieme identifichiamo in modo proattivo gli account compromessi e offriamo una protezione in tempo reale contro gli accessi ad alto rischio. Vengono presi in considerazione diversi fattori, ad esempio l'accesso da dispositivi infetti e tramite reti anonime, da posizioni atipiche e improbabili.
* Molti di questi eventi e segnalazioni sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM (Security Information and Event Management, Sistema di gestione delle informazioni e degli eventi di sicurezza).
* Per altre informazioni, vedere [Azure AD Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection).

**Accesso remoto come servizio:** non è necessario preoccuparsi di mantenere e applicare patch ai server locali.

* Il proxy applicazione di Azure AD è un servizio Internet di Microsoft, quindi si otterranno sempre le patch di sicurezza e gli aggiornamenti più recenti.
* Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Con il modello di servizio di Microsoft non è necessario farsi carico della gestione di più server perimetrali.

I servizi di accesso remoto forniti con Azure AD operano in base alle linee guida e agli standard indicati nel [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center).

Il diagramma seguente illustra in che modo Azure AD consente l'accesso remoto sicuro alle applicazioni locali.

 ![Diagramma dell'accesso remoto sicuro con il proxy applicazione di Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

>[!NOTE]
>Per migliorare la sicurezza delle applicazioni pubblicate dal proxy applicazione di Azure AD, vengono bloccate l'indicizzazione e l'archiviazione delle applicazioni da parte dei robot dell'agente di ricerca Web. Ogni volta che un robot dell'agente di ricerca Web prova a recuperare le impostazioni dei robot per un'app pubblicata, il proxy dell'applicazione risponde con un file robots.txt che include il testo seguente:
>
>_User-agent: *_  
>_Disallow: /_

## <a name="components-of-the-azure-ad-application-proxy-solution"></a>Componenti della soluzione proxy applicazione di Azure AD

Il proxy applicazione Azure AD è costituito da due parti:

* Servizio basato sul cloud: servizio in cui vengono eseguite le connessioni esterne client/utente.
* Connettore proxy applicazione di Azure AD: si tratta di un componente locale in ascolto per le richieste provenienti dal servizio proxy applicazione di Azure AD e che gestisce le connessioni alle applicazioni interne. Il servizio comprende la gestione di elementi come la delega vincolata Kerberos (KCD) per l'accesso SSO.

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

1. Il servizio proxy applicazione verifica le impostazioni di configurazione per l'app. Se l'app è configurata per l'uso della preautenticazione con Azure AD, l'utente viene reindirizzato al servizio token di sicurezza di Azure AD per l'autenticazione. Questo passaggio viene ignorato se si pubblica l'app usando il pass-through.

 a. Durante l'autenticazione con Azure AD, il proxy applicazione verifica gli eventuali requisiti dei criteri di accesso condizionale per l'applicazione specifica. Questo passaggio consente di verificare che l'utente sia stato assegnato all'applicazione. Se è necessaria l'autenticazione a più fattori (MFA), la sequenza di autenticazione richiede all'utente l'autenticazione a due fattori.

 b. Dopo aver superato tutti i controlli, il servizio token di sicurezza di Azure AD rilascia un token firmato per l'applicazione e l'utente viene reindirizzato al servizio proxy applicazione.

 c. Il proxy applicazione convalida quindi il token per assicurarsi che sia stato emesso per l'applicazione a cui l'utente aveva richiesto l'accesso. Vengono eseguiti anche altri controlli, come verificare che il token sia stato firmato da Azure AD e si trovi ancora nella finestra valida.

 d. Il proxy applicazione imposta un cookie di autenticazione crittografato (ad esempio un cookie non persistente) per indicare che l'autenticazione per l'applicazione è stata completata. Questo cookie include un timestamp di scadenza basato sul token di Azure AD e su altri dati, ad esempio il nome utente su cui si basa l'autenticazione. Questo cookie viene crittografato con una chiave privata nota solo al servizio proxy applicazione.

 e. Il proxy applicazione reindirizza l'utente all'URL originariamente richiesto.

 >[!NOTE]
 >Se qualsiasi parte dei passaggi di preautenticazione non ha esito positivo, la richiesta dell'utente viene negata e viene visualizzato un messaggio che indica l'origine del problema.
 >

2. Dopo aver ricevuto la richiesta dal client, il proxy applicazione verifica che la condizione di preautenticazione sia stata soddisfatta e che il cookie sia ancora valido (in base alle esigenze). Il proxy applicazione inserisce quindi una richiesta nella coda appropriata perché un connettore locale possa gestirla. 

 >[!NOTE]
 >Tutte le richieste provenienti dal connettore sono in uscita verso il servizio proxy applicazione. I connettori mantengono aperta una connessione in uscita al proxy applicazione. Quando viene ricevuta una richiesta, il proxy applicazione la mette in coda su una delle connessioni aperte perché il connettore la accetti.

 * La richiesta include elementi dell'applicazione, come le intestazioni della richiesta e i dati del cookie crittografato, l'utente che effettua la richiesta e l'ID richiesta. Il cookie di autenticazione crittografato non viene tuttavia inviato al connettore.

3. Il connettore riceve la richiesta dalla coda, in base a una connessione in uscita di lunga durata. A seconda della richiesta, il proxy applicazione eseguirà una delle azioni seguenti:

 * Il connettore conferma se può identificare l'applicazione. Se non può identificare l'applicazione, il connettore stabilisce una connessione al servizio proxy applicazione per raccogliere dettagli sull'applicazione e memorizza l'applicazione nella cache locale.

 * Se la richiesta è un'operazione semplice, ad esempio non sono presenti dati all'interno del corpo come avviene in una richiesta RESTful *GET*, il connettore esegue una connessione alla risorsa interna di destinazione e attende una risposta.

 * Se la richiesta contiene dati associati nel corpo, ad esempio un'operazione RESTful *POST*, il connettore esegue una connessione in uscita usando il certificato client verso l'istanza del proxy applicazione. Esegue questa connessione per richiedere i dati e aprire una connessione alla risorsa interna. Dopo la ricezione della richiesta dal connettore, il servizio proxy applicazione inizia ad accettare contenuto dall'utente e inoltra i dati al connettore. Il connettore, a sua volta, inoltra i dati alla risorsa interna.

4. Dopo aver completato la richiesta e la trasmissione di tutto il contenuto al back-end, il connettore attende una risposta.

5. Dopo aver ricevuto la risposta, il connettore esegue una connessione in uscita al servizio proxy applicazione per restituire i dettagli dell'intestazione e avviare il flusso dei dati restituiti.

6. Il proxy applicazione invia i dati all'utente in un flusso. In questa fase è possibile che siano eseguite alcune operazioni di elaborazione delle intestazioni, in base alle esigenze e a quanto definito dall'applicazione.

Se è necessaria assistenza per la comunicazione da un'applicazione Web di Azure tramite un browser client verso un endpoint locale SOAP (Simple Object Access Protocol) autenticato in Windows, vedere [Azure Field Notes Blog](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy) (Blog delle note di campo su Azure).

## <a name="next-steps"></a>Passaggi successivi

[Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md)

