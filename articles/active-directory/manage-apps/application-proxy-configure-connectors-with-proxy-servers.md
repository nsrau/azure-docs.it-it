---
title: Usare server proxy locali esistenti e Azure AD | Documentazione Microsoft
description: Tratta l'uso di server proxy locali esistenti.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aafb971ca1ce812a68045f7d0c0c2ab7f532133
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877389"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Usare server proxy locali esistenti

Questo articolo illustra come configurare i connettori del proxy applicazione di Azure Active Directory (Azure AD) per l'uso di server proxy in uscita. È rivolto ai clienti con ambienti di rete che dispongono di proxy esistenti.

Iniziamo esaminando questi scenari di distribuzione principali:

* Configurare i connettori per ignorare i proxy in uscita locali.
* Configurare i connettori per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD.
* Configurare l'utilizzo di un proxy tra il connettore e l'applicazione back-end.

Per ulteriori informazioni sui connettori, vedere [Informazioni sui connettori proxy di applicazione di Azure AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Ignorare i proxy in uscita

I connettori sono componenti del sistema operativo sottostanti che creano le richieste in uscita. Questi componenti tentano automaticamente di individuare un server proxy nella rete usando WPAD (Web Proxy Auto-Discovery).

I componenti del sistema operativo provano a individuare un server proxy eseguendo una ricerca DNS per wpad.domainsuffix. Se la ricerca restituisce il DNS, viene quindi inviata una richiesta HTTP all'indirizzo IP per wpad.dat. Questa richiesta diventa lo script di configurazione proxy nell'ambiente. Il connettore usa questo script per selezionare un server proxy in uscita. Il traffico del connettore potrebbe tuttavia non riuscire ancora a passare perché sono necessarie altre impostazioni di configurazione nel proxy.

È possibile configurare il connettore in modo che ignori il proxy locale, per garantire che usi la connettività diretta ai servizi di Azure. Se consentita dai criteri di rete, questa è un'operazione consigliata, perché implica una configurazione in meno da gestire.

Per disabilitare l'utilizzo del proxy in uscita per il connettore, modificare il file C: , Programmi , Microsoft AAD App Proxy Connector , ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* illustrata in questo esempio di codice:

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

Per assicurarsi che anche il servizio Connector Updater ignori il proxy, apportare una modifica simile al file ApplicationProxyConnectorUpdaterService.exe.config. Questo file si trova in C:\Programmi\Microsoft AAD App Proxy Connector Updater.

Assicurarsi di creare copie dei file originali nel caso sia necessario ripristinare i file .config predefiniti.

## <a name="use-the-outbound-proxy-server"></a>Usare il server proxy in uscita

Alcuni ambienti dei clienti richiedono che tutto il traffico in uscita passi attraverso un proxy in uscita, senza eccezioni. Di conseguenza ignorare il proxy non è possibile.

Si può configurare il traffico del connettore per il passaggio attraverso il proxy in uscita, come illustrato nel diagramma seguente:

 ![Configurazione del traffico del connettore per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Dato che il traffico è solo in uscita, non è necessario configurare l'accesso in ingresso attraverso firewall.

> [!NOTE]
> Il proxy di applicazione non supporta l'autenticazione in altri proxy. Gli account del servizio di rete del connettore/strumento di aggiornamento dovrebbero essere in grado di connettersi al proxy senza ricevere richieste di autenticazione.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passaggio 1: Configurare il connettore e i servizi correlati per il passaggio attraverso il proxy in uscita

Se WPAD è abilitato nell'ambiente e configurato in modo corretto, il connettore individua automaticamente il server proxy in uscita e tenta di usarlo. Tuttavia, è possibile configurare in modo esplicito il connettore per il passaggio attraverso un proxy in uscita.

A tale scopo modificare il file C:\Programmi\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* indicata nell'esempio di codice seguente. Modificare *proxyserver:8080* per riflettere il nome o l'indirizzo IP del server proxy locale e la porta su cui è in ascolto. Il valore deve contenere il prefisso http://, anche se si usa un indirizzo IP.

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

Configurare quindi il servizio Connector Updater in modo che usi il proxy, apportando una modifica simile al file C:\Programmi\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passaggio 2: configurare il proxy per consentire il passaggio del traffico proveniente dal connettore e dai servizi correlati

Esistono quattro aspetti da considerare per il proxy in uscita:

* Regole in uscita del proxy
* Autenticazione proxy
* Porte proxy
* Ispezione TLS

#### <a name="proxy-outbound-rules"></a>Regole in uscita del proxy

Consentire l'accesso agli URL seguenti:

| URL | Uso |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicazione tra il connettore e il servizio cloud proxy di applicazione |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Il connettore utilizza questi URL per verificare i certificati |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*.msecnd.net<br>*.msftauth.net<br>*File<br>con estensione msftauthimages.net*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Il connettore usa questi URL durante il processo di registrazione. |

Se il firewall o il proxy consente di configurare \*gli \*elenchi indirizzi DNS consentiti, è possibile consentire le connessioni a .msappproxy.net e .servicebus.windows.net. In caso contrario, è necessario consentire l'accesso agli [intervalli di indirizzi IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Gli intervalli di indirizzi IP vengono aggiornati ogni settimana.

Se non è possibile consentire la connettività in base al nome di dominio completo ed è necessario specificare invece intervalli IP, usare queste opzioni:

* Consentire al connettore l'accesso in uscita a tutte le destinazioni.
* Consentire al connettore l'accesso in uscita a tutti gli [intervalli di indirizzi IP del data center di Azure](https://www.microsoft.com//download/details.aspx?id=41653). Il problema con l'elenco di intervalli di indirizzi IP del data center di Azure è che viene aggiornato ogni settimana. È necessario implementare un processo per assicurare che le regole di accesso vengano aggiornate di conseguenza. L'uso di un solo subset di indirizzi IP può provocare errori di configurazione.

#### <a name="proxy-authentication"></a>Autenticazione proxy

L'autenticazione proxy non è attualmente supportata. Il nostro consiglio è di consentire l'accesso anonimo del connettore alle destinazioni su Internet.

#### <a name="proxy-ports"></a>Porte proxy

Il connettore effettua connessioni in uscita basate su TLS utilizzando il metodo CONNECT. Questo metodo crea essenzialmente un tunnel attraverso il proxy in uscita. Configurare il server proxy per consentire il tunneling alle porte 443 e 80.

> [!NOTE]
> Quando il bus di servizio viene eseguito su HTTPS, usa la porta 443. Per impostazione predefinita, il bus di servizio prova tuttavia a stabilire connessioni TCP dirette ed esegue il fallback su HTTPS solo se si verifica un errore di connettività diretta.

#### <a name="tls-inspection"></a>Ispezione TLS

Non utilizzare l'ispezione TLS per il traffico del connettore, perché causa problemi per il traffico del connettore. Il connettore utilizza un certificato per l'autenticazione al servizio proxy di applicazione e tale certificato può andare perso durante l'ispezione TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Configurare l'utilizzo di un proxy tra il connettore e l'applicazione back-end
L'utilizzo di un proxy di inoltro per la comunicazione verso l'applicazione back-end potrebbe essere un requisito speciale in alcuni ambienti.
Per abilitare questa opzione, seguire i passaggi successivi:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Passaggio 1: Aggiungere il valore del Registro di sistema necessario al server
1. Per abilitare l'utilizzo del proxy predefinito, `UseDefaultProxyForBackendRequests = 1` aggiungere il seguente valore del Registro di sistema (DWORD) alla chiave del Registro di sistema di configurazione del connettore che si trova in "HKEY_LOCAL_MACHINE Software Microsoft Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Passaggio 2: Configurare manualmente il server proxy utilizzando il comando netsh
1.  Abilitare i criteri di gruppo Effettuare le impostazioni proxy per computer. Di gruppo È disponibile in: Configurazione computer, Criteri, Modelli amministrativi, Componenti di Windows e Internet Explorer. Questo deve essere impostato anziché avere questo criterio impostato su per utente.
2.  Eseguire `gpupdate /force` sul server o riavviare il server per assicurarsi che utilizzi le impostazioni aggiornate dei criteri di gruppo.
3.  Avviare un prompt dei comandi `control inetcpl.cpl`con privilegi elevati con diritti di amministratore e immettere .
4.  Configurare le impostazioni proxy necessarie. 

Queste impostazioni rendono il connettore usare lo stesso proxy di inoltro per la comunicazione con Azure e per l'applicazione back-end. Se il connettore alla comunicazione di Azure non richiede alcun proxy di inoltro o un proxy di inoltro diverso, è possibile configurarlo con la modifica del file ApplicationProxyConnectorService.exe.config come descritto nelle sezioni Ignora proxy in uscita o Usare il server proxy in uscita.

Il servizio di aggiornamento del connettore utilizzerà anche il proxy del computer. Questo comportamento può essere modificato modificando il file ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Risolvere i problemi relativi al connettore proxy e alla connettività del servizio

A questo punto si dovrebbe vedere tutto il traffico che passa attraverso il proxy. Se si verificano problemi, potrebbero essere utili le seguenti informazioni per la risoluzione dei problemi.

Il modo migliore per identificare e risolvere i problemi di connettività del connettore è eseguire un'acquisizione di rete durante l'avvio del servizio del connettore. Ecco alcuni semplici suggerimenti per acquisire e filtrare le tracce di rete.

È possibile usare lo strumento di monitoraggio preferito. Ai fini di questo articolo, è stato usato Microsoft Message Analyzer. È possibile [scaricarlo da Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Gli esempi seguenti sono specifici di Message Analyzer, ma i principi possono essere applicati a qualsiasi strumento di analisi.

### <a name="take-a-capture-of-connector-traffic"></a>Eseguire un'acquisizione del traffico del connettore

Per iniziare la risoluzione dei problemi, procedere come segue:

1. Da services.msc arrestare il servizio connettore del proxy applicazione di Azure AD.

   ![Servizio connettore proxy applicazione di Azure AD in services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Eseguire Message Analyzer come amministratore.
1. Selezionare l'opzione per l'avvio della **traccia locale**.
1. Avviare il servizio connettore del proxy applicazione di Azure AD.
1. Arrestare l'acquisizione di rete.

   ![Screenshot mostra il pulsante Interrompi acquisizione rete](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controllare se il traffico del connettore ignora i proxy in uscita

Se il connettore del proxy di applicazione è stato configurato per ignorare i server proxy e connettersi direttamente al servizio proxy di applicazione, è necessario individuare nell'acquisizione di rete i tentativi di connessione TCP non riusciti.

Usare il filtro di Message Analyzer per identificare questi tentativi. Immettere `property.TCPSynRetransmit` nella casella del filtro e selezionare **Applica**.

Un pacchetto SYN è il primo pacchetto inviato per stabilire una connessione TCP. Se questo pacchetto non restituisce una risposta, il SYN viene ritentato. È possibile usare il filtro precedente per visualizzare qualsiasi SYN ritrasmesso. È quindi possibile vedere se questi SYN corrispondono a traffico correlato al connettore.

Se si prevede che il connettore esegua connessioni dirette ai servizi di Azure, le risposte SynRetransmit sulla porta 443 indicano la presenza di un problema di rete o di firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controllare se il traffico del connettore usa proxy in uscita

Se il traffico del connettore del proxy di applicazione è stato configurato per passare attraverso i server proxy, è necessario individuare le connessioni HTTPS non riuscite nel proxy.

Per filtrare l'acquisizione di rete in modo da individuare questi tentativi di connessione, immettere `(https.Request or https.Response) and tcp.port==8080` nel filtro di Message Analyzer, sostituendo 8080 con la porta del servizio proxy. Selezionare **Applica** per visualizzare i risultati del filtro.

Il filtro precedente mostra solo le richieste e le risposte HTTPS da e verso la porta del proxy. Cercare le richieste CONNECT che indicano la comunicazione con il server proxy. Al completamento dell'operazione si ottiene una risposta HTTP affermativa (200).

Se vengono visualizzati altri codici di risposta, ad esempio 407 o 502, significa che il proxy richiede l'autenticazione o non consente il traffico per altri motivi. A questo punto ci si rivolge al team di supporto del server proxy.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui connettori del proxy di applicazione di Azure ADUnderstand Azure AD Application Proxy connectors](application-proxy-connectors.md)
* In caso di problemi di connettività del connettore, porre una domanda nel [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) o creare un ticket per il team di supporto Microsoft.
