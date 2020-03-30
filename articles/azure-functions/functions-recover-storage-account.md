---
title: 'Errore di risoluzione dei problemi: Azure Functions Runtime is unreachable'
description: Informazioni su come risolvere il problema di un account di archiviazione non valido.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063782"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Risolvere l'errore: "Il runtime di Funzioni di Azure non è raggiungibile"Troubleshoot error: "Azure Functions Runtime is unreachable"

Questo articolo consente di risolvere la stringa di errore seguente visualizzata nel portale di Azure:This article helps you troubleshoot the following error string that appears in the Azure portal:

> "Errore: il runtime delle funzioni di Azure non è raggiungibile. Fare clic qui per informazioni dettagliate sulla configurazione dello storage."

Questo problema si verifica quando non è possibile avviare il runtime di Funzioni di Azure.This issue occurs when the Azure Functions Runtime can't start. Il motivo più comune del problema è che l'app per le funzioni ha perso l'accesso al proprio account di archiviazione. Per ulteriori informazioni, vedere [Requisiti dell'account di archiviazione](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

Il resto di questo articolo consente di risolvere le seguenti cause di questo errore, tra cui come identificare e risolvere ogni caso.

## <a name="storage-account-was-deleted"></a>L'account di archiviazione è stato eliminato

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se l'account viene eliminato, la funzione non funzionerà.

Iniziare cercando il nome dell'account di archiviazione nelle impostazioni dell'applicazione. In `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` uno o contiene il nome dell'account di archiviazione racchiuso in una stringa di connessione. Per altre informazioni, vedere Informazioni di riferimento sulle impostazioni delle app per Funzioni di Azure.For more information, see [App settings reference for Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Cercare l'account di archiviazione nel portale di Azure per verificare se esiste ancora. Se è stato eliminato, ricreare l'account di archiviazione e sostituire le stringhe di connessione di archiviazione. Il codice della funzione viene perso ed è necessario ridistribuirlo.

## <a name="storage-account-application-settings-were-deleted"></a>Le impostazioni dell'applicazione dell'account di archiviazione sono state eliminate

Nel passaggio precedente, se non è possibile trovare una stringa di connessione dell'account di archiviazione, è probabile che sia stata eliminata o sovrascritta. L'eliminazione delle impostazioni dell'applicazione si verifica più comunemente quando si usano slot di distribuzione o script di Azure Resource Manager per impostare le impostazioni dell'applicazione.

### <a name="required-application-settings"></a>Impostazioni dell'applicazione necessarie

* Obbligatoria:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obbligatorio per le funzioni del piano di consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Per altre informazioni, vedere Informazioni di riferimento sulle impostazioni delle app per Funzioni di Azure.For more information, see [App settings reference for Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Materiale sussidiario

* Non selezionare "impostazione slot" per nessuna di queste impostazioni. Se si scambiano gli slot di distribuzione, l'app per le funzioni si interrompe.
* Non modificare queste impostazioni come parte delle distribuzioni automatiche.
* Queste impostazioni devono essere valide e devono essere indicate al momento della creazione. Una distribuzione automatica che non contiene queste impostazioni genera un'app per le funzioni che non verrà eseguita, anche se le impostazioni vengono aggiunte in un secondo momento.

## <a name="storage-account-credentials-are-invalid"></a>Le credenziali dell'account di archiviazione non sono valideStorage account credentials are invalid

Le stringhe di connessione dell'account di archiviazione illustrate in precedenza devono essere aggiornate se si rigenerano le chiavi di archiviazione. Per altre informazioni sulla gestione delle chiavi di archiviazione, vedere Creare un account di archiviazione di Azure.For more information about storage key management, see [Create an Azure Storage account.](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-is-inaccessible"></a>L'account di archiviazione non è accessibileStorage account is inaccessible

L'app per le funzioni deve essere in grado di accedere all'account di archiviazione. I problemi comuni che bloccano l'accesso di un'app per le funzioni a un account di archiviazione sono:Common issues that block a function app's access to a storage account are:

* L'app per le funzioni viene distribuita nell'ambiente del servizio app senza le regole di rete corrette per consentire il traffico da e verso l'account di archiviazione.

* Il firewall dell'account di archiviazione è abilitato e non configurato per consentire il traffico da e verso le funzioni. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>La quota di esecuzione giornaliera è piena

Se è configurata una quota di esecuzione giornaliera, l'app per le funzioni viene temporaneamente disabilitata, il che causa la disponibilità di molti controlli del portale. 

Per verificare la quota nel [portale di Azure,](https://portal.azure.com)selezionare**Impostazioni dell'app Per** le funzioni delle funzionalità > della **piattaforma**nell'app per le funzioni. Se si supera la **quota di utilizzo giornaliera** impostata, viene visualizzato il seguente messaggio:

  > "L'app per le funzioni ha raggiunto la quota di utilizzo giornaliera ed è stata interrotta fino al successivo periodo di 24 ore."

Per risolvere questo problema, rimuovere o aumentare la quota giornaliera e quindi riavviare l'app. In caso contrario, l'esecuzione dell'app viene bloccata fino al giorno successivo.

## <a name="app-is-behind-a-firewall"></a>L'app è dietro un firewall

Il runtime della funzione potrebbe non essere raggiungibile per uno dei motivi seguenti:Your function runtime might be unreachable for either of the following reasons:

* L'app per le funzioni è ospitata in un ambiente del servizio app con bilanciamento del carico interno ed è [configurata](../app-service/environment/create-ilb-ase.md) per bloccare il traffico Internet in ingresso.

* L'app per le funzioni ha [restrizioni IP in ingresso](functions-networking-options.md#inbound-ip-restrictions) configurate per bloccare l'accesso a Internet. 

Il portale di Azure effettua chiamate direttamente all'app in esecuzione per recuperare l'elenco di funzioni ed effettua chiamate HTTP all'endpoint Kudu. Le impostazioni a livello di piattaforma nella scheda **Funzionalità della piattaforma** sono ancora disponibili.

Per verificare la configurazione dell'ambiente del servizio app:To verify your App Service Environment configuration:
1. Passare al gruppo di sicurezza di rete della subnet in cui risiede l'ambiente del servizio app.
1. Convalidare le regole in ingresso per consentire il traffico proveniente dall'indirizzo IP pubblico del computer a cui si accede all'applicazione. 
   
È anche possibile usare il portale da un computer connesso alla rete virtuale che esegue l'app o a una macchina virtuale in esecuzione nella rete virtuale. 

Per ulteriori informazioni sulla configurazione delle regole in ingresso, vedere la sezione "Gruppi di sicurezza di rete" in [Considerazioni](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)sulla rete per un ambiente del servizio app .

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul monitoraggio delle app per le funzioni:

> [!div class="nextstepaction"]
> [Monitorare Funzioni di Azure](functions-monitoring.md)
