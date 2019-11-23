---
title: Risolvere i problemi di integrità back-end in applicazione Azure gateway
description: Viene descritto come risolvere i problemi di integrità back-end per applicazione Azure gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001677"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Risolvere i problemi di integrità back-end nel gateway applicazione
==================================================

<a name="overview"></a>Overview
--------

Per impostazione predefinita, applicazione Azure gateway esegue il probe dei server back-end per verificarne lo stato di integrità e per verificare se sono pronti per gestire le richieste. Gli utenti possono anche creare Probe personalizzati per citare il nome host, il percorso in cui eseguire il probe e i codici di stato da accettare come integri. In ogni caso, se il server back-end non risponde correttamente, il gateway applicazione contrassegna il server come non integro e interrompe l'invio delle richieste al server. Quando il server inizia a rispondere correttamente, il gateway applicazione riprende l'invio delle richieste.

### <a name="how-to-check-backend-health"></a>Come controllare l'integrità back-end

Per verificare l'integrità del pool back-end, è possibile usare la pagina **integrità back-end** nella portale di Azure. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), l' [interfaccia](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)della riga di comando o l' [API REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

Lo stato recuperato da uno di questi metodi può essere uno dei seguenti:

- Healthy

- Non integro

- Sconosciuto

Se lo stato di integrità back-end di un server è integro, significa che il gateway applicazione inoltrerà le richieste a tale server. Tuttavia, se l'integrità back-end per tutti i server in un pool back-end non è integra o è sconosciuta, potrebbero verificarsi problemi quando si tenta di accedere alle applicazioni. Questo articolo descrive i sintomi, le cause e la risoluzione di ogni errore visualizzato.

<a name="backend-health-status-unhealthy"></a>Stato di integrità back-end: non integro
-------------------------------

Se lo stato di integrità back-end non è integro, la visualizzazione del portale sarà simile alla schermata seguente:

![Integrità back-end del gateway applicazione-non integro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

In alternativa, se si usa una query Azure PowerShell, CLI o API REST di Azure, si otterrà una risposta simile alla seguente:
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
Dopo aver ricevuto lo stato di un server back-end non integro per tutti i server in un pool back-end, le richieste non vengono inviate ai server e il gateway applicazione restituisce un errore "502 gateway non valido" al client richiedente. Per risolvere il problema, controllare la colonna **Dettagli** nella scheda **integrità back-end** .

Il messaggio visualizzato nella colonna **Dettagli** fornisce informazioni dettagliate sul problema e, in base a tali informazioni, è possibile iniziare la risoluzione del problema.

> [!NOTE]
> La richiesta di probe predefinita viene inviata nel formato \<protocollo\>://127.0.0.1:\<porta\>/. Ad esempio, http://127.0.0.1:80 per un probe http sulla porta 80. Solo i codici di stato HTTP da 200 a 399 sono considerati integri. Il protocollo e la porta di destinazione vengono ereditati dalle impostazioni HTTP. Se si vuole che il gateway applicazione Probe su un protocollo, un nome host o un percorso diverso e riconosca un codice di stato diverso come integro, configurare un probe personalizzato e associarlo alle impostazioni HTTP.

<a name="error-messages"></a>messaggi di errore
------------------------
#### <a name="backend-server-timeout"></a>Timeout server back-end

**Messaggio:** Il tempo impiegato dal back-end per rispondere al Probe di integrità del gateway applicazione\'s è superiore alla soglia di timeout nell'impostazione del probe.

**Motivo:** Dopo che il gateway applicazione ha inviato una richiesta di probe HTTP (S) al server back-end, attende una risposta dal server back-end per un periodo configurato. Se il server back-end non risponde entro il periodo configurato (il valore di timeout), viene contrassegnato come non integro fino a quando non viene riavviata la risposta entro il periodo di timeout configurato.

**Risoluzione:** Verificare il motivo per cui l'applicazione o il server back-end non risponde entro il periodo di timeout configurato e controllare anche le dipendenze dell'applicazione. Verificare, ad esempio, se nel database sono presenti problemi che potrebbero causare un ritardo nella risposta. Se si è in grado di riconoscere il comportamento dell'applicazione e deve rispondere solo dopo il valore di timeout, aumentare il valore di timeout dalle impostazioni del probe personalizzato. È necessario disporre di un probe personalizzato per modificare il valore di timeout. Per informazioni su come configurare un probe personalizzato, [vedere la pagina della documentazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Per aumentare il valore di timeout, attenersi alla procedura seguente:

1.  Accedere direttamente al server back-end e controllare il tempo impiegato dal server per rispondere a tale pagina. È possibile usare qualsiasi strumento per accedere al server back-end, incluso un browser con gli strumenti di sviluppo.

1.  Dopo aver individuato il tempo impiegato dall'applicazione per rispondere, selezionare la scheda **Probe integrità** e quindi selezionare il probe associato alle impostazioni http.

1.  Immettere un valore di timeout maggiore del tempo di risposta dell'applicazione, in secondi.

1.  Salvare le impostazioni del probe personalizzato e verificare se l'integrità back-end è ora visualizzata come integro.

#### <a name="dns-resolution-error"></a>Errore di risoluzione DNS

**Messaggio:** Il gateway applicazione non è riuscito a creare un probe per questo back-end. Questo problema si verifica in genere quando il nome di dominio completo del back-end non è stato immesso correttamente. 

**Motivo:** Se il pool back-end è di tipo indirizzo IP/FQDN o servizio app, il gateway applicazione viene risolto nell'indirizzo IP del nome di dominio completo immesso tramite Domain Name System (DNS) (personalizzato o predefinito di Azure) e tenta di connettersi al server sulla porta TCP indicata nelle impostazioni HTTP. Tuttavia, se questo messaggio viene visualizzato, suggerisce che il gateway applicazione non è stato in grado di risolvere correttamente l'indirizzo IP del FQDN immesso.

**Risoluzione:**

1.  Verificare che il nome FQDN immesso nel pool back-end sia corretto e che si tratta di un dominio pubblico, quindi provare a risolverlo dal computer locale.

1.  Se è possibile risolvere l'indirizzo IP, potrebbe essersi verificato un errore nella configurazione DNS nella rete virtuale.

1.  Controllare se la rete virtuale è configurata con un server DNS personalizzato. In tal caso, controllare il motivo per cui non è possibile risolvere l'indirizzo IP del nome di dominio completo specificato.

1.  Se si usa il servizio DNS predefinito di Azure, contattare il registrar per sapere se il mapping di un record o un record CNAME è stato completato correttamente.

1.  Se il dominio è privato o interno, provare a risolverlo da una macchina virtuale nella stessa rete virtuale. Se è possibile risolverlo, riavviare il gateway applicazione e verificare di nuovo. Per riavviare il gateway applicazione, è necessario [arrestare](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [iniziare](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) usando i comandi di PowerShell descritti in queste risorse collegate.

#### <a name="tcp-connect-error"></a>Errore di connessione TCP

**Messaggio:** Il gateway applicazione non è riuscito a connettersi al back-end.
Verificare che il back-end risponda sulla porta usata per il probe.
Controllare anche se NSG/UDR/firewall blocca l'accesso all'IP e alla porta di questo back-end

**Motivo:** Dopo la fase di risoluzione DNS, il gateway applicazione tenta di connettersi al server back-end sulla porta TCP configurata nelle impostazioni HTTP. Se il gateway applicazione non è in grado di stabilire una sessione TCP sulla porta specificata, il probe viene contrassegnato come non integro con questo messaggio.

**Soluzione:** Se viene visualizzato questo errore, attenersi alla procedura seguente:

1.  Verificare se è possibile connettersi al server back-end sulla porta indicata nelle impostazioni HTTP usando un browser o PowerShell. Ad esempio, eseguire il comando seguente: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Se la porta indicata non è la porta desiderata, immettere il numero di porta corretto per la connessione del gateway applicazione al server back-end

1.  Se non è possibile connettersi alla porta anche dal computer locale, eseguire le operazioni seguenti:

    a.  Controllare le impostazioni del gruppo di sicurezza di rete (NSG) della scheda di rete e della subnet del server back-end e se sono consentite le connessioni in ingresso alla porta configurata. In caso affermativo, creare una nuova regola per consentire le connessioni. Per informazioni su come creare regole NSG, [vedere la pagina della documentazione](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Verificare che le impostazioni NSG della subnet del gateway applicazione consentano il traffico pubblico e privato in uscita, in modo che sia possibile stabilire una connessione. Vedere la pagina del documento fornita nel passaggio 3a per altre informazioni su come creare regole NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controllare le impostazioni delle route definite dall'utente (UDR) del gateway applicazione e la subnet del server back-end per eventuali anomalie di routing. Verificare che UDR non stia indirizzando il traffico dalla subnet back-end. Ad esempio, verificare la presenza di route per le appliance virtuali di rete o le route predefinite annunciate alla subnet del gateway applicazione tramite Azure ExpressRoute e/o VPN.

    d.  Per verificare le route e le regole valide per una scheda di rete, è possibile usare i comandi di PowerShell seguenti:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Se non si riscontrano problemi con NSG o UDR, controllare il server back-end per individuare problemi relativi all'applicazione che impediscono ai client di stabilire una sessione TCP sulle porte configurate. Ecco alcuni aspetti da controllare:

    a.  Aprire un prompt dei comandi (Win + R-\> cmd), immettere `netstat`e premere INVIO.

    b.  Controllare se il server è in ascolto sulla porta configurata. Ad esempio:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Se non è in ascolto sulla porta configurata, controllare le impostazioni del server Web. Ad esempio: binding del sito in IIS, blocco server in NGINX e host virtuale in Apache.

    d.  Controllare le impostazioni del firewall del sistema operativo per assicurarsi che il traffico in ingresso verso la porta sia consentito.

#### <a name="http-status-code-mismatch"></a>Mancata corrispondenza del codice di stato HTTP

**Messaggio:** Il codice di stato della risposta HTTP back-end\'s non corrisponde all'impostazione del probe. Previsto: {HTTPStatusCode0} ricevuto: {HTTPStatusCode1}.

**Motivo:** Una volta stabilita la connessione TCP e viene eseguito un handshake SSL (se SSL è abilitato), il gateway applicazione invierà il probe come richiesta HTTP GET al server back-end. Come descritto in precedenza, il probe predefinito sarà \<protocollo\>://127.0.0.1:\<porta\>/e considera i codici di stato della risposta in Rage 200 fino a 399 come integri. Se il server restituisce un altro codice di stato, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** A seconda del codice di risposta del server back-end, è possibile eseguire i passaggi seguenti. Di seguito sono elencati alcuni codici di stato comuni:

| **Errore** | **Actions** |
| --- | --- |
| Mancata corrispondenza del codice di stato Probe: ricevuto 401 | Verificare che il server back-end richieda l'autenticazione. I probe del gateway applicazione non possono passare le credenziali per l'autenticazione a questo punto. Consentire \"\" HTTP 401 in una corrispondenza del codice di stato di un probe o eseguire il probe in un percorso in cui il server non richiede l'autenticazione. | |
| Mancata corrispondenza del codice di stato Probe: ricevuto 403 | Accesso non consentito. Controllare se l'accesso al percorso è consentito nel server back-end. | |
| Mancata corrispondenza del codice di stato Probe: ricevuto 404 | Pagina non trovata. Controllare se il percorso del nome host è accessibile nel server back-end. Modificare il nome host o il parametro Path in un valore accessibile. | |
| Mancata corrispondenza del codice di stato Probe: ricevuto 405 | Le richieste di probe per il gateway applicazione usano il metodo HTTP GET. Controllare se il server consente questo metodo. | |
| Mancata corrispondenza del codice di stato Probe: ricevuto 500 | Errore interno del server. Verificare l'integrità del server back-end e se i servizi sono in esecuzione. | |
| Mancata corrispondenza del codice di stato Probe: ricevuto 503 | Servizio non disponibile. Verificare l'integrità del server back-end e se i servizi sono in esecuzione. | |

In alternativa, se si ritiene che la risposta sia legittima e si desidera che il gateway applicazione accetti altri codici di stato come integri, è possibile creare un probe personalizzato. Questo approccio è utile nelle situazioni in cui il sito Web back-end necessita di autenticazione. Poiché le richieste di probe non contengono credenziali utente, avranno esito negativo e verrà restituito un codice di stato HTTP 401 dal server back-end.

Per creare un probe personalizzato, attenersi alla [seguente procedura](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Mancata corrispondenza del corpo della risposta HTTP

**Messaggio:** Il corpo della risposta HTTP back-end\'s non corrisponde all'impostazione del probe. Il corpo della risposta ricevuta non contiene {String}.

**Motivo:** Quando si crea un probe personalizzato, è possibile contrassegnare un server back-end come integro associando una stringa dal corpo della risposta. Ad esempio, è possibile configurare il gateway applicazione in modo che accetti "non autorizzato" come stringa per la corrispondenza. Se la risposta del server back-end per la richiesta del probe contiene la stringa non **autorizzata**, verrà contrassegnata come integro. In caso contrario, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** Per risolvere il problema, attenersi alla seguente procedura:

1.  Accedere al server back-end localmente o da un computer client nel percorso Probe e verificare il corpo della risposta.

1.  Verificare che il corpo della risposta nella configurazione del probe personalizzato del gateway applicazione corrisponda a quanto configurato.

1.  Se non corrispondono, modificare la configurazione del probe in modo che sia in grado di accettare il valore stringa corretto.

Altre informazioni sulla [corrispondenza dei probe del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>CA del certificato del server back-end non valida

**Messaggio:** Il certificato server usato dal back-end non è firmato da un'autorità di certificazione (CA) Nota. Inserire nell'elenco elementi consentiti il back-end nel gateway applicazione caricando il certificato radice del certificato del server usato dal back-end.

**Motivo:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per poter considerare integro il server.
Affinché un certificato SSL venga considerato attendibile, il certificato del server back-end deve essere emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile (ad esempio, se è stato usato un certificato autofirmato), gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

**Soluzione:** Seguire questa procedura per esportare e caricare il certificato radice attendibile nel gateway applicazione. Questa procedura è per i client Windows.

1.  Accedere al computer in cui è ospitata l'applicazione.

1.  Selezionare Win + R oppure fare clic con il pulsante destro del mouse sul pulsante **Start** , quindi scegliere **Esegui**.

1.  Immettere `certmgr.msc` e premere INVIO. È anche possibile cercare gestione certificati dal menu **Start** .

1.  Individuare il certificato, in genere in `\Certificates - Current User\\Personal\\Certificates\`e aprirlo.

1.  Selezionare il certificato radice e quindi fare clic su **Visualizza certificato**.

1.  In Proprietà certificato selezionare la scheda **Dettagli** .

1.  Nella scheda **Dettagli** selezionare l'opzione **copia su file** e salvare il file nel file X. 509 con codifica base 64 (. Formato CER).

1.  Aprire la pagina **delle impostazioni** http del gateway applicazione nella portale di Azure.

1. Aprire le impostazioni HTTP, selezionare **Aggiungi certificato**e individuare il file del certificato appena salvato.

1. Selezionare **Save (Salva** ) per salvare le impostazioni http.

In alternativa, è possibile esportare il certificato radice da un computer client accedendo direttamente al server (ignorando il gateway applicazione) tramite il browser ed esportando il certificato radice dal browser.

Per altre informazioni su come estrarre e caricare i certificati radice trusted nel gateway applicazione, vedere [esportare un certificato radice trusted (per lo SKU v2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Mancata corrispondenza del certificato radice attendibile

**Messaggio:** Il certificato radice del certificato del server usato dal back-end non corrisponde al certificato radice attendibile aggiunto al gateway applicazione. Assicurarsi di aggiungere il certificato radice corretto all'elenco elementi consentiti del back-end

**Motivo:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per poter considerare integro il server.
Affinché un certificato SSL venga considerato attendibile, il certificato del server back-end deve essere emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile (ad esempio, è stato usato un certificato autofirmato), gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

Il certificato caricato nelle impostazioni HTTP del gateway applicazione deve corrispondere al certificato radice del certificato del server back-end.

**Soluzione:** Se viene visualizzato questo messaggio di errore, esiste una mancata corrispondenza tra il certificato che è stato caricato nel gateway applicazione e quello che è stato caricato nel server back-end.

Seguire i passaggi 1-11 nel metodo precedente per caricare il certificato radice attendibile corretto nel gateway applicazione.

Per altre informazioni su come estrarre e caricare i certificati radice trusted nel gateway applicazione, vedere [esportare un certificato radice trusted (per lo SKU v2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Questo errore può verificarsi anche se il server back-end non scambia la catena completa del certificato, inclusa la radice > intermedio (se applicabile) > foglia durante l'handshake TLS. Per verificare, è possibile usare i comandi OpenSSL da qualsiasi client e connettersi al server back-end usando le impostazioni configurate nel probe del gateway applicazione.

Ad esempio:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se l'output non Mostra la catena completa del certificato restituito, esportare di nuovo il certificato con la catena completa, incluso il certificato radice. Configurare il certificato nel server back-end. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comune (CN) del certificato back-end non valido

**Messaggio:** Il nome comune (CN) del certificato back-end non corrisponde all'intestazione host del probe.

**Motivo:** Il gateway applicazione verifica se il nome host specificato nelle impostazioni HTTP back-end corrisponde a quello del CN presentato dal certificato SSL del server back-end. Questo è Standard_v2 e WAF_v2 comportamento dello SKU. Lo SKU standard e WAF Indicazione nome server (SNI) viene impostato come FQDN nell'indirizzo del pool back-end.

Nello SKU V2, se è presente un probe predefinito (nessun Probe personalizzato è stato configurato e associato), SNI verrà impostato dal nome host indicato nelle impostazioni HTTP. In alternativa, se "pick host name from back-end Address" è indicato nelle impostazioni HTTP, dove il pool di indirizzi back-end contiene un FQDN valido, questa impostazione verrà applicata.

Se è presente un probe personalizzato associato alle impostazioni HTTP, SNI verrà impostato dal nome host indicato nella configurazione del probe personalizzato. In alternativa, se nel Probe personalizzato è selezionata l'opzione **Seleziona nome host da impostazioni http back-end** , SNI verrà impostato dal nome host indicato nelle impostazioni http.

Se l'impostazione **Seleziona nome host dall'indirizzo back-end** è impostata nelle impostazioni http, il pool di indirizzi back-end deve contenere un nome di dominio completo valido.

Se viene visualizzato questo messaggio di errore, il CN del certificato back-end non corrisponde al nome host configurato nel Probe personalizzato o nelle impostazioni HTTP (se è selezionata l'opzione **Seleziona nome host da impostazioni http back-end** ). Se si usa un probe predefinito, il nome host verrà impostato come **127.0.0.1**. Se non si tratta di un valore desiderato, è necessario creare un probe personalizzato e associarlo alle impostazioni HTTP.

**Soluzione:**

Per risolvere il problema, seguire questa procedura.

Per Windows:

1.  Accedere al computer in cui è ospitata l'applicazione.

1.  Selezionare Win + R oppure fare clic con il pulsante destro del mouse sul pulsante **Start** e scegliere **Esegui**.

1.  Immettere **certmgr. msc** e premere INVIO. È anche possibile cercare gestione certificati dal menu **Start** .

1.  Individuare il certificato (in genere in `\Certificates - Current User\\Personal\\Certificates`) e aprire il certificato.

1.  Nella scheda **Dettagli** controllare l' **oggetto**del certificato.

1.  Verificare il CN del certificato dai dettagli e immettere lo stesso nel campo nome host del probe personalizzato o nelle impostazioni HTTP (se è selezionata l'opzione **Seleziona nome host da impostazioni http back-end** ). Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per tale dominio oppure immettere il nome host corretto nella configurazione del probe personalizzato o dell'impostazione HTTP.

Per Linux con OpenSSL:

1.  Eseguire questo comando in OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Dalle proprietà visualizzate, trovare il CN del certificato e immettere lo stesso nel campo nome host delle impostazioni http. Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per tale dominio oppure immettere il nome host corretto nella configurazione del probe personalizzato o dell'impostazione HTTP.

#### <a name="backend-certificate-is-invalid"></a>Il certificato back-end non è valido

**Messaggio:** Il certificato back-end non è valido. La data corrente non è compresa nell'intervallo di \"valido da\" e \"valido per\" intervallo di date nel certificato.

**Motivo:** Ogni certificato viene specificato con un intervallo di validità e la connessione HTTPS non sarà protetta, a meno che il certificato SSL del server non sia valido. I dati correnti devono essere compresi nell'intervallo **valido da** e **valido a** . In caso contrario, il certificato viene considerato non valido e verrà creato un problema di sicurezza in cui il gateway applicazione contrassegna il server back-end come non integro.

**Soluzione:** Se il certificato SSL è scaduto, rinnovare il certificato con il fornitore e aggiornare le impostazioni del server con il nuovo certificato. Se si tratta di un certificato autofirmato, è necessario generare un certificato valido e caricare il certificato radice nelle impostazioni HTTP del gateway applicazione. A tale scopo, seguire questi passaggi:

1.  Aprire le impostazioni HTTP del gateway applicazione nel portale.

1.  Selezionare l'impostazione con il certificato scaduto, selezionare **Aggiungi certificato**e aprire il nuovo file di certificato.

1.  Rimuovere il certificato precedente usando l'icona di **eliminazione** accanto al certificato e quindi selezionare Save ( **Salva**).

#### <a name="certificate-verification-failed"></a>Verifica del certificato non riuscita

**Messaggio:** Non è stato possibile verificare la validità del certificato back-end. Per individuare il motivo, controllare la diagnostica SSL aperta per il messaggio associato al codice di errore {errorCode}

**Motivo:** Questo errore si verifica quando il gateway applicazione non è in grado di verificare la validità del certificato.

**Soluzione:** Per risolvere questo problema, verificare che il certificato nel server sia stato creato correttamente. Ad esempio, è possibile usare [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) per verificare il certificato e le relative proprietà, quindi provare a ricaricare il certificato nelle impostazioni http del gateway applicazione.

<a name="backend-health-status-unknown"></a>Stato di integrità back-end: sconosciuto
-------------------------------
Se l'integrità back-end viene visualizzata come sconosciuta, la visualizzazione del portale sarà simile alla schermata seguente:

![Stato back-end del gateway applicazione-sconosciuto](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Questo comportamento può verificarsi per uno o più dei seguenti motivi:

1.  Il NSG nella subnet del gateway applicazione blocca l'accesso in ingresso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU v2) da "Internet".
1.  Il UDR nella subnet del gateway applicazione è impostato sulla route predefinita (0.0.0.0/0) e l'hop successivo non è specificato come "Internet".
1.  La route predefinita viene annunciata da una connessione VPN/ExpressRoute a una rete virtuale tramite BGP.
1.  Il server DNS personalizzato è configurato in una rete virtuale che non è in grado di risolvere i nomi di dominio pubblici.
1.  Il gateway applicazione si trova in uno stato non integro.

**Soluzione:**

1.  Controllare se il NSG blocca l'accesso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU v2) da **Internet**:

    a.  Nella scheda **Panoramica** del gateway applicazione selezionare il collegamento **rete virtuale/Subnet** .

    b.  Nella scheda **subnet** della rete virtuale selezionare la subnet in cui è stato distribuito il gateway applicazione.

    c.  Controllare se sono configurati NSG.

    d.  Se è configurato un NSG, cercare la risorsa NSG nella scheda **Cerca** o in tutte le **risorse**.

    e.  Nella sezione **regole in ingresso** aggiungere una regola in ingresso per consentire l'intervallo di porte di destinazione 65503-65534 per lo SKU v1 o 65200-65535 V2 con il set di **origine** come **qualsiasi** o **Internet**.

    f.  Selezionare **Save (Salva** ) e verificare che sia possibile visualizzare il back-end come integro. In alternativa, è possibile eseguire questa operazione tramite [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Controllare se il UDR ha una route predefinita (0.0.0.0/0) con l'hop successivo non impostato come **Internet**:
    
    a.  Seguire i passaggi 1a e 1B per determinare la subnet.

    b.  Controllare se sono stati configurati UDR. Se è presente, cercare la risorsa nella barra di ricerca o in **tutte le risorse**.

    c.  Controllare se sono presenti route predefinite (0.0.0.0/0) con l'hop successivo non impostato come **Internet**. Se l'impostazione è **appliance virtuale** o **gateway di rete virtuale**, è necessario assicurarsi che l'appliance virtuale o il dispositivo locale sia in grado di reindirizzare correttamente il pacchetto alla destinazione Internet senza modificare il pacchetto.

    d.  In caso contrario, modificare l'hop successivo su **Internet**, selezionare **Salva**e verificare l'integrità back-end.

1.  Route predefinita annunciata dalla connessione VPN/ExpressRoute alla rete virtuale su BGP:

    a.  Se si dispone di una connessione VPN o ExpressRoute alla rete virtuale su BGP e se si annuncia una route predefinita, è necessario assicurarsi che il pacchetto venga reindirizzato alla destinazione Internet senza modificarlo. È possibile verificare usando l'opzione per la **risoluzione dei problemi di connessione** nel portale del gateway applicazione.

    b.  Scegliere la destinazione manualmente come qualsiasi indirizzo IP instradabile su Internet, ad esempio 1.1.1.1. Impostare la porta di destinazione come qualsiasi elemento e verificare la connettività.

    c.  Se l'hop successivo è il gateway di rete virtuale, potrebbe essere presente una route predefinita annunciata tramite ExpressRoute o VPN.

1.  Se nella rete virtuale è configurato un server DNS personalizzato, verificare che il server (o i server) sia in grado di risolvere i domini pubblici. La risoluzione dei nomi di dominio pubblico potrebbe essere necessaria negli scenari in cui il gateway applicazione deve raggiungere domini esterni come server OCSP o controllare lo stato di revoca del certificato.

1.  Per verificare che il gateway applicazione sia integro e in esecuzione, passare all'opzione **integrità risorse** nel portale e verificare che lo stato sia **integro**. Se viene visualizzato uno stato non **integro** o **danneggiato** , contattare il [supporto tecnico](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Passaggi successivi
----------

Altre informazioni sulla [diagnostica e sulla registrazione del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
