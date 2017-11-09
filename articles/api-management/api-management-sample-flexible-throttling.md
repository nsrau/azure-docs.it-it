---
title: Limitazione avanzata delle richieste con Gestione API di Azure
description: Informazioni su come creare e applicare criteri flessibili di limitazione della frequenza e della quota con Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 356f98aec072a1295915ae0701a3e3cd793aba07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitazione avanzata delle richieste con Gestione API di Azure
La possibilità di limitare le richieste in ingresso è uno dei ruoli fondamentali di Gestione API di Azure. Tramite il controllo della frequenza delle richieste o del totale delle richieste o dei dati trasferiti, Gestione API consente ai provider di API di proteggere le API da abusi e di aggiungere valore a diversi livelli di prodotto API.

## <a name="product-based-throttling"></a>Limitazione basata sul prodotto
Finora le funzionalità di limitazione della frequenza avevano come ambito una sottoscrizione specifica a un prodotto (essenzialmente una chiave), definita nel portale di pubblicazione di Gestione API. Tali funzionalità sono utili per consentire al provider di API di applicare limiti agli sviluppatori che si sono registrati per l'uso dell'API, ma non consente di limitare ad esempio i singoli utenti finali dell'API. Il singolo utente dell'applicazione dello sviluppatore può usare l'intera quota e quindi impedire ad altri clienti dello sviluppatore di usare l'applicazione. Inoltre, alcuni clienti possono generare un numero elevato di richieste, limitando l'accesso agli utenti occasionali.

## <a name="custom-key-based-throttling"></a>Limitazione basata su chiave personalizzata
I nuovi criteri [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) offrono una soluzione molto più flessibile per il controllo del traffico. Questi nuovi criteri consentono di definire le espressioni per l'identificazione delle chiavi che verranno usate per tenere traccia dell'utilizzo del traffico. Il modo più semplice per spiegarne il funzionamento è illustrare un esempio. 

## <a name="ip-address-throttling"></a>Limitazione dell'indirizzo IP
I seguenti criteri limitano l'indirizzo IP di un singolo client a 10 chiamate al minuto, con un totale di 1.000.000 chiamate e 10.000 KB di larghezza di banda al mese. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se tutti i client su Internet usassero un indirizzo IP univoco, sarebbe un metodo efficace per limitare l'utilizzo per utente. È tuttavia molto probabile che più utenti condividano un singolo indirizzo IP pubblico, in quanto accedono a Internet tramite un dispositivo NAT. Nonostante ciò, per le API che consentono l'accesso non autenticato, `IpAddress` può essere la soluzione migliore.

## <a name="user-identity-throttling"></a>Limitazione dell'identità utente
Se un utente finale viene autenticato, può essere generata una chiave per la limitazione delle richieste in base alle informazioni che identificano in modo univoco l'utente.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

In questo esempio si estrae l'intestazione dell'autorizzazione, la si converte in un oggetto `JWT` e si usa l'oggetto del token per identificare l'utente e usarlo come chiave per la limitazione della frequenza. Se l'identità dell'utente è archiviata nell'oggetto `JWT` come una delle altre attestazioni, è possibile sostituirla con quel valore.

## <a name="combined-policies"></a>Criteri combinati
Sebbene i nuovi criteri di limitazione garantiscano maggiore controllo rispetto ai criteri di limitazione esistenti, la combinazione delle due funzionalità presenta comunque dei vantaggi. La limitazione per chiave di sottoscrizione del prodotto ([Limit call rate by subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) (Limitare la frequenza delle chiamate per sottoscrizione) e [Set usage quota by subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) (Impostare la quota di utilizzo per sottoscrizione)) è un ottimo metodo per monetizzare un'API effettuando gli addebiti in base ai livelli di utilizzo. Il controllo con granularità maggiore per impostare la limitazione per utente è complementare e impedisce che il comportamento di un utente comprometta l'esperienza di un altro utente. 

## <a name="client-driven-throttling"></a>Limitazione basata su client
Quando la chiave per la limitazione viene definita mediante un' [espressione di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx), è il provider di API a scegliere l'ambito della limitazione. Uno sviluppatore può tuttavia voler controllare la modalità di limitazione della frequenza per i propri clienti. Il provider di API può abilitare questa opzione introducendo un'intestazione personalizzata per consentire all'applicazione client dello sviluppatore di comunicare la chiave all'API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

In questo modo l'applicazione client dello sviluppatore è in grado di scegliere la modalità di creazione della chiave di limitazione della frequenza. Con un po' di astuzia, uno sviluppatore di client può creare i propri livelli di frequenza allocando set di chiavi agli utenti e ruotando l'utilizzo delle chiavi.

## <a name="summary"></a>Summary
Gestione API di Azure offre funzionalità di limitazione della frequenza e della quota per proteggere e aggiungere valore al proprio servizio API. I nuovi criteri di limitazione con regole personalizzate di definizione dell'ambito consentono di controllare con granularità maggiore tali criteri per consentire ai clienti di creare applicazioni migliori. Gli esempi in questo articolo illustrano l'uso di questi nuovi criteri tramite la produzione di chiavi per la limitazione della frequenza con gli indirizzi IP del client, l'identità dell'utente e valori generati dal client. È tuttavia possibile usare molte altre parti del messaggio, ad esempio l'agente utente, frammenti del percorso dell'URL e la dimensione del messaggio.

## <a name="next-steps"></a>Passaggi successivi
È possibile inserire commenti e suggerimenti per questo argomento nel thread Disqus. Può essere utile individuare altri potenziali valori di chiave che sono utili negli scenari in uso.

## <a name="watch-a-video-overview-of-these-policies"></a>Video contenente una panoramica di questi criteri
Per altre informazioni sui criteri [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) descritti in questo articolo, guardare il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

