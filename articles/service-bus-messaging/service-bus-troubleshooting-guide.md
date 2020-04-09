---
title: Guida alla risoluzione dei problemi per il bus di servizio di Azure Documenti Microsoft
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica del bus di servizio di Azure e le azioni consigliate da eseguire quando si verifica l'eccezione.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887775"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guida alla risoluzione dei problemi per il bus di servizio di AzureTroubleshooting guide for Azure Service Bus
Questo articolo fornisce suggerimenti e suggerimenti per la risoluzione di alcuni problemi che possono verificarsi quando si usa il bus di servizio di Azure.This article provides troubleshooting tips and recommendations for a few issues that you may see when using Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemi di connettività, certificato o timeout
I passaggi seguenti possono essere utili per la risoluzione dei problemi di connettività/certificato/timeout per tutti i servizi sottoposti a servicebus.windows.net. 

- Individuare o [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Aiuta con il controllo se si dispone di filtri IP o problemi di rete virtuale o catena di certificati (più comuni quando si utilizza java SDK).

    Un esempio di messaggio di successo:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Un esempio di messaggio di errore di errore:An example of failure error message:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Eseguire il comando seguente per verificare se una porta è bloccata sul firewall. Le porte utilizzate sono 443 (HTTPS), 5671 (AMQP) e 9354 (Net Messaging/SBMP). A seconda della libreria utilizzata, vengono utilizzate anche altre porte. Di seguito è riportato il comando di esempio che controlla se la porta 5671 è bloccata. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti scartati. Questo comando tenterà di stabilire 25 diverse connessioni TCP ogni 1 secondo con il servizio. Quindi, è possibile controllare il numero di essi riuscito/non riuscito e vedere anche la latenza della connessione TCP. È possibile `psping` scaricare lo strumento da [qui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    È possibile utilizzare comandi equivalenti se si `tnc` `ping`utilizzano altri strumenti, ad esempio , e così via. 
- Ottenere una traccia di rete se i passaggi precedenti non aiutano e la analizzano utilizzando strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [il supporto tecnico Microsoft.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemi che possono verificarsi con gli aggiornamenti/riavvii del servizio
Gli aggiornamenti e i riavvii del servizio back-end possono causare il seguente impatto sulle applicazioni:

- Le richieste possono essere momentaneamente limitate.
- Potrebbe esserci un calo nei messaggi/richieste in arrivo.
- Il file di registro può contenere messaggi di errore.
- Le applicazioni potrebbero essere disconnesse dal servizio per alcuni secondi.

Se il codice dell'applicazione utilizza SDK, i criteri di ripetizione dei tentativi sono già incorporati e attivi. L'applicazione si riconnetterà senza alcun impatto significativo sull'applicazione o sul flusso di lavoro.

## <a name="unauthorized-access-send-claims-are-required"></a>Accesso non autorizzato: sono necessarie richieste le attestazioni di invio
Questo errore può essere visualizzato quando si tenta di accedere a un argomento del bus di servizio da Visual Studio in un computer locale utilizzando un'identità gestita assegnata dall'utente con autorizzazioni di invio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Per risolvere questo errore, installare la libreria [Microsoft.Azure.Services.AppAuthentication.To](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) resolve this error, install the Microsoft.Azure.Services.AppAuthentication library.  Per ulteriori informazioni, vedere [Autenticazione di sviluppo locale](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Per informazioni su come assegnare autorizzazioni ai ruoli, vedere [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse](service-bus-managed-service-identity.md)del bus di servizio di Azure.To learn how to assign permissions to roles, see Authenticate a managed identity with Azure Active Directory to access Azure Service Bus resources .

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Eccezioni](service-bus-resource-manager-exceptions.md)di Azure Resource Manager . Elenca le eccezioni generate durante l'interazione con il bus di servizio di Azure usando Gestione risorse di Azure (tramite modelli o chiamate dirette).
- [Eccezioni di messaggistica](service-bus-messaging-exceptions.md). Fornisce un elenco di eccezioni generate da .NET Framework per il bus di servizio di Azure.It provides a list of exceptions generated by .NET Framework for Azure Service Bus. 

