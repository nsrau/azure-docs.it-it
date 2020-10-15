---
title: Guida alla risoluzione dei problemi del bus di servizio di Azure | Microsoft Docs
description: Informazioni sui suggerimenti e consigli per la risoluzione dei problemi che si possono verificare quando si usa il bus di servizio di Azure.
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: aab7fa53b4af309c68cd91fdb1d25c5771f89828
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261125"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guida alla risoluzione dei problemi del bus di servizio di Azure
Questo articolo fornisce suggerimenti e consigli per la risoluzione dei problemi che possono verificarsi quando si usa il bus di servizio di Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemi di connettività, certificato o timeout
I passaggi seguenti possono essere utili per la risoluzione dei problemi di connettività/certificato/timeout per tutti i servizi in *. servicebus.windows.net. 

- Passare a o [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Consente di controllare se sono presenti problemi di filtro IP, rete virtuale o catena di certificati, che sono comuni quando si usa Java SDK.

    Esempio di messaggio riuscito:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Esempio di messaggio di errore di errore:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Eseguire il comando seguente per verificare se una porta è bloccata sul firewall. Le porte utilizzate sono 443 (HTTPS), 5671 (AMQP) e 9354 (NET Messaging/SBMP). A seconda della libreria usata, vengono usate anche altre porte. Ecco il comando di esempio che controlla se la porta 5671 è bloccata. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni secondo con il servizio. Quindi, è possibile controllare il numero di riuscite/non riuscite e vedere anche latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    È possibile utilizzare comandi equivalenti se si utilizzano altri strumenti, ad esempio `tnc` , `ping` e così via. 
- Ottenere una traccia di rete se i passaggi precedenti non sono utili e analizzarli tramite strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemi che possono verificarsi con gli aggiornamenti o i riavvii del servizio

### <a name="symptoms"></a>Sintomi
- Le richieste possono essere temporaneamente limitate.
- Potrebbe essere presente un calo nei messaggi/richieste in arrivo.
- Il file di log può contenere messaggi di errore.
- È possibile che le applicazioni siano disconnesse dal servizio per alcuni secondi.

### <a name="cause"></a>Causa
Gli aggiornamenti del servizio back-end e i riavvii possono causare questi problemi nelle applicazioni.

### <a name="resolution"></a>Soluzione
Se il codice dell'applicazione usa l'SDK, i criteri di ripetizione dei tentativi sono già incorporati e attivi. L'applicazione si riconnetterà senza conseguenze significative per l'applicazione o il flusso di lavoro.

## <a name="unauthorized-access-send-claims-are-required"></a>Accesso non autorizzato: è necessario inviare attestazioni

### <a name="symptoms"></a>Sintomi 
Questo errore può essere visualizzato quando si tenta di accedere a un argomento del bus di servizio da Visual Studio in un computer locale usando un'identità gestita assegnata dall'utente con autorizzazioni di invio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Causa
L'identità non dispone delle autorizzazioni per accedere all'argomento del bus di servizio. 

### <a name="resolution"></a>Soluzione
Per correggere l'errore, installare la libreria [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Per ulteriori informazioni, vedere [autenticazione di sviluppo locale](../key-vault/general/service-to-service-authentication.md#local-development-authentication). 

Per informazioni su come assegnare le autorizzazioni ai ruoli, vedere [autenticare un'identità gestita con Azure Active Directory per accedere alle risorse del bus di servizio di Azure](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Eccezione del bus di servizio: il token put non è riuscito

### <a name="symptoms"></a>Sintomi
Quando si tenta di inviare più di 1000 messaggi usando la stessa connessione del bus di servizio, verrà visualizzato il messaggio di errore seguente: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Causa
È previsto un limite al numero di token usati per inviare e ricevere messaggi usando una singola connessione a uno spazio dei nomi del bus di servizio. È 1000. 

### <a name="resolution"></a>Soluzione
Aprire una nuova connessione allo spazio dei nomi del bus di servizio per inviare altri messaggi.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Azure Resource Manager eccezioni](service-bus-resource-manager-exceptions.md). Elenca le eccezioni generate durante l'interazione con il bus di servizio di Azure usando Azure Resource Manager (tramite modelli o chiamate dirette).
- [Eccezioni di messaggistica](service-bus-messaging-exceptions.md). Fornisce un elenco di eccezioni generate da .NET Framework per il bus di servizio di Azure.