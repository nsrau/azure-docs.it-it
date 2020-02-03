---
title: Come risolvere il problema del runtime di Funzioni di Azure che non è raggiungibile.
description: Informazioni su come risolvere il problema di un account di archiviazione non valido.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963887"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Come risolvere il problema del "runtime di Funzioni di Azure non raggiungibile"

Questo articolo ha lo scopo di risolvere il messaggio di errore "Runtime di funzioni non raggiungibile" quando viene visualizzato nella portale di Azure. Quando si verifica questo errore, nel portale viene visualizzata la stringa di errore seguente.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Questo errore si verifica quando non è possibile avviare il runtime di Funzioni di Azure. Il motivo più comune per cui si verifica questo errore è che l'app per le funzioni perde l'accesso al relativo account di archiviazione. Per altre informazioni, vedere [requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

Nella parte restante di questo articolo vengono illustrate le cause seguenti di questo errore, tra cui come identificare e risolvere ogni caso.

+ [Account di archiviazione eliminato](#storage-account-deleted)
+ [Impostazioni applicazione dell'account di archiviazione eliminate](#storage-account-application-settings-deleted)
+ [Credenziali dell'account di archiviazione non valide](#storage-account-credentials-invalid)
+ [Account di archiviazione inaccessibile](#storage-account-inaccessible)
+ [Quota di esecuzione giornaliera superata](#daily-execution-quota-full)
+ [L'app è dietro un firewall](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Account di archiviazione eliminato

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se l'account viene eliminato, la funzione non funzionerà.

### <a name="how-to-find-your-storage-account"></a>Come trovare l'account di archiviazione

Iniziare ricercando il nome dell'account di archiviazione nelle impostazioni dell'applicazione. `AzureWebJobsStorage` o `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterranno il nome dell'account di archiviazione all'interno di una stringa di connessione. Per altre informazioni dettagliate, vedere [qui il riferimento all'impostazione dell'applicazione](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Ricercare l'account di archiviazione nel portale di Azure per verificare se esiste ancora. Se è stato eliminato, è necessario ricreare un account di archiviazione e sostituire le stringhe di connessione di archiviazione. Il codice della funzione è andato perso e sarà necessario ridistribuirlo nuovamente.

## <a name="storage-account-application-settings-deleted"></a>Impostazioni di applicazione dell'account di archiviazione eliminate

Nel passaggio precedente, se non è presente una stringa di connessione dell'account di archiviazione, è probabile che sia stata eliminata o sovrascritta. L'eliminazione delle impostazioni dell'app viene eseguita più di frequente quando si usano gli slot di distribuzione o gli script di Azure Resource Manager per configurare le impostazioni dell'applicazione.

### <a name="required-application-settings"></a>Impostazioni dell'applicazione necessarie

* Obbligatorio
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obbligatoria per funzioni di Piano a consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Per informazioni sulle impostazioni dell'applicazione, vedere qui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Guida

* Non selezionare "impostazione slot" per una di queste impostazioni. Quando si scambiano gli slot di distribuzione, l'app per le funzioni si interrompe.
* Non modificare queste impostazioni come parte delle distribuzioni automatiche.
* Queste impostazioni devono essere valide e devono essere indicate al momento della creazione. Una distribuzione automatica che non contiene queste impostazioni genera un'app per le funzioni che non verrà eseguita, anche se le impostazioni vengono aggiunte in un secondo momento.

## <a name="storage-account-credentials-invalid"></a>Credenziali dell'account di archiviazione non valide

Se si rigenerano le chiavi di archiviazione, è necessario aggiornare le stringhe di connessione dell'account di archiviazione precedente. [Per altre informazioni sulla gestione delle chiavi di archiviazione, vedere qui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Account di archiviazione inaccessibile

L'app per le funzioni deve essere in grado di accedere all'account di archiviazione. I problemi comuni che bloccano un accesso delle funzioni a un account di archiviazione sono:

+ app per le funzioni distribuite in ambienti del servizio app (ASE) senza le regole di rete corrette per consentire il traffico da e verso l'account di archiviazione.

+ Il firewall dell'account di archiviazione è abilitato e non è configurato per consentire il traffico da e verso le funzioni. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Quota di esecuzione giornaliera completa

Se è configurata una quota di esecuzione giornaliera, l'app per le funzioni è temporaneamente disabilitata, causando la mancata disponibilità di molti dei controlli del portale. 

+ Per verificare nella [portale di Azure](https://portal.azure.com), aprire **funzionalità della piattaforma** > **impostazioni app per le funzioni** nell'app per le funzioni. Quando si supera la **quota di utilizzo giornaliera** impostata, viene visualizzato il messaggio seguente:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Per risolvere il problema, rimuovere o aumentare la quota giornaliera e riavviare l'app. In caso contrario, l'esecuzione dell'app verrà bloccata fino al giorno successivo.

## <a name="app-is-behind-a-firewall"></a>L'app è dietro un firewall

Il runtime della funzione non sarà raggiungibile se l'app per le funzioni è ospitata in un [ambiente del servizio app con carico bilanciato internamente](../app-service/environment/create-ilb-ase.md) ed è configurata per bloccare il traffico Internet in ingresso oppure è configurata una [restrizione IP in ingresso](functions-networking-options.md#inbound-ip-restrictions) per bloccare l'accesso a Internet. Il portale di Azure effettua chiamate dirette all'app in esecuzione per recuperare l'elenco di funzioni ed effettua chiamate HTTP anche all'endpoint KUDU. Le impostazioni a livello di piattaforma nella scheda `Platform Features` saranno ancora disponibili.

Per verificare la configurazione dell'ambiente del servizio app, passare alla NSG della subnet in cui si trova l'ambiente del servizio app e convalidare le regole in entrata per consentire il traffico proveniente dall'IP pubblico del computer a cui si sta accedendo. È anche possibile usare il portale da un computer connesso alla rete virtuale che esegue l'app o una macchina virtuale in esecuzione nella rete virtuale. [Per altre informazioni sulla configurazione delle regole in ingresso, vedere qui](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Fasi successive

Informazioni sul monitoraggio delle app per le funzioni:

> [!div class="nextstepaction"]
> [Monitorare funzioni di Azure](functions-monitoring.md)
