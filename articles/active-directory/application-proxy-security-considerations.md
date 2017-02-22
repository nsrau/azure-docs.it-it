---
title: Considerazioni relative alla sicurezza quando si accede alle app da remoto usando il proxy applicazione Azure AD | Documentazione Microsoft
description: Tratta considerazioni relative alla sicurezza quando si usa il proxy applicazione Azure AD.
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
ms.sourcegitcommit: b01d0ddef2271c440cbff1684c85d23dcda9a1de
ms.openlocfilehash: 75d657412a61de38ab53708a8108d40309de742d


---

# <a name="security-considerations-when-accessing-apps-remotely-using-azure-ad-application-proxy"></a>Considerazioni relative alla sicurezza quando si accede alle app da remoto usando il proxy applicazione Azure AD

>[!NOTE]
>Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
>  

Questo articolo spiega come il proxy applicazione Azure AD fornisca un servizio sicuro per la pubblicazione e l'accesso alle applicazioni da remoto. 

Il proxy applicazione Azure AD offre i vantaggi di sicurezza seguenti:

**Accesso autenticato:** solo le connessioni autenticate possono accedere alla rete

* Il proxy applicazione Azure AD fa affidamento sul servizio token di sicurezza di Azure AD per tutta l'autenticazione. Per le applicazioni pubblicate con preautenticazione, non è consentito il passaggio di alcun traffico dal servizio proxy applicazione all'ambiente senza un token valido del servizio token di sicurezza.
* La preautenticazione, per sua natura, blocca un numero significativo di attacchi anonimi, in quanto solo alle identità autenticate è consentito accedere all'applicazione back-end.

**Accesso condizionale:** applicare controlli dei criteri più completi prima che vengano stabilite connessioni alla rete

* Con l'accesso condizionale è possibile definire restrizioni più complete per determinare il traffico che può accedere alle applicazioni back-end. È possibile definire restrizioni in base alla posizione, al livello di autenticazione e al profilo di rischio.
* Questa funzionalità consente di creare barriere aggiuntive per gli utenti malintenzionati. Per altre informazioni sull'argomento, vedere [Introduzione all'accesso condizionale di Azure Active Directory](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps).

**Terminazione del traffico:** tutto il traffico viene terminato nel cloud

* Il proxy applicazione Azure AD è un proxy inverso, pertanto tutto il traffico verso le applicazioni back-end viene terminato nel servizio. La sessione può essere ristabilita solo con il server back-end. Ciò significa che i server back-end non sono esposti al traffico HTTP diretto. Ad esempio è possibile mitigare più facilmente gli attacchi.

**Tutti gli accessi sono in uscita:** non è necessario aprire alcuna connessione in ingresso alla rete aziendale

* I connettori Azure AD mantengono le connessioni in uscita al servizio proxy applicazione Azure AD. Ciò significa che non è necessario aprire porte del firewall per le connessioni in ingresso. 
* Gli approcci tradizionali richiedevano una rete perimetrale e l'apertura di un accesso a connessioni non autenticate al perimetro della rete. Ciò determinava la necessità di molti altri investimenti in prodotti WAF per analizzare il traffico e offrire protezione aggiunta all'ambiente. Con il proxy applicazione è possibile evitare questo scenario. È anche possibile prendere in considerazione di operare senza rete perimetrale, dato che tutte le connessioni sono in uscita e su un canale sicuro.

**Analisi di sicurezza e intelligence basata sul linguaggio macchina:** protezione all'avanguardia

* Protezione dell'identità di Azure AD con intelligence basata sul linguaggio macchina e feed di dati provenienti dalla Digital Crimes Unit e da Microsoft Security Response Center. Insieme identifichiamo in modo proattivo gli account compromessi e offriamo una protezione in tempo reale contro gli accessi ad alto rischio. Teniamo in considerazione diversi fattori, ad esempio l'accesso da dispositivi infetti e tramite reti anonime, da posizioni atipiche e improbabili.
* Molte di queste segnalazioni ed eventi sono già disponibili tramite un'API per l'integrazione con i sistemi SIEM.
* Per altre informazioni, vedere [Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection).

**Accesso remoto come servizio:** non è necessario preoccuparsi di mantenere e applicare patch ai server locali

* Il proxy applicazione Azure AD è un servizio su scala Internet che possediamo, pertanto si può avere la certezza di ottenere sempre le patch di sicurezza e gli aggiornamenti più recenti. Tutelare la sicurezza della rete è uno dei nostri scopi.
* Il software senza patch è tuttora responsabile di un numero elevato di attacchi. Con il nostro modello di servizio non è necessario farsi carico di gestire più server perimetrali. 

I servizi di accesso remoto forniti con Azure AD operano in base alle linee guida e agli standard indicati nel [Centro protezione di Azure](https://azure.microsoft.com/en-us/support/trust-center). 

Il diagramma seguente illustra in che modo Azure AD consente l'accesso remoto sicuro alle applicazioni locali.

 ![Connettori del proxy applicazione AzureAD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="components-of-the-azure-ad-app-proxy-solution"></a>Componenti della soluzione proxy applicazione Azure AD

Il proxy applicazione Azure AD è costituito da due parti:

* Il servizio basato su cloud (proxy applicazione AAD). Qui è dove vengono eseguite le connessioni esterne client/utente.
* Un componente locale denominato connettore del proxy applicazione AAD.  Il connettore è in ascolto per le richieste provenienti dal servizio proxy applicazione AAD e gestisce le connessioni alle applicazioni interne, fra cui elementi come la delega vincolata Kerberos per l'accesso SSO.

### <a name="traffic-flowsand-how-they-are-secured"></a>Flussi di traffico e come sono protetti
Questa sezione riporta informazioni dettagliate su come vengono protetti i flussi. Un flusso tra il connettore e il servizio proxy applicazione viene stabilito quando: 

* Il connettore viene configurato per la prima volta.
* Il connettore reperisce le informazioni di configurazione dal servizio proxy applicazione, incluso il gruppo di cui ogni connettore è membro.
* Un utente accede a un'applicazione pubblicata.

>[!NOTE]
>Tutte le comunicazioni si verificano su SSL e hanno sempre origine dal connettore verso il servizio proxy applicazione. Questo servizio è solo in uscita.
>

Il connettore usa un certificato client per autenticare il servizio proxy applicazione per tutte le chiamate. L'unica eccezione a questo è il passaggio di configurazione iniziale in cui viene stabilito il certificato client.

#### <a name="install-the-connector"></a>Installare il connettore

Quando il connettore viene configurato per la prima volta, ha luogo il flusso seguente.

1. La registrazione del connettore per il servizio avviene durante l'installazione del connettore. A questo punto all'utente viene chiesto di immettere le credenziali di amministratore di Azure AD. Il token acquisito viene quindi presentato al servizio proxy applicazione Azure Active Directory.
2. Il proxy applicazione valuta il token per verificare che l'utente sia membro del ruolo di amministratore aziendale nel tenant per cui è stato rilasciato il token. In caso contrario il processo viene terminato.
3. Il connettore genera una richiesta di certificato client e la passa con il token al servizio proxy applicazione che a sua volta verifica il token e firma la richiesta del certificato client. 
4. Il connettore usa questo certificato client per la futura comunicazione con il servizio proxy applicazione.
4. Il connettore esegue un pull iniziale dei dati di configurazione del sistema dal servizio usando il certificato client ed è pronto ad accettare le richieste.

#### <a name="periodic-configuration-updates"></a>Aggiornamenti periodici della configurazione

Il flusso seguente si verifica periodicamente, ogni volta che il servizio proxy applicazione aggiorna le impostazioni di configurazione.

1. Il connettore si connette all'endpoint di configurazione all'interno del servizio proxy applicazione usando il suo certificato client.
2. Dopo che il certificato client è stato convalidato, il servizio proxy applicazione restituisce i dati di configurazione al connettore, ad esempio il gruppo di connettori di cui il connettore deve far parte.
3. Se il certificato corrente ha più di 30 giorni, il connettore genera una nuova richiesta di certificato, sostituendo di fatto il certificato client ogni 30 giorni.

#### <a name="accesss-published-applications"></a>Accedere alle applicazioni pubblicate

Quando gli utenti accedono a un'applicazione pubblicata, ha luogo il flusso seguente.

1. Quando un utente accede a un'applicazione pubblicata, il servizio proxy applicazione controlla le impostazioni di configurazione per l'app.  Se l'app è configurata in modo da usare la preautenticazione con Azure AD, l'utente viene reindirizzato al servizio token di sicurezza di Azure AD per l'autenticazione.  Questo passaggio viene ignorato se si pubblica l'app usando il pass-through.
 * Durante l'autenticazione con Azure AD saranno controllati gli eventuali requisiti dei criteri di accesso condizionale per l'applicazione specifica. Questo garantisce che l'utente sia stato assegnato all'applicazione (ad esempio, se è richiesta la MFA, la sequenza di autenticazione chiederà all'utente un secondo fattore di autenticazione).
 * Una volta superati tutti i controlli, il servizio token di sicurezza di Azure AD rilascia un token firmato per l'applicazione e l'utente viene reindirizzato al servizio proxy applicazione.
 * Il proxy applicazione convalida quindi il token per assicurarsi che sia stato emesso per l'applicazione a cui l'utente aveva richiesto l'accesso. Insieme a tutto questo vengono eseguiti altri controlli, come verificare che il token sia stato firmato da Azure AD e si trovi ancora nella finestra valida e così via.
 * Il proxy applicazione imposta un cookie di autenticazione crittografato (ad esempio un cookie non persistente) per indicare che l'autenticazione per l'applicazione è stata completata.  Questo cookie include un timestamp di scadenza basato sul token di Azure AD e su altri dati, ad esempio il nome utente su cui si basa l'autenticazione.  Questo cookie viene crittografato mediante una chiave privata nota solo al servizio proxy applicazione.
 * Il proxy applicazione reindirizza l'utente all'URL originariamente richiesto.
 >[!NOTE] 
 >Se qualsiasi parte dei passaggi di preautenticazione non ha esito positivo, la richiesta dell'utente viene negata e all'utente viene visualizzato un messaggio che indica l'origine del problema.
 >

2. Il proxy applicazione, dopo aver ricevuto la richiesta dal client, verifica che la condizione di preautenticazione sia stata soddisfatta e che il cookie sia ancora valido (in base alle esigenze). Quindi viene inserita una richiesta nella coda appropriata affinché un connettore locale possa gestirla. 

 >[!NOTE]
 >Tutte le richieste provenienti dal connettore sono in uscita verso il servizio proxy applicazione. I connettori mantengono aperta una connessione in uscita al proxy applicazione. Quando viene ricevuta una richiesta, il proxy applicazione la mette in coda su una delle connessioni aperte affinché il connettore la accetti.
 >

 * La richiesta include elementi dell'applicazione, come le intestazioni della richiesta e i dati del cookie crittografato, l'utente che effettua la richiesta e l'ID richiesta.  Tuttavia il cookie di autenticazione crittografato non viene inviato al connettore.
3. Il connettore riceve la richiesta dalla coda, in base a una connessione in uscita di lunga durata. Il proxy applicazione eseguirà una delle operazioni seguenti in base alla richiesta:
 * Il connettore conferma se può identificare l'applicazione.  In caso contrario il connettore stabilisce una connessione al servizio proxy applicazione per raccogliere dettagli sull'applicazione e li memorizza nella cache in locale.
 * Se la richiesta è un'operazione semplice, ad esempio non sono presenti dati all'interno del corpo come avviene in una richiesta RESTful "GET", il connettore esegue una connessione alla risorsa interna di destinazione e attende una risposta.
 * Se la richiesta contiene dati associati nel corpo, ad esempio un'operazione RESTful "POST", il connettore esegue una connessione in uscita usando il certificato client verso l'istanza del proxy applicazione. Lo fa per richiedere i dati e aprire una connessione alla risorsa interna.  Al momento della ricezione della richiesta dal connettore, il servizio proxy applicazione inizia ad accettare contenuto dall'utente e lo inoltra al connettore.  Il connettore, a sua volta, inoltra i dati alla risorsa interna.
4. Una volta completata la richiesta/trasmissione di tutto il contenuto al back-end, il connettore attende una risposta.
5. Dopo aver ricevuto la risposta, il connettore esegue una connessione in uscita al servizio proxy applicazione per restituire i dettagli dell'intestazione e avviare il flusso dei dati restituiti.
6. Il proxy applicazione invia i dati all'utente in un flusso.  In questa fase è possibile che siano eseguite alcune operazioni di elaborazione delle intestazioni, in base alle esigenze e a quanto definito dall'applicazione.

Se si richiede assistenza per la comunicazione da un'applicazione Web di Azure tramite un browser client verso un endpoint locale SOAP (Simple Object Access Protocal) autenticato in Windows, vedere questo [Azure Field Notes Blog](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy). 

##<a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md)




<!--HONumber=Feb17_HO1-->


