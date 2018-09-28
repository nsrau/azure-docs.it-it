---
title: Livelli elevati di disponibilità e affidabilità - Utilità di pianificazione di Azure
description: Informazioni sui livelli elevati di disponibilità e affidabilità nell'Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: d647de379972bac317a213e2f8925c0ff8c3372c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947925"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Livelli elevati di disponibilità e affidabilità per l'Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà l'Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

L'Utilità di pianificazione di Azure offre livelli elevati di [disponibilità](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) e di affidabilità per i processi. Per altre informazioni, vedere il [contratto di servizio per l'Utilità di pianificazione](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Disponibilità elevata

L'Utilità di pianificazione di Azure offre disponibilità elevata e usa sia funzionalità di distribuzione del servizio con ridondanza geografica che di replica geografica internazionale dei processi.

### <a name="geo-redundant-service-deployment"></a>Distribuzione del servizio con ridondanza geografica

L'Utilità di pianificazione di Azure è disponibile nel portale di Azure per quasi [tutte le aree geografiche attualmente supportate da Azure](https://azure.microsoft.com/global-infrastructure/regions/#services). Pertanto, se un data center di Azure in un'area servita non è più disponibile, è comunque possibile usare l'Utilità di pianificazione di Azure, in quanto le funzionalità di failover del servizio renderanno disponibile l'Utilità di pianificazione da un altro data center.

### <a name="geo-regional-job-replication"></a>Replica geografica internazionale dei processi

I processi nell'Utilità di pianificazione di Azure vengono replicati tra le aree di Azure. Se si verifica un'interruzione di servizio in un'area, quindi, l'Utilità di pianificazione di Azure attiva il failover e assicura che il processo venga eseguito da un altro data center nell'area geografica associata.

Ad esempio, se si crea un processo nell'area Stati Uniti centro-meridionali, l'Utilità di pianificazione di Azure replica automaticamente tale processo nell'area Stati Uniti centro-settentrionali. Se si verifica un problema nell'area Stati Uniti centro-meridionali, l'Utilità di pianificazione di Azure esegue il processo nell'area Stati Uniti centro-settentrionali. 

![Replica geografica internazionale dei processi](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

L'Utilità di pianificazione di Azure garantisce anche che i dati rimangano nella stessa area geografica, ma in un'area più ampia, in caso di un problema in Azure. Non è quindi necessario duplicare i processi quando serve semplicemente una disponibilità elevata. L'Utilità di pianificazione di Azure offre automaticamente la disponibilità elevata per i processi.

## <a name="reliability"></a>Affidabilità

L'Utilità di pianificazione di Azure garantisce la propria disponibilità elevata, ma adotta un approccio diverso per i processi creati dall'utente. Ad esempio, si supponga che il processo richiami un endpoint HTTP che non è disponibile. L'Utilità di pianificazione di Azure tenta comunque di eseguire correttamente il processo, offrendo modi alternativi per la gestione degli errori: 

* Configurare criteri di ripetizione.
* Configurare endpoint alternativi.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Criteri di ripetizione

L'Utilità di pianificazione di Azure consente di configurare criteri di ripetizione. Se un processo non riesce, per impostazione predefinita l'Utilità di pianificazione tenta di eseguire nuovamente il processo quattro volte, a intervalli di 30 secondi. È possibile impostare questi criteri di ripetizione in modo che siano più aggressivi (ad esempio, dieci volte a intervalli di 30 secondi) o meno aggressivi (ad esempio, due volte a distanza di un giorno l’una dall'altra).

Ad esempio, si supponga di creare un processo settimanale che chiama un endpoint HTTP. Se l'endpoint HTTP diventa non disponibile per alcune ore durante l'esecuzione del processo, si potrebbe non voler attendere un'ulteriore settimana per eseguire di nuovo il processo, dato che i criteri di ripetizione predefiniti non funzionano in questo caso. Potrebbe essere quindi utile modificare i criteri di ripetizione standard in modo che i tentativi vengano ripetuti ogni tre ore, ad esempio, anziché ogni 30 secondi. 

Per informazioni su come configurare i criteri di ripetizione, vedere [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Endpoint alternativi

Se il processo dell'Utilità di pianificazione di Azure chiama un endpoint non raggiungibile, anche dopo l'applicazione dei criteri di ripetizione, l'Utilità di pianificazione esegue il fallback su un endpoint alternativo in grado di gestire tali errori. Pertanto, se si configura questo endpoint, l'Utilità di pianificazione chiama tale endpoint, rendendo i processi a disponibilità elevata quando si verificano errori.

Ad esempio, questo diagramma mostra il modo in cui l'Utilità di pianificazione segue i criteri di ripetizione quando si chiama un servizio Web di New York. Se i tentativi hanno esito negativo, l'Utilità di pianificazione cerca un endpoint alternativo. Se l'endpoint esiste, l'Utilità di pianificazione inizia a inviare le richieste all'endpoint alternativo. Si noti che gli stessi criteri di ripetizione si applicano sia all'azione originale che all'azione alternativa.

![Comportamento dell'Utilità di pianificazione con criteri di ripetizione ed endpoint alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Il tipo di azione per l'azione alternativa può essere diverso dall'azione originale. Ad esempio, anche se l'azione originale chiama un endpoint HTTP, l'azione alternativa potrebbe registrare gli errori tramite una coda di archiviazione, una coda del bus di servizio o un'azione di argomento del bus di servizio.

Per informazioni su come configurare un endpoint alternativo, vedere [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'Utilità di pianificazione di Azure](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità](scheduler-concepts-terms.md)
* [Creare pianificazioni complesse e ricorrenze avanzate](scheduler-advanced-complexity.md)
* [Limiti, valori predefiniti e codici di errore](scheduler-limits-defaults-errors.md)
