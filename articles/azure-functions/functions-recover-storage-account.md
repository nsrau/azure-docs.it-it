---
title: Come risolvere il problema del runtime di Funzioni di Azure che non è raggiungibile.
description: Informazioni su come risolvere il problema di un account di archiviazione non valido.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226769"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Come risolvere il problema del "runtime di Funzioni di Azure non raggiungibile"


## <a name="error-text"></a>Testo dell'errore
Questo documento è destinato a risolvere l'errore seguente quando visualizzato nel portale di Funzioni di Azure.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>summary
Questo problema si verifica quando non è possibile avviare il runtime di Funzioni di Azure. Il motivo più comune per cui si verifica questo errore è che l'app per le funzioni perde l'accesso al relativo account di archiviazione. [Altre informazioni sui requisiti dell'account di archiviazione sono reperibili qui](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Risoluzione dei problemi
Verranno esaminati i quattro casi di errore più comuni, come identificare e come risolvere ogni caso.

1. Account di archiviazione eliminato
1. Impostazioni di applicazione dell'account di archiviazione eliminate
1. Credenziali dell'account di archiviazione non valide
1. Account di archiviazione inaccessibile
1. Quota di esecuzione giornaliera completa

## <a name="storage-account-deleted"></a>Account di archiviazione eliminato

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se l'account viene eliminato, la funzione non funzionerà.

### <a name="how-to-find-your-storage-account"></a>Come trovare l'account di archiviazione

Iniziare ricercando il nome dell'account di archiviazione nelle impostazioni dell'applicazione. `AzureWebJobsStorage` o `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterranno il nome dell'account di archiviazione all'interno di una stringa di connessione. Leggere informazioni più specifiche nel [riferimento all'impostazione dell'applicazione qui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Ricercare l'account di archiviazione nel portale di Azure per verificare se esiste ancora. Se è stato eliminato, è necessario ricreare un account di archiviazione e sostituire le stringhe di connessione di archiviazione. Il codice della funzione è andato perso e sarà necessario ridistribuirlo nuovamente.

## <a name="storage-account-application-settings-deleted"></a>Impostazioni di applicazione dell'account di archiviazione eliminate

Nel passaggio precedente, se non si disponeva di una stringa di connessione dell'account di archiviazione, è probabile che sia stata eliminata o sovrascritta. L'eliminazione delle impostazioni dell'app viene eseguita più di frequente quando si usano gli slot di distribuzione o gli script di Azure Resource Manager per configurare le impostazioni dell'applicazione.

### <a name="required-application-settings"></a>Impostazioni dell'applicazione necessarie

* obbligatori
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obbligatoria per funzioni di Piano a consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Informazioni su queste impostazioni dell'applicazione qui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Indicazioni

* Non selezionare "impostazione slot" per qualsiasi di queste impostazioni. Quando si scambiano gli slot di distribuzione la funzione verrà interrotta.
* Non modificare queste impostazioni come parte delle distribuzioni automatiche.
* Queste impostazioni devono essere valide e devono essere indicate al momento della creazione. Una distribuzione automatizzata che non contiene queste impostazioni comporterà un'app non funzionante, anche se le impostazioni vengono aggiunte al termine dell'attività.

## <a name="storage-account-credentials-invalid"></a>Credenziali dell'account di archiviazione non valide

Se si rigenerano le chiavi di archiviazione, è necessario aggiornare le stringhe di connessione dell'account di archiviazione precedente. [Altre informazioni sulla gestione delle chiavi archiviazione qui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Account di archiviazione inaccessibile

L'app per le funzioni deve essere in grado di accedere all'account di archiviazione. I problemi comuni che bloccano un accesso delle funzioni a un account di archiviazione sono:

* L'app per le funzioni distribuita agli ambienti del servizio app senza le regole di rete corrette per consentire il traffico da e verso l'account di archiviazione
* Il firewall dell'account di archiviazione è abilitato e non è configurato per consentire il traffico da e verso le funzioni. [Altre informazioni sulla configurazione del firewall dell'account di archiviazione sono reperibili qui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Quota di esecuzione giornaliera completa

Se si dispone di una Quota di esecuzione giornaliera configurata, verrà disabilitata temporaneamente l'app per le funzioni e molti dei controlli del portale non saranno più disponibili. 

* Per verificare, selezionare Apri Funzionalità della piattaforma > Impostazioni dell'app per le funzioni nel portale. Nel caso di superamento di quota, viene inviato il messaggio seguente
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Rimuovere la quota e riavviare l'app per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'app per le funzioni è nuovamente operativa, esaminare i riferimenti per sviluppatori e i modelli di avvio rapido per iniziare a usare nuovamente l'app.

* [Creare la prima funzione di Azure](functions-create-first-azure-function.md)  
  Informazioni su come iniziare immediatamente a creare la prima funzione tramite Avvio rapido di Funzioni di Azure. 
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)  
  Include informazioni più tecniche sul runtime di Funzioni di Azure, nonché informazioni di riferimento per la codifica di funzioni e la definizione di trigger e associazioni.
* [Test di Funzioni di Azure](functions-test-a-function.md)  
  Descrive diversi strumenti e tecniche per il test delle funzioni.
* [Come aumentare le prestazioni di Funzioni di Azure](functions-scale.md)  
  Presenta i piani di servizio disponibili con Funzioni di Azure, tra cui il piano di hosting A consumo, e spiega come scegliere quello più appropriato. 
* [Informazioni sul servizio app di Azure](../app-service/overview.md)  
  Funzioni di Azure sfrutta il servizio app di Azure per le funzionalità di base, ad esempio distribuzioni, variabili di ambiente e diagnostica. 
