---
title: Usare server proxy locali esistenti e Azure AD | Documentazione Microsoft
description: Tratta l&quot;uso di server proxy locali esistenti.
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
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Usare server proxy locali esistenti

Questo articolo illustra come configurare il connettore del proxy applicazione per usare server proxy in uscita. È rivolto ai clienti con ambienti di rete che dispongono di proxy esistenti.

Iniziamo esaminando questi scenari di distribuzione principali:
* Configurazione dei connettori per ignorare il proxy in uscita locale.
* Configurazione dei connettori per usare un proxy in uscita per accedere al proxy applicazione Azure AD.

Per altre informazioni sul funzionamento dei connettori, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configurare i connettori

Il servizio connettore principale usa il bus di servizio di Azure per gestire alcune delle comunicazioni sottostante al servizio proxy applicazione Azure Active Directory. Il bus di servizio aggiunge ulteriori requisiti di configurazione.

Per informazioni sulla risoluzione dei problemi di connettività di Azure AD, vedere [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Come risolvere i problemi di connettività del proxy applicazione Azure AD). 

## <a name="configure-the-outbound-proxy"></a>Configurare il proxy in uscita

Se si dispone di un proxy in uscita nell'ambiente, assicurarsi che l'account che esegue l'installazione sia configurato in modo appropriato per l'uso del proxy in uscita. Poiché il programma di installazione viene eseguito nel contesto dell'utente che esegue l'installazione, questa operazione può essere eseguita usando Microsoft Edge o altri browser Internet. 

Per configurare le impostazioni del proxy usando Microsoft Edge, passare a Impostazioni, Visualizza impostazioni avanzate, Apri impostazioni proxy, Configurazione manuale proxy. Impostare "Usa server proxy" su Sì, selezionare "Non usare server proxy per indirizzi locali (Intranet)" e quindi cambiare l'indirizzo e la porta in modo che corrispondano al server proxy locale.

Specificare le impostazioni proxy necessarie come indicato nelle opzioni di seguito.

 ![Ignorare gli indirizzi locali con Azure AD](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>Ignorare i proxy in uscita

Per impostazione predefinita i componenti del sistema operativo sottostanti utilizzati dal connettore per eseguire automaticamente le richieste in uscita tentano di individuare un server proxy nella rete mediante Web Proxy Auto-Discovery (WPAD), se è attivato nell'ambiente.

In genere ciò avviene eseguendo una ricerca DNS per wpad.domainsuffix. Se viene risolto in un DNS, verrà quindi eseguita una richiesta HTTP all'indirizzo IP per WPAD.dat che sarà lo script di configurazione proxy nell'ambiente. Il connettore lo userà quindi per selezionare un server proxy in uscita. Il traffico del connettore però potrebbe non riuscire ancora a passare in quanto sono necessarie altre impostazioni nel proxy. 

Nella sezione successiva vengono trattati i passaggi di configurazione necessari sul proxy in uscita perché il traffico scorra attraverso di esso. Ma prima vediamo come è possibile configurare il connettore in modo da ignorare il proxy locale e garantire che usi la connettività diretta ai servizi di Azure. Questo è il modo consigliato di procedere (se i criteri di rete lo consentono), in quanto significa avere una configurazione in meno da mantenere.

Per disabilitare l'uso del proxy in uscita per il connettore, modificare il file C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione [system.net] illustrata nell'esempio di codice riportato di seguito:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Per assicurarsi che anche il servizio di aggiornamento del connettore ignori il proxy, apportare una modifica simile al file ApplicationProxyConnectorUpdaterService.exe.config che si trova in C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

Assicurarsi di creare copie dei file originali nel caso sia necessario ripristinare i file .config predefiniti. 

## <a name="use-the-outbound-proxy-server"></a>Usare il server proxy in uscita

Come è stato detto in precedenza, in alcuni ambienti dei clienti è richiesto che tutto il traffico in uscita passi attraverso un proxy in uscita, senza eccezioni. Di conseguenza ignorare il proxy non è possibile.

Si può configurare il traffico del connettore per il passaggio attraverso il proxy in uscita, come illustrato di seguito.

 ![Ignorare gli indirizzi locali con Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Siccome il traffico è solo in uscita, non è necessario configurare il bilanciamento del carico tra i connettori o configurare l'accesso in ingresso attraverso il firewall.

In ogni caso è necessario completare i seguenti passaggi:
1. Configurare il connettore e il servizio di aggiornamento per il passaggio attraverso il proxy in uscita.
2. Configurare le impostazioni proxy in modo da consentire le connessioni al servizio proxy app di Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passaggio 1: Configurare il connettore e i servizi correlati per il passaggio attraverso il proxy in uscita

Come illustrato in precedenza, se WPAD è attivato nell'ambiente e configurato in modo appropriato, il connettore individuerà automaticamente il server proxy in uscita e tenterà di usarlo. Tuttavia, è possibile configurare in modo esplicito il connettore per il passaggio attraverso un proxy in uscita. 

Per farlo modificare il file C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione [system.net] mostrata nell'esempio di codice seguente:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>Modificare _proxyserver:8080_ in modo che corrisponda al nome o all'indirizzo IP del server proxy locale e alla porta su cui è in ascolto.
>

Quindi è necessario configurare il servizio di aggiornamento del connettore in modo che usi il proxy, apportando una modifica simile al file che si trova in C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passaggio 2: Configurare il proxy per consentire il passaggio del traffico proveniente dal connettore e dai servizi correlati

Esistono 4 aspetti da considerare per il proxy in uscita:
* Regole in uscita del proxy
* Autenticazione proxy
* Porte proxy
* Ispezione SSL

#### <a name="2a-proxy-outbound-rules"></a>2.A: Regole in uscita del proxy
Consentire l'accesso agli endpoint seguenti per l'accesso al servizio connettore:

* *.msappproxy.net 
* *.servicebus.microsoft.net 

Per la registrazione iniziale consentire l'accesso agli endpoint seguenti:

* login.windows.net 
* login.microsoftonline.com. 

I canali di controllo del bus di servizio sottostanti utilizzati dal servizio connettore richiedono anche la connettività a specifici indirizzi IP. Stiamo lavorando con il team del bus di servizio per passare a un nome di dominio completo. Per ora sono disponibili due opzioni: 
 
* Consentire al connettore l'accesso in uscita a tutte le destinazioni o
* Consentire al connettore l'accesso in uscita agli intervalli IP del data center di Azure elencati in https://www.microsoft.com/en-gb/download/details.aspx?id=41653

>[!NOTE]
>La difficoltà dell'elenco degli intervalli IP del data center di Azure è che viene aggiornato ogni settimana, pertanto è necessario implementare un processo per verificare che le regole di accesso vengono aggiornate di conseguenza.
>

#### <a name="2b-proxy-authentication"></a>2.B: Autenticazione proxy

L'autenticazione proxy non è attualmente supportata. Il nostro consiglio è di consentire l'accesso anonimo del connettore alle destinazioni su Internet.

#### <a name="2c-proxy-ports"></a>2.C: Porte proxy

Il connettore esegue le connessioni SSL in uscita usando il metodo CONNECT. In questo modo essenzialmente viene creato un tunnel attraverso il proxy in uscita. Alcuni server proxy per impostazione predefinita consentono il tunneling in uscita solo alle porte SSL standard, ad esempio la 443. In questo caso il server proxy dovrà essere configurato in modo da consentire il tunneling a porte aggiuntive.

Configurare il server proxy per consentire il tunneling alle porte SSL non standard 8080, 9090, 9091 e 10100-10120.

>[!NOTE]
>Quando il bus di servizio viene eseguito su HTTPS, usa la porta 443. Tuttavia, per impostazione predefinita, il bus di servizio tenterà di stabilire connessioni TCP dirette ed eseguirà il fallback su HTTPS solo se si verifica un errore di connettività diretta. > 
> 

Per garantire che il traffico del bus di servizio venga inviato anche tramite il server proxy in uscita, è necessario assicurarsi che il connettore non possa connettersi direttamente ai servizi di Azure per le porte 9350, 9352 e 5671.

#### <a name="2d-ssl-inspection"></a>2.D: Ispezione SSL
Non usare l'ispezione SSL per il traffico del connettore in quanto causerà problemi per tale traffico. 

Non c'è altro. A questo punto si dovrebbe vedere tutto il traffico che passa attraverso il proxy. Se si verificano problemi, potrebbero essere utili si seguenti passaggi per la risoluzione dei problemi.

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Risoluzione dei problemi relativi al connettore proxy e alla connettività del servizio

Il modo migliore di identificare e risolvere i problemi di connettività del connettore è eseguire un'acquisizione di rete sul servizio del connettore durante l'avvio del servizio del connettore. Questa può essere un'attività molto complessa, quindi vediamo alcuni semplici suggerimenti sull'acquisizione e il filtraggio delle tracce di rete.

È possibile usare lo strumento di monitoraggio preferito. Ai fini di questo articolo abbiamo usato Microsoft Network Monitor 3.4 che può essere scaricato da https://www.microsoft.com/en-us/download/details.aspx?id=4865.

Gli esempi e i filtri che usiamo di seguito sono specifici di Network Monitor ma i principi possono essere applicati a qualsiasi strumento di analisi.

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Eseguire un'acquisizione con Microsoft Network Monitor

Per avviare un'acquisizione, aprire Network Monitor e fare clic su Nuovo Acquisizione. Fare clic sul pulsante Avvia per iniziare.

 ![Network Monitor in Azure AD](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Dopo aver completato un'acquisizione, fare clic sul pulsante Arresta per terminare l'acquisizione.

### <a name="take-a-capture-of-connector-traffic"></a>Eseguire un'acquisizione del traffico del connettore

Per iniziare la risoluzione dei problemi, procedere come segue: 

1. Da services.msc arrestare il servizio connettore del proxy applicazione Microsoft AAD (come mostrato di seguito).
2. Avviare l'acquisizione di rete.
3. Avviare il servizio connettore del proxy applicazione Microsoft AAD.
4. Arrestare l'acquisizione di rete.

 ![Network Monitor in Azure AD](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>Esaminare le richieste del connettore al server proxy

Ora che si ha un'acquisizione di rete, è possibile filtrarla. Per l'analisi della traccia è fondamentale sapere come filtrare l'acquisizione.

Un filtro per questo caso è il seguente (dove 8080 è la porta del servizio proxy): 

(http.Request o http.Response) e tcp.port==8080

Se si immette il filtro nella finestra di visualizzazione del filtro e si seleziona Applica, il traffico acquisiti sarà filtrato in base al filtro.

Il filtro precedente consente di visualizzare solo le richieste e risposte HTTP da e verso la porta del proxy. Per l'avvio di un connettore dove il connettore è configurato per usare un server proxy si ottiene una visualizzazione simile alla seguente:

 ![Network Monitor in Azure AD](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Ora si cercano in modo specifico le richieste CONNECT che dimostrano che si sta dialogando con il server proxy corretto. Al completamento dell'operazione si otterrà una risposta HTTP affermativa (200).

Se vengono visualizzati altri codici di risposta, ad esempio 407 o 502, significa che il proxy richiede l'autenticazione o non consente il traffico per altri motivi. A questo punto si dovrebbe coinvolgere il team di supporto del server proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificare tentativi di connessione TCP non riusciti

L'altro scenario comune a cui si potrebbe essere interessati è quando il connettore sta tentando di connettersi direttamente ma non riesce. 

Un altro filtro di Network Monitor che aiuta a identificare facilmente questa situazione è:

property.TCPSynRetransmit

Un pacchetto SYN è il primo pacchetto inviato per stabilire una connessione TCP. Se questo non restituisce una risposta, il SYN viene ritentato. Il filtro riportato sopra consente di visualizzare tutti i SYN trasmessi nuovamente. Quindi è possibile vedere se questi corrispondono a traffico correlato al connettore. 

L'esempio seguente illustra un tentativo di connessione non riuscito alla porta 9352 del bus di servizio:

 ![Network Monitor in Azure AD](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Se viene visualizzato qualcosa di simile alla risposta precedente, significa che il connettore sta tentando di comunicare direttamente con il servizio del bus di servizio di Azure. Se si prevede che il connettore esegua connessioni dirette ai servizi di Azure, questa è una chiara indicazione del fatto che si è verificato un problema di rete o di firewall.

>[!NOTE]
>Se il sistema è configurato per l'uso di un server proxy, è possibile che il bus di servizio tenti una connessione TCP diretta prima di passare a tentare di connettersi su HTTPS, pertanto tenere presente questo.
>

L'analisi delle tracce di rete non è per tutti. Ma può essere uno strumento estremamente prezioso per ottenere rapidamente informazioni su cosa sta succedendo nella rete. 

Se si verificano continuamente problemi di connettività del connettore, creare un ticket con il team di supporto Microsoft per la risoluzione del problema.

Per informazioni sulla risoluzione degli errori con il connettore del proxy applicazione, vedere [Risolvere i problemi del proxy applicazione](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Passaggi successivi

[Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md)<br>
[Come eseguire un'installazione invisibile all'utente del connettore del proxy applicazione Azure AD](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


