---
title: Ritiro da parte di Monitoraggio di Azure di API di modelli di distribuzione classici per metriche e scalabilità automatica
description: Ritiro delle API classiche di metrica e scalabilità automatica, chiamate anche Azure Service Management (ASM) o modello di distribuzione RDFE
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 11/19/2018
ms.openlocfilehash: 7a93419ee84e6a50ce07cefa941a8df9f85b7b6e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552205"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Ritiro da parte di Monitoraggio di Azure di API di modelli di distribuzione classici per metriche e scalabilità automatica

Monitoraggio di Azure (in precedenza Azure Insights quando fu rilasciato) offre attualmente la possibilità di creare e gestire le impostazioni di scalabilità automatica e di usare metriche da macchine virtuali classiche e servizi cloud classici. Il set originale di API di distribuzione classica basate su modello verrà **ritirato dopo il 30 giugno 2019** in tutti i cloud pubblici e privati di Azure in tutte le aree.   

Le stesse operazioni sono state supportate tramite un set di API basate su Azure Resource Manager per oltre un anno. Il portale di Azure Usa le nuove API REST per la scalabilità automatica e le metriche. Sono disponibili anche un nuovo SDK, PowerShell, e l’interfaccia della riga di comando basata su queste API di Resource Manager. I nostri servizi partner per il monitoraggio utilizzano le nuove API REST di Gestione risorse in Monitoraggio di Azure.  

## <a name="who-is-not-affected"></a>Chi non è interessato

Se si sta gestendo la scalabilità automatica tramite il portale di Azure, il [nuovo SDK di Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, l’interfaccia della riga di comando o i modelli di Gestione risorse, non è necessaria alcuna azione.  

Se si utilizzano le metriche tramite il portale di Azure o tramite vari [servizi dei partner di monitoraggio](../../azure-monitor/platform/partners.md), non è necessaria alcuna azione. Microsoft sta lavorando con partner di monitoraggio per eseguire la migrazione alle nuove API.

## <a name="who-is-affected"></a>Chi è interessato

Questo articolo riguarda gli utenti che utilizzano i seguenti componenti:

- **SDK di Azure Insights classico**: se si usa il [SDK di Azure Insights classico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), iniziare a usare il nuovo SDK di Monitoraggio di Azure per [.NET](https://github.com/azure/azure-libraries-for-net#download) o [Java](https://github.com/azure/azure-libraries-for-java#download). Scaricare il [pacchetto NuGet di SDK di Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Scalabilità automatica classica**: se si chiamano le [API di impostazione della scalabilità automatica classiche](https://msdn.microsoft.com/library/azure/mt348562.aspx) dagli strumenti personalizzati o usando il [SDK classico di Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), iniziare a usare l’[API REST di Monitoraggio di Azure per Gestione risorse](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Metriche classiche**: se si utilizzano le metriche con le [API REST classiche](https://msdn.microsoft.com/library/azure/dn510374.aspx) o il [SDK classico di Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) dagli strumenti personalizzati, iniziare a usare l’[API REST di Monitoraggio di Azure per Gestione risorse](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Se non si è certi che il codice o gli strumenti personalizzati chiamino le API classiche, esaminare quanto segue:

- Controllare l'URI a cui fa riferimento il codice o lo strumento. Le API classiche usano l'URI https://management.core.windows.net. Utilizzare l'URI più recente per le API di Resource Manager che inizia con https://management.azure.com/.

- Confrontare il nome dell'assembly nel computer. L'assembly classico precedente si trova https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Se si usa l'autenticazione con certificato per accedere alle API di metriche o scalabilità automatica, si usano un endpoint e una libreria classici. Le nuove API di Gestione risorse richiedono l'autenticazione ad Azure Active Directory tramite un'entità servizio o l'entità utente.

- Se si usano le chiamate a cui fa riferimento la documentazione in uno dei collegamenti riportati di seguito, si stanno utilizzando le API classiche precedenti.

  - [Libreria di classi Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [.NET di monitoraggio (classico)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interfaccia IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Perché è necessario

Tutte le funzionalità di scalabilità automatica e di metriche esistente continueranno a funzionare con le nuove API.  

La migrazione alle nuove API consente di usare funzionalità basate su Resource Manager, ad esempio il supporto per il controllo degli accessi in base al ruolo (RBAC) coerente in tutti i servizi di monitoraggio. È anche possibile ottenere funzionalità aggiuntive per le metriche: 

- supporto per le dimensioni
- granularità coerente delle metriche di 1 minuto in tutti i servizi 
- migliore esecuzione di query
- conservazione dei dati più elevata (93 giorni di metriche rispetto a 30 giorni) 

In generale, come per tutti gli altri servizi di Azure, le API di Monitoraggio di Azure basate su Gestione risorse garantiscono prestazioni, scalabilità e affidabilità migliori. 

## <a name="what-happens-if-you-do-not-migrate"></a>Cosa accade se non si effettua la migrazione

### <a name="before-retirement"></a>Prima del ritiro

Non ci sarà un impatto diretto sui servizi di Azure o sui carichi di lavoro.  

### <a name="after-retirement"></a>Dopo il ritiro

Tutte le chiamate alle API classiche disponibili in precedenza avranno esito negativo e si riceveranno messaggi di errore simili al seguente:

Per la scalabilità automatica: *questa API è stata deprecata. Usare i modelli portale di Azure, Azure monitor SDK, PowerShell, CLI o Gestione risorse per gestire le impostazioni di scalabilità*automatica.  

Per le metriche: *questa API è stata deprecata. Usare la portale di Azure, Azure monitor SDK, PowerShell e l'interfaccia della riga di comando per eseguire query per le metriche*.

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

È stata inviata una notifica di ritiro agli indirizzi e-mail dei seguenti ruoli di account: 

- Amministratori di account e del servizio
- Coamministratori  

Per eventuali domande, contattarci a MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Riferimenti

- [Nuove API REST per Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/) 
- [Nuovo SDK per Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
