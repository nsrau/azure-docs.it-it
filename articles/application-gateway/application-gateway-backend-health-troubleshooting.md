---
title: Risolvere i problemi di integrità del back-end nel gateway applicazione di Azure
description: Descrive come risolvere i problemi di integrità del back-end per il gateway applicazione di Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: b8acf1b025a5943773821c8ab78de6288eb6bec2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397899"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Risolvere i problemi di integrità del back-end nel gateway applicazione
==================================================

<a name="overview"></a>Panoramica
--------

Per impostazione predefinita, il gateway applicazione di Azure verifica tramite probe i server back-end per controllarne lo stato integrità e determinare se sono pronti per gestire le richieste. Gli utenti possono anche creare probe personalizzati per indicare il nome host, il percorso da verificare tramite probe e i codici di stato da accettare come integri. In ogni caso, se il server back-end non risponde correttamente, il gateway applicazione contrassegna il server come non integro e interrompe l'inoltro delle richieste al server. Quando il server inizia a rispondere correttamente, il gateway applicazione riprende l'inoltro delle richieste.

### <a name="how-to-check-backend-health"></a>Come controllare l'integrità del back-end

Per controllare l'integrità del pool back-end, è possibile usare la pagina **Integrità back-end** nel portale di Azure. In alternativa, è possibile usare [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) l'[interfaccia della riga di comando di Azure](/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health) o l'[API REST](/rest/api/application-gateway/applicationgateways/backendhealth).

Lo stato recuperato da questi diversi metodi può essere uno dei seguenti:

- Integro

- Non integro

- Sconosciuto

Se lo stato integrità del back-end di un server è Integro, significa che il gateway applicazione inoltrerà le richieste a questo server. Tuttavia, se lo stato integrità del back-end per tutti i server in un pool back-end è Non integro o Sconosciuto, si potrebbero riscontrare problemi quando si tenta di accedere alle applicazioni. Questo articolo descrive i sintomi, le cause e le soluzioni per ognuno degli errori visualizzati.

<a name="backend-health-status-unhealthy"></a>Stato integrità del back-end: Non integro
-------------------------------

Se lo stato integrità del back-end è Non integro, la visualizzazione del portale sarà simile allo screenshot seguente:

![Integrità del back-end del gateway applicazione - Non integro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

In alternativa, se si usa una query di Azure PowerShell, dell'interfaccia della riga di comando di Azure o dell'API REST di Azure, si otterrà una risposta simile alla seguente:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Se si riceve lo stato Non integro per un server back-end per tutti i server in un pool back-end, le richieste non vengono inoltrate ai server e il gateway applicazione restituisce l'errore "502 Gateway non valido" al client richiedente. Per risolvere il problema, controllare la colonna **Dettagli** nella scheda **Integrità back-end**.

Il messaggio visualizzato nella colonna **Dettagli** fornisce informazioni più dettagliate sul problema, in base alle quali è possibile iniziare a risolverlo.

> [!NOTE]
> La richiesta di probe predefinita viene inviata nel formato \<protocol\> ://127.0.0.1: \<port\> /. Ad esempio, http://127.0.0.1:80 per un probe HTTP sulla porta 80. Solo i codici di stato HTTP da 200 a 399 vengono considerati integri. Il protocollo e la porta di destinazione vengono ereditati dalle impostazioni HTTP. Se si vuole che il gateway applicazione verifichi tramite probe un altro protocollo, nome host o percorso e riconosca un codice di stato diverso come integro, configurare un probe personalizzato e associarlo alle impostazioni HTTP.

<a name="error-messages"></a>Messaggi di errore
------------------------
#### <a name="backend-server-timeout"></a>Timeout del server back-end

**Messaggio:** Time taken by the backend to respond to application gateway\'s health probe is more than the timeout threshold in the probe setting (Il tempo impiegato dal back-end per rispondere al probe di integrità del gateway applicazione è maggiore della soglia di timeout nell'impostazione del probe).

**Causa:** dopo che il gateway applicazione ha inviato una richiesta di probe HTTP(S) al server back-end, attende una risposta dal server back-end per un periodo di tempo configurato. Se il server back-end non risponde entro il periodo di tempo configurato (valore di timeout), viene contrassegnato come Non integro fino a quando non inizia di nuovo a rispondere entro il periodo di timeout configurato.

**Risoluzione:** verificare il motivo per cui l'applicazione o il server back-end non risponde entro il periodo di timeout configurato e controllare anche le dipendenze dell'applicazione. Verificare, ad esempio, se nel database sono presenti problemi che possono causare un ritardo nella risposta. Se si conosce il comportamento dell'applicazione e questa risponde solo dopo il valore di timeout, aumentare questo valore dalle impostazioni del probe personalizzato. Per modificare il valore di timeout, deve essere disponibile un probe personalizzato. Per informazioni su come configurare un probe personalizzato, [vedere la pagina della documentazione](./application-gateway-create-probe-portal.md).

Per aumentare il valore di timeout, seguire questa procedura:

1.  Accedere direttamente al server back-end e controllare il tempo impiegato dal server per rispondere nella pagina. È possibile usare qualsiasi strumento per accedere al server back-end, incluso un browser con gli strumenti di sviluppo.

1.  Dopo aver calcolato il tempo impiegato dall'applicazione per rispondere, selezionare la scheda **Probe integrità** e quindi selezionare il probe associato alle impostazioni HTTP.

1.  Immettere qualsiasi valore di timeout maggiore del tempo di risposta dell'applicazione, in secondi.

1.  Salvare le impostazioni del probe personalizzato e verificare se lo stato integrità del back-end è ora Integro.

#### <a name="dns-resolution-error"></a>Errore di risoluzione DNS

**Messaggio:** Application Gateway could not create a probe for this backend. This usually happens when the FQDN of the backend has not been entered correctly (Il gateway applicazione non è riuscito a creare un probe per il back-end. Questo problema si verifica in genere quando l'FQDN del back-end non è stato immesso correttamente). 

**Causa:** se il pool back-end è di tipo indirizzo IP/FQDN o servizio app, il gateway applicazione viene risolto nell'indirizzo IP dell'FQDN immesso tramite Domain Name System (DNS), personalizzato o predefinito di Azure, e tenta di connettersi al server sulla porta TCP indicata nelle impostazioni HTTP. Tuttavia, se viene visualizzato questo messaggio, è possibile che il gateway applicazione non sia riuscito a risolvere l'indirizzo IP dell'FQDN immesso.

**Risoluzione:**

1.  Verificare che l'FQDN immesso nel pool back-end sia corretto e che si tratta di un dominio pubblico, quindi provare a risolverlo dal computer locale.

1.  Se è possibile risolvere l'indirizzo IP, può essersi verificato un problema relativo alla configurazione DNS nella rete virtuale.

1.  Verificare se la rete virtuale è configurata con un server DNS personalizzato. Se sì, verificare il motivo per cui il server DNS non può essere risolto nell'indirizzo IP dell'FQDN specificato.

1.  Se si usa il server DNS predefinito di Azure, contattare il registrar per verificare che sia stato completato il mapping di un record A o CNAME appropriato.

1.  Se il dominio è privato o interno, provare a risolverlo da una macchina virtuale nella stessa rete virtuale. Se è possibile risolverlo, riavviare il gateway applicazione e verificare di nuovo. Per riavviare il gateway applicazione, è necessario [arrestarlo](/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [avviarlo](/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) usando i comandi di PowerShell descritti in queste risorse collegate.

#### <a name="tcp-connect-error"></a>Errore di connessione TCP

**Messaggio:** Application Gateway could not connect to the backend.
Please check that the backend responds on the port used for the probe.
Also check whether any NSG/UDR/Firewall is blocking access to the Ip and port of this backend (Il gateway applicazione non è riuscito a connettersi al back-end. Verificare che il back-end risponda sulla porta usata per il probe. Verificare anche se un NSG, una route definita dall'utente o un firewall blocca l'accesso all'indirizzo IP e alla porta del back-end).

**Causa:** dopo la fase di risoluzione DNS, il gateway applicazione tenta di connettersi al server back-end sulla porta TCP configurata nelle impostazioni HTTP. Se il gateway applicazione non riesce a stabilire una sessione di connessione TCP sulla porta specificata, il probe viene contrassegnato come non integro con questo messaggio.

**Soluzione:** se viene visualizzato questo errore, seguire questa procedura:

1.  Verificare se è possibile connettersi al server back-end sulla porta indicata nelle impostazioni HTTP usando un browser o PowerShell. Ad esempio, eseguire questo comando: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se la porta indicata non è quella desiderata, immettere il numero di porta corretto per la connessione del gateway applicazione al server back-end.

1.  Se non è possibile connettersi sulla porta neanche dal computer locale, eseguire le operazioni seguenti:

    a.  Controllare le impostazioni del gruppo di sicurezza di rete (NSG) della scheda di rete e della subnet del server back-end e se sono consentite le connessioni in ingresso alla porta configurata. In caso negativo, creare una nuova regola per consentire le connessioni. Per informazioni su come creare regole dei gruppi di sicurezza di rete, [vedere la pagina della documentazione](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Verificare se le impostazioni dell'NSG della subnet del gateway applicazione consentono il traffico pubblico e privato in uscita, in modo che sia possibile stabilire una connessione. Vedere la pagina della documentazione indicata nel passaggio 3a per altre informazioni su come creare regole per i gruppi di sicurezza di rete.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controllare le impostazioni delle route definite dall'utente del gateway applicazione e la subnet del server back-end per individuare eventuali anomalie di routing. Verificare che la route definita dall'utente non indirizzi il traffico al di fuori della subnet back-end. Ad esempio, verificare se alla subnet del gateway applicazione vengono annunciate route verso appliance virtuali di rete o route predefinite tramite ExpressRoute di Azure e/o VPN.

    d.  Per verificare le route e le regole valide per una scheda di rete, è possibile usare i comandi di PowerShell seguenti:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se non si riscontrano problemi relativi a NSG o route definite dall'utente, controllare la presenza di problemi relativi all'applicazione nel server back-end che impediscono ai client di stabilire una sessione TCP sulle porte configurate. Alcuni controlli da eseguire:

    a.  Aprire un prompt dei comandi (Windows+R -\> cmd), immettere `netstat` e premere INVIO.

    b.  Verificare se il server è in ascolto sulla porta configurata. Ad esempio:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se non è in ascolto sulla porta configurata, controllare le impostazioni del server Web. Ad esempio: binding del sito in IIS, blocco del server in NGINX e host virtuale in Apache.

    d.  Controllare le impostazioni del firewall del sistema operativo per assicurarsi che sia consentito il traffico in ingresso verso la porta.

#### <a name="http-status-code-mismatch"></a>Mancata corrispondenza del codice di stato HTTP

**Messaggio:** Status code of the backend\'s HTTP response did not match the probe setting. Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1} (Il codice di stato della risposta HHTP del back-end non corrisponde all'impostazione del probe. Previsto: {HTTPStatusCode0} Ricevuto: {HTTPStatusCode1}).

**Causa:** una volta stabilita la connessione TCP e al termine di un handshake TLS (se TLS è abilitato), il gateway applicazione invierà il probe come richiesta HTTP GET al server back-end. Come descritto in precedenza, il probe predefinito corrisponde a \<protocol\> ://127.0.0.1: \<port\> /e considera i codici di stato della risposta da Rage 200 a 399 come integri. Se il server restituisce qualsiasi altro codice di stato, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** a seconda del codice di risposta del server back-end, è possibile completare i passaggi seguenti. Di seguito sono elencati alcuni codici di stato comuni:

| **Error (Errore) (Error (Errore)e)** | **Actions** |
| --- | --- |
| Mancata corrispondenza del codice di stato del probe: errore 401 ricevuto | Verificare se il server back-end richiede l'autenticazione. I probe del gateway applicazione non possono passare le credenziali per l'autenticazione. Consentire \"HTTP 401\" in una corrispondenza del codice di stato del probe o eseguire una verifica tramite probe in un percorso in cui il server non richiede l'autenticazione. | |
| Mancata corrispondenza del codice di stato del probe: errore 403 ricevuto | Accesso negato. Controllare se l'accesso al percorso è consentito nel server back-end. | |
| Mancata corrispondenza del codice di stato del probe: errore 404 ricevuto | Pagina non trovata. Controllare se il percorso del nome host è accessibile nel server back-end. Modificare i parametri del nome host o del percorso in un valore accessibile. | |
| Mancata corrispondenza del codice di stato del probe: errore 405 ricevuto | Le richieste di probe per il gateway applicazione usano il metodo HTTP GET. Verificare se il server consente questo metodo. | |
| Mancata corrispondenza del codice di stato del probe: errore 500 ricevuto | Errore interno del server. Verificare l'integrità del server back-end e se i servizi sono in esecuzione. | |
| Mancata corrispondenza del codice di stato del probe: errore 503 ricevuto | servizio non disponibile. Verificare l'integrità del server back-end e se i servizi sono in esecuzione. | |

In alternativa, se si ritiene che la risposta sia legittima e si vuole che il gateway applicazione accetti altri codici di stato come integri, è possibile creare un probe personalizzato. Questo approccio è utile in situazioni in cui il sito Web back-end richiede l'autenticazione. Poiché le richieste di probe non contengono credenziali utente, non riusciranno e verrà restituito un codice di stato HTTP 401 dal server back-end.

Per creare un probe personalizzato, seguire [questa procedura](./application-gateway-create-probe-portal.md).

#### <a name="http-response-body-mismatch"></a>Mancata corrispondenza del corpo della risposta HTTP

**Messaggio:** Body of the backend\'s HTTP response did not match the probe setting. Received response body does not contain {string} (Il corpo della risposta HTTP del back-end non corrisponde all'impostazione del probe. Il corpo della risposta ricevuta non contiene {string}).

**Causa:** quando si crea un probe personalizzato, è possibile contrassegnare un server back-end come integro attraverso la corrispondenza a una stringa del corpo della risposta. Ad esempio, è possibile configurare il gateway applicazione in modo da accettare "non autorizzato" come stringa per la corrispondenza. Se la risposta del server back-end per la richiesta di probe contiene la stringa **non autorizzato** , il server verrà contrassegnato come integro. In caso contrario, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** Per risolvere il problema, seguire questa procedura:

1.  Accedere al server back-end in locale o da un computer client nel percorso di probe e controllare il corpo della risposta.

1.  Verificare che il corpo della risposta nella configurazione del probe personalizzato del gateway applicazione corrisponda a quanto configurato.

1.  In caso di mancata corrispondenza, modificare la configurazione del probe in modo che includa il corretto valore della stringa da accettare.

Per altre informazioni, vedere [Corrispondenza del probe del gateway applicazione](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> Per tutti i messaggi di errore correlati a TLS e per altre informazioni sul comportamento dell'indicazione nome server e sulle differenze tra lo SKU V1 e V2, vedere la pagina [Panoramica di TLS](ssl-overview.md).


#### <a name="backend-server-certificate-invalid-ca"></a>Autorità di certificazione del server back-end non valida

**Messaggio:** The server certificate used by the backend is not signed by a well-known Certificate Authority (CA). Consentire il back-end nel gateway applicazione caricando il certificato radice del certificato del server usato dal back-end.

**Causa:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per considerare integro il server.
Affinché un certificato TLS/SSL venga considerato attendibile, tale certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile, ad esempio se è stato usato un certificato autofirmato, gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

**Soluzione:** seguire questa procedura per esportare e caricare il certificato radice attendibile nel gateway applicazione. La procedura è per i client Windows.

1.  Accedere al computer in cui è ospitata l'applicazione.

1.  Premere Windows+R o fare clic con il pulsante destro del mouse sul pulsante **Start** e quindi scegliere **Esegui**.

1.  Immettere `certmgr.msc` e premere INVIO. È anche possibile cercare Gestione certificati nel menu **Start**.

1.  Individuare il certificato, in genere in `\Certificates - Current User\\Personal\\Certificates\`, e aprirlo.

1.  Selezionare il certificato radice e quindi **Visualizza certificato**.

1.  In Proprietà certificato selezionare la scheda **Dettagli**.

1.  Nella scheda **Dettagli** selezionare l'opzione **Copia su file** e salvare il file nel formato X.509 (CER) con codifica base 64.

1.  Aprire la pagina **Applicazioni HTTP** del gateway applicazione nel portale di Azure.

1. Aprire le impostazioni HTTP, selezionare **Aggiungi certificato** e individuare il file del certificato appena salvato.

1. Selezionare **Salva** per salvare le impostazioni HTTP.

In alternativa, è possibile esportare il certificato radice da un computer client accedendo direttamente al server (ignorando il gateway applicazione) tramite il browser ed esportando il certificato radice dal browser.

Per altre informazioni su come estrarre e caricare certificati radice attendibili nel gateway applicazione, vedere [Esportare un certificato radice attendibile (per lo SKU V2)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Mancata corrispondenza del certificato radice attendibile

**Messaggio:** The root certificate of the server certificate used by the backend does not match the trusted root certificate added to the application gateway. Ensure that you add the correct root certificate to whitelist the backend (Il certificato radice del certificato del server usato dal back-end non corrisponde al certificato radice attendibile aggiunto al gateway applicazione. Assicurarsi di aggiungere il certificato radice corretto per aggiungere il back-end all'elenco elementi consentiti).

**Causa:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per considerare integro il server.
Affinché un certificato TLS/SSL venga considerato attendibile, il certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile, ad esempio è stato usato un certificato autofirmato, gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

Il certificato caricato nelle impostazioni HTTP del gateway applicazione deve corrispondere al certificato radice del certificato del server back-end.

**Soluzione:** Se si riceve questo messaggio di errore, il certificato caricato nel gateway applicazione e quello caricato nel server back-end non corrispondono.

Seguire i passaggi da 1 a 11 della procedura precedente per caricare il certificato radice attendibile corretto nel gateway applicazione.

Per altre informazioni su come estrarre e caricare certificati radice attendibili nel gateway applicazione, vedere [Esportare un certificato radice attendibile (per lo SKU V2)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Questo errore può verificarsi anche se il server back-end non scambia la catena completa del certificato, tra cui radice > livello intermedio (se applicabile) > foglia, durante l'handshake TLS. Per verificare, è possibile usare comandi OpenSSL da qualsiasi client e connettersi al server back-end con le impostazioni configurate nel probe del gateway applicazione.

Ad esempio:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se l'output non mostra la catena completa del certificato restituito, esportare di nuovo il certificato con la catena completa, incluso il certificato radice. Configurare il certificato nel server back-end. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comune del certificato del back-end non valido

**Messaggio:** The Common Name (CN) of the backend certificate does not match the host header of the probe (Il nome comune del certificato del back-end non corrisponde all'intestazione host del probe).

**Causa:** il gateway applicazione verifica se il nome host specificato nelle impostazioni HTTP del back-end corrisponde al nome comune presentato dal certificato TLS/SSL del server back-end. Si tratta del comportamento degli SKU (V2) Standard_v2 e WAF_v2. L'indicazione nome server (SNI) degli SKU Standard e WAF (V1) è impostata come FQDN nell'indirizzo del pool back-end. Per altre informazioni sul comportamento delle indicazioni nome server e sulle differenze tra gli SKU V1 e V2, vedere [Panoramica della terminazione TLS e di TLS end-to-end con il gateway applicazione](ssl-overview.md).

Se nello SKU V2 è presente un probe predefinito (non è stato configurato e associato alcun probe personalizzato), L'indicazione nome server verrà impostata dal nome host indicato nelle impostazioni HTTP. In alternativa, se nelle impostazioni HTTP è indicato "Nome host di selezione dall'indirizzo back-end", in cui il pool di indirizzi back-end contiene un FQDN valido, verrà applicata questa impostazione.

Se è presente un probe predefinito associato alle impostazioni HTTP, l'indicazione nome server verrà impostata dal nome host indicato nella configurazione del probe personalizzato. In alternativa, se nel probe personalizzato è selezionato **Nome host di selezione dall'indirizzo back-end** , l'indicazione nome server verrà impostata dal nome host indicato nelle impostazioni HTTP.

Se nelle impostazioni HTTP è impostato **Nome host di selezione dall'indirizzo back-end** , il pool di indirizzi back-end deve contenere un FQDN valido.

Se si riceve questo messaggio di errore, il nome comune del certificato del back-end non corrisponde al nome host configurato nel probe personalizzato o nelle impostazioni HTTP (se è selezionato **Nome host di selezione dalle impostazioni HTTP back-end** ). Se si usa un probe predefinito, il nome host verrà impostato come **127.0.0.1**. Se non si tratta di un valore desiderato, è necessario creare un probe personalizzato e associarlo alle impostazioni HTTP.

**Soluzione:**

Per risolvere il problema, seguire questa procedura.

Per Windows:

1.  accedere al computer in cui è ospitata l'applicazione.

1.  Premere Windows+R o fare clic con il pulsante destro del mouse sul pulsante **Start** e scegliere **Esegui**.

1.  Immettere **certmgr.msc** e premere INVIO. È anche possibile cercare Gestione certificati nel menu **Start**.

1.  Individuare il certificato, in genere in `\Certificates - Current User\\Personal\\Certificates`, e aprirlo.

1.  Nella scheda **Dettagli** verificare il valore di **Oggetto** del certificato.

1.  Verificare il nome comune del certificato nei dettagli e immettere lo stesso nel campo del nome host del probe personalizzato o nelle impostazioni HTTP (se è selezionato **Nome host di selezione dalle impostazioni HTTP back-end** ). Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per il dominio o immettere il nome host corretto nella configurazione del probe personalizzato o delle impostazioni HTTP.

Per Linux con OpenSSL:

1.  Eseguire questo comando in OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Dalle proprietà visualizzate individuare il nome comune del certificato e immettere lo stesso nome nel campo del nome host delle impostazioni HTTP. Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per il dominio o immettere il nome host corretto nella configurazione del probe personalizzato o delle impostazioni HTTP.

#### <a name="backend-certificate-is-invalid"></a>Il certificato del back-end non è valido

**Messaggio:** Il certificato di back-end non è valido. Current date is not within the \"Valid from\" and \"Valid to\" date range on the certificate (La data corrente non rientra nell'intervallo di date specificato in Valido da e Valido fino al).

**Causa:** ogni certificato viene specificato con un intervallo di validità e la connessione HTTPS non sarà protetta se il certificato TLS/SSL del server non è valido. La data corrente deve essere inclusa nell'intervallo specificato in **Valido da** e **Valido fino a**. In caso contrario, il certificato viene considerato non valido e verrà creato un problema di sicurezza in cui il gateway applicazione contrassegna il server back-end come non integro.

**Soluzione:** se il certificato TLS/SSL è scaduto, rinnovare il certificato con il fornitore e aggiornare le impostazioni del server con il nuovo certificato. Se si tratta di un certificato autofirmato, è necessario generare un certificato valido e caricare il certificato radice nelle impostazioni HTTP del gateway applicazione. A tale scopo, seguire questi passaggi:

1.  Aggiornare le impostazioni HTTP del gateway applicazione nel portale.

1.  Selezionare l'impostazione con il certificato scaduto, selezionare **Aggiungi certificato** e aprire il nuovo file di certificato.

1.  Rimuovere il certificato precedente usando l'icona **Elimina** accanto al certificato e quindi selezionare **Salva**.

#### <a name="certificate-verification-failed"></a>Verifica del certificato non riuscita

**Messaggio:** The validity of the backend certificate could not be verified. To find out the reason, check OpenSSL diagnostics for the message associated with error code {errorCode}. (Non è stato possibile verificare la validità del certificato del back-end. Per scoprire il motivo, controllare nella diagnostica di OpenSSL il messaggio associato al codice di errore {errorCode}).

**Causa:** questo errore si verifica quando il gateway applicazione non è in grado di verificare la validità del certificato.

**Soluzione:** per risolvere il problema, verificare che il certificato nel server sia stato creato correttamente. Ad esempio, è possibile usare [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) per verificare il certificato e le relative proprietà e provare a ricaricare il certificato nelle impostazioni HTTP del gateway applicazione.

<a name="backend-health-status-unknown"></a>Stato integrità del back-end: sconosciuto
-------------------------------
Se lo stato integrità del back-end è Sconosciuto, quanto visualizzato nel portale sarà simile allo screenshot seguente:

![Integrità del back-end del gateway applicazione - Sconosciuto](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Questo comportamento può verificarsi per uno o più dei motivi seguenti:

1.  L'NSG nella subnet del gateway applicazione blocca l'accesso in ingresso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU V2) da "Internet".
1.  La route definita dall'utente nella subnet del gateway applicazione è impostata sulla route predefinita (0.0.0.0/0) e l'hop successivo non è specificato come "Internet".
1.  La route predefinita viene annunciata da una connessione ExpressRoute/VPN a una rete virtuale tramite BGP.
1.  Il server DNS personalizzato è configurato in una rete virtuale che non è in grado di risolvere i nomi di dominio pubblici.
1.  Il gateway applicazione si trova in uno stato non integro.

**Soluzione:**

1.  controllare se l'NSG blocca l'accesso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU V2) da **Internet** :

    a.  Nella scheda **Panoramica** del gateway applicazione selezionare il collegamento **Rete virtuale/subnet**.

    b.  Nella scheda **Subnet** della rete virtuale selezionare la subnet in cui è stato distribuito il gateway applicazione.

    c.  Controllare se è configurato un NSG.

    d.  Se è configurato un NSG, cercare la risorsa NSG nella scheda **Cerca** o in **Tutte le risorse**.

    e.  Nella sezione **Regole in ingresso** aggiungere una regola in ingresso per consentire l'intervallo di porte di destinazione 65503-65534 per lo SKU V1 o 65200-65535 per lo SKU V2 con **Origine** impostato su **Any** o **Internet**.

    f.  Selezionare **Salva** e verificare che sia possibile visualizzare il back-end come integro. In alternativa, è possibile usare [PowerShell o l'interfaccia della riga di comando](../virtual-network/manage-network-security-group.md) a questo scopo.

1.  Controllare se la route definita dall'utente include una route predefinita (0.0.0.0/0) con l'hop successivo non impostato come **Internet** :
    
    a.  Seguire i passaggi 1a e 1b per determinare la subnet.

    b.  Controllare se è configurata una route definita dall'utente. Se sì, cercare la risorsa sulla barra di ricerca o in **Tutte le risorse**.

    c.  Controllare se sono presenti route predefinite (0.0.0.0/0) con l'hop successivo non impostato come **Internet**. Se l'impostazione è **Appliance virtuale** o **Gateway di rete virtuale** , è necessario assicurarsi che l'appliance virtuale o il dispositivo locale sia in grado di reinstradare correttamente il pacchetto alla destinazione Internet senza modificarlo.

    d.  In caso contrario, modificare l'hop successivo in **Internet** , selezionare **Salva** e verificare l'integrità del back-end.

1.  La route predefinita viene annunciata da una connessione ExpressRoute/VPN alla rete virtuale tramite BGP:

    a.  Se è presente una connessione ExpressRoute/VPN alla rete virtuale tramite BGP e se si annuncia una route predefinita, è necessario assicurarsi che il pacchetto venga reinstradato alla destinazione Internet senza modificarlo. Per la verifica, è possibile usare l'opzione **Risoluzione dei problemi di connessione** nel portale del gateway applicazione.

    b.  Scegliere la destinazione manualmente come qualsiasi indirizzo IP instradabile su Internet, ad esempio 1.1.1.1. Impostare la porta di destinazione come qualsiasi valore e verificare la connettività.

    c.  Se l'hop successivo è il gateway di rete virtuale, potrebbe essere presente una route predefinita annunciata tramite ExpressRoute o VPN.

1.  Se nella rete virtuale è configurato un server DNS personalizzato, verificare che il server (o i server) sia in grado di risolvere i domini pubblici. La risoluzione dei nomi di dominio pubblici può essere necessaria in scenari in cui il gateway applicazione deve raggiungere domini esterni come server OCSP o per controllare lo stato di revoca del certificato.

1.  Per verificare che il gateway applicazione sia integro e in esecuzione, passare all'opzione **Integrità risorse** nel portale e verificare che lo stato sia **Integro**. Se viene visualizzato lo stato **Non integro** o **Danneggiato** , [contattare il supporto tecnico](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Passaggi successivi
----------

Per altre informazioni, vedere [Diagnostica e registrazione del gateway applicazione](./application-gateway-diagnostics.md).