---
title: 'Errore di risoluzione dei problemi: runtime di Funzioni di Azure non è raggiungibile'
description: Informazioni su come risolvere il problema di un account di archiviazione non valido.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063782"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Errore di risoluzione dei problemi: "runtime di Funzioni di Azure non è raggiungibile"

Questo articolo consente di risolvere i problemi relativi alla stringa di errore seguente visualizzata nella portale di Azure:

> "Errore: runtime di Funzioni di Azure non è raggiungibile. Per informazioni dettagliate sulla configurazione dell'archiviazione, fare clic qui.

Questo problema si verifica quando non è possibile avviare il runtime di Funzioni di Azure. La causa più comune del problema è che l'app per le funzioni ha perso l'accesso al proprio account di archiviazione. Per altre informazioni, vedere [requisiti dell'account di archiviazione](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

Nella parte restante di questo articolo vengono illustrate le cause seguenti di questo errore, tra cui come identificare e risolvere ogni caso.

## <a name="storage-account-was-deleted"></a>L'account di archiviazione è stato eliminato

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se tale account viene eliminato, la funzione non funzionerà.

Per iniziare, cercare il nome dell'account di archiviazione nelle impostazioni dell'applicazione. `AzureWebJobsStorage`O `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contiene il nome dell'account di archiviazione incluso in una stringa di connessione. Per altre informazioni, vedere [riferimento alle impostazioni dell'app per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Cercare l'account di archiviazione nell'portale di Azure per verificare se esiste ancora. Se è stato eliminato, ricreare l'account di archiviazione e sostituire le stringhe di connessione di archiviazione. Il codice della funzione viene perso ed è necessario ridistribuirlo.

## <a name="storage-account-application-settings-were-deleted"></a>Le impostazioni dell'applicazione dell'account di archiviazione sono state eliminate

Nel passaggio precedente, se non è possibile trovare una stringa di connessione dell'account di archiviazione, è probabile che sia stata eliminata o sovrascritta. L'eliminazione delle impostazioni dell'applicazione si verifica in genere quando si usano gli slot di distribuzione o Azure Resource Manager script per impostare le impostazioni dell'applicazione.

### <a name="required-application-settings"></a>Impostazioni dell'applicazione necessarie

* Obbligatoria:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obbligatorio per le funzioni del piano a consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Per altre informazioni, vedere [riferimento alle impostazioni dell'app per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Indicazioni

* Non selezionare "impostazione slot" per una di queste impostazioni. Se si scambiano gli slot di distribuzione, l'app per le funzioni si interrompe.
* Non modificare queste impostazioni come parte delle distribuzioni automatiche.
* Queste impostazioni devono essere valide e devono essere indicate al momento della creazione. Una distribuzione automatica che non contiene queste impostazioni genera un'app per le funzioni che non verrà eseguita, anche se le impostazioni vengono aggiunte in un secondo momento.

## <a name="storage-account-credentials-are-invalid"></a>Le credenziali dell'account di archiviazione non sono valide

Se si rigenerano le chiavi di archiviazione, è necessario aggiornare le stringhe di connessione dell'account di archiviazione descritte in precedenza. Per altre informazioni sulla gestione delle chiavi di archiviazione, vedere [creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>L'account di archiviazione non è accessibile

L'app per le funzioni deve essere in grado di accedere all'account di archiviazione. I problemi comuni che bloccano l'accesso di un'app per le funzioni a un account di archiviazione sono:

* L'app per le funzioni viene distribuita nel ambiente del servizio app senza le regole di rete corrette per consentire il traffico da e verso l'account di archiviazione.

* Il firewall dell'account di archiviazione è abilitato e non è configurato per consentire il traffico da e verso le funzioni. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Quota di esecuzione giornaliera piena

Se è configurata una quota di esecuzione giornaliera, l'app per le funzioni è temporaneamente disabilitata, causando la mancata disponibilità di molti dei controlli del portale. 

Per verificare la quota nella [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma**  >  **app per le funzioni impostazioni** nell'app per le funzioni. Se si supera la **quota di utilizzo giornaliera** impostata, viene visualizzato il messaggio seguente:

  > "La app per le funzioni ha raggiunto la quota di utilizzo giornaliero ed è stata arrestata fino all'intervallo di 24 ore successivo."

Per risolvere il problema, rimuovere o aumentare la quota giornaliera, quindi riavviare l'app. In caso contrario, l'esecuzione dell'app verrà bloccata fino al giorno successivo.

## <a name="app-is-behind-a-firewall"></a>L'app è dietro un firewall

Il runtime della funzione potrebbe non essere raggiungibile per uno dei motivi seguenti:

* L'app per le funzioni è ospitata in un [ambiente del servizio app con carico bilanciato internamente](../app-service/environment/create-ilb-ase.md) ed è configurato per bloccare il traffico Internet in ingresso.

* L'app per le funzioni ha [restrizioni IP in ingresso](functions-networking-options.md#inbound-ip-restrictions) configurate per bloccare l'accesso a Internet. 

Il portale di Azure effettua chiamate dirette all'app in esecuzione per recuperare l'elenco di funzioni e effettua chiamate HTTP all'endpoint Kudu. Le impostazioni a livello di piattaforma nella scheda **funzionalità della piattaforma** sono ancora disponibili.

Per verificare la configurazione del ambiente del servizio app:
1. Passare al gruppo di sicurezza di rete (NSG) della subnet in cui risiede il ambiente del servizio app.
1. Convalidare le regole in entrata per consentire il traffico proveniente dall'IP pubblico del computer a cui si sta accedendo all'applicazione. 
   
È anche possibile usare il portale da un computer connesso alla rete virtuale che esegue l'app o a una macchina virtuale in esecuzione nella rete virtuale. 

Per ulteriori informazioni sulla configurazione delle regole in ingresso, vedere la sezione "gruppi di sicurezza di rete" di [considerazioni sulla rete per un ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul monitoraggio delle app per le funzioni:

> [!div class="nextstepaction"]
> [Monitorare Funzioni di Azure](functions-monitoring.md)
