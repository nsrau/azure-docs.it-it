---
title: Funzionalità di richiesta dei dati dei clienti in Azure Time Series Insights
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
ms.openlocfilehash: d99febdf88fdbf66c45c2ba9b0ab274db1fde185
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34369023"
---
# <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti

Azure Time Series Insights è un servizio cloud gestito con componenti di archiviazione, analisi e visualizzazione che rendono facile inserire, archiviare, esplorare e analizzare miliardi di eventi simultaneamente.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Per visualizzare, esportare ed eliminare i dati personali che possono essere oggetto di una richiesta da parte dei soggetti interessati, l'amministratore del tenant di Azure Time Series Insights può usare il portale di Azure o le API REST. L'uso del portale di Azure per gestire le richieste dei dati da parte dei soggetti interessati costituisce un metodo più semplice per eseguire tali operazioni, preferito dalla maggior parte degli utenti.

## <a name="identifying-customer-data"></a>Identificazione dei dati dei clienti

Azure Time Series Insights considera i dati personali come dati associati agli amministratori e agli utenti di Time Series Insights. Time Series Insights archivia l'ID oggetto di Azure Active Directory degli utenti con accesso all'ambiente. Nel portale di Azure vengono visualizzati gli indirizzi di posta elettronica degli utenti, ma tali indirizzi di posta elettronica non vengono archiviati in Time Series Insights, vengono cercati in modo dinamico mediante l'ID oggetto di Azure Active Directory in Azure Active Directory.

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti

L'amministratore del tenant può eliminare i dati dei clienti tramite il portale di Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Tuttavia, prima di eliminare i dati dei clienti tramite il portale, è necessario rimuovere i criteri di accesso dell'utente dall'ambiente di Time Series Insights nel portale di Azure. Per altre informazioni, vedere [Concedere l'accesso ai dati in un ambiente Time Series Insights con il portale di Azure](time-series-insights-data-access.md).

È anche possibile eseguire le operazioni di eliminazione dei criteri di accesso usando l'API REST. Per altre informazioni, vedere [Access Policies - Delete](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete) (Criteri di accesso - Delete).

Time Series Insights si integra con il pannello Criteri nel portale di Azure. Sia Time Series Insights che il pannello Criteri consentono di visualizzare, esportare ed eliminare i dati utente archiviati all'interno del servizio. Le azioni di eliminazione eseguite nel pannello Criteri del portale di Azure fanno sì che i dati utente vengano eliminati anche in Time Series Insights. Ad esempio, se un utente ha una query personale salvata, tale query viene eliminata definitivamente dallo strumento di esplorazione di Time Series Insights. Se l'utente ha una query condivisa salvata, la query persiste, ma le informazioni sull'utente vengono eliminate definitivamente. La nota seguente contiene le istruzioni su come eseguire queste attività.

## <a name="exporting-customer-data"></a>Esportazione di dati dei clienti

Analogamente all'eliminazione dei dati, l'amministratore del tenant può visualizzare ed esportare i dati archiviati in Time Series Insights dal pannello Criteri nel portale di Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se si è un amministratore di tenant, è possibile visualizzare i criteri di accesso ai dati nell'ambiente di Time Series Insights nel portale di Azure. Per altre informazioni, vedere [Concedere l'accesso ai dati in un ambiente Time Series Insights con il portale di Azure](time-series-insights-data-access.md).

È anche possibile eseguire le operazioni di esportazione dei criteri di accesso mediante l'operazione di elenco in base ad ambiente specificata nell'API REST. Per altre informazioni, vedere [Access Policies - List By Environment](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment) (Criteri di accesso - List By Environment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Per eliminare i dati archiviati in Time Series Insights

I dati personali possono essere aggiunti all'archiviazione di Time Series Insights, andando a costituire uno scenario diverso rispetto a quello dei dati utente o dell'amministratore. Se si considerano i dati archiviati in Time Series Insights come dati personali, è possibile esportarli ed eliminarli mediante la procedura seguente:

**Visualizzare ed esportare i dati**

Per visualizzare ed esportare i dati archiviati in Time Series Insights, è necessario prima cercarli. Per visualizzare ed esportare i dati è possibile usare lo strumento di esplorazione di Time Series Insights o le API di query di Time Series Insight. Per visualizzare ed esportare i dati mediante lo strumento di esplorazione di Time Series Insights, eseguire prima una ricerca per trovare i dati utente in questione. Dopo aver eseguito la ricerca, fare clic con il pulsante destro del mouse sul grafico e selezionare **Esplora eventi**. Viene visualizzata la griglia degli eventi e vengono presentate le opzioni per esportare i dati come file con estensione csv e json.

Per altre informazioni, vedere [Strumento di esplorazione di Azure Time Series Insights](time-series-insights-explorer.md).

**Eliminare i dati**

Attualmente, Time Series Insights non supporta l'eliminazione granulare dei dati. Tuttavia, Time Series Insights offre la possibilità di rimuovere i dati dei clienti archiviati configurando i criteri di conservazione. È possibile regolare il periodo di conservazione dell'intero ambiente di Time Series Insights su qualsiasi numero di giorni per supportare i requisiti di eliminazione.

Per altre informazioni, vedere [Configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).