---
title: Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole di memorizzazione nella cache | Microsoft Docs
description: È possibile usare le regole di memorizzazione nella cache della rete CDN o modificare il comportamento predefinito di scadenza della cache a livello globale e con condizioni, ad esempio un percorso URL e le estensioni dei file.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: 3a94b8252feb7c5c345d678579c477fce02d6e03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679213"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole di memorizzazione nella cache

> [!NOTE] 
> Le regole di memorizzazione nella cache sono disponibili solo per i profili di **rete CDN Standard di Azure con tecnologia Verizon** e di **rete CDN Standard di Azure con tecnologia Akamai**. Per i profili di **rete CDN Premium di Azure con tecnologia Verizon** è necessario usare il [motore regole della rete CDN di Azure](cdn-rules-engine.md) accessibile dal portale tramite il pulsante **Gestisci** per funzionalità analoghe.
 
La rete per la distribuzione di contenuti (CDN) di Azure offre due modi per controllare la modalità di memorizzazione nella cache dei file: 

- Regole di memorizzazione nella cache: Questo articolo descrive come è possibile usare una rete di distribuzione di contenuti (CDN) le regole di memorizzazione nella cache per impostare o modificare il comportamento predefinito di scadenza della cache sia a livello globale e con condizioni personalizzate, ad esempio un'estensione di file e percorso URL. La rete CDN di Azure offre due tipi di regole di memorizzazione nella cache:

   - Regole di memorizzazione nella cache globali: È possibile impostare una regola di memorizzazione nella cache globale per ogni endpoint nel profilo, che influisce su tutte le richieste all'endpoint. La regola di memorizzazione nella cache globale esegue l'override di eventuali intestazioni di direttive della cache HTTP, se impostate.

   - Memorizzazione nella cache regole personalizzate: È possibile impostare uno o più regole memorizzazione nella cache personalizzate per ogni endpoint nel profilo. Le regole di memorizzazione nella cache personalizzate corrispondono a percorsi ed estensioni dei file specifici, vengono elaborate in ordine e sostituiscono la regola di memorizzazione nella cache globale, se impostata. 

- Memorizzazione nella cache di stringa di query: È possibile modificare il modo in cui la rete CDN di Azure considera la memorizzazione nella cache per le richieste con stringhe di query. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string.md). Se il file non è memorizzabile nella cache, l'impostazione di memorizzazione nella cache delle stringhe di query non ha alcun effetto, in base alle regole di memorizzazione e ai comportamenti predefiniti della rete CDN.

Per informazioni sul comportamento predefinito e sulle intestazioni delle direttive di memorizzazione nella cache, vedere [Funzionamento della memorizzazione nella cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Accesso alle regole di memorizzazione nella cache della rete CDN di Azure

1. Aprire il portale di Azure, selezionare un profilo della rete CDN e selezionare un endpoint.

2. In Impostazioni nel riquadro sinistro selezionare **Regole di memorizzazione nella cache**.

   ![Pulsante Regole di memorizzazione nella cache della rete CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Viene visualizzata la pagina **Regole di memorizzazione nella cache**.

   ![Pagina delle regole di memorizzazione nella cache della rete CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Impostazioni del comportamento di memorizzazione nella cache
Per le regole di memorizzazione nella cache globali e personalizzate, è possibile specificare le impostazioni di **Comportamento di memorizzazione nella cache** seguenti:

- **Ignora cache**: Non memorizzare nella cache e le intestazioni delle direttive della cache fornite in origine e ignorate.

- **Eseguire l'override**: Ignora durata della cache fornite in origine e; Usare invece la durata della cache fornita. Non sostituirà cache-control: no-cache.

- **Imposta se mancante**: Rispetta le intestazioni di direttive della cache fornite in origine e, se presenti; in caso contrario, usare la durata della cache fornita.

![Regole di memorizzazione nella cache globali](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Regole di memorizzazione nella cache personalizzate](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Durata di scadenza della cache
Per le regole di memorizzazione nella cache globali e personalizzate è possibile specificare la durata di scadenza della cache in giorni, ore, minuti e secondi:

- Per le impostazioni **Override** e **Imposta se mancante** di **Comportamento di memorizzazione nella cache**, le durate della cache valide sono comprese tra 0 secondi e 366 giorni. Per un valore di 0 secondi, la rete CDN memorizza nella cache il contenuto, ma deve ripetere la convalida di ogni richiesta con il server di origine.

- Per l'impostazione **Ignora cache** la durata della cache viene impostata automaticamente su 0 secondi e non può essere modificata.

## <a name="custom-caching-rules-match-conditions"></a>Condizioni di corrispondenza delle regole di memorizzazione nella cache personalizzate

Per le regole della cache personalizzate sono disponibili due condizioni di corrispondenza:
 
- **Percorso**: Questa condizione corrispondente al percorso dell'URL, escluso il nome di dominio e supporta il carattere jolly (\*). Ad esempio, _/myfile.html_, _/my/folder/*_ e _/my/images/*.jpg_. La lunghezza massima consentita è di 260 caratteri.

- **Estensione**: Questa condizione corrispondente all'estensione di file del file richiesto. È possibile specificare un elenco di estensioni di file delimitati da virgole per la corrispondenza. Ad esempio, _.jpg_, _.mp3_ o _.png_. Il numero massimo di estensioni è 50 e il numero massimo di caratteri per estensione è 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordine di elaborazione delle regole globali e personalizzate
Le regole di memorizzazione nella cache globali e personalizzate vengono elaborate nell'ordine seguente:

- Le regole di memorizzazione nella cache globali hanno precedenza rispetto al comportamento di memorizzazione nella cache della rete CDN predefinito (impostazioni delle intestazioni delle direttive della cache HTTP). 

- Le regole di memorizzazione nella cache personalizzate hanno precedenza rispetto alle regole di memorizzazione nella cache globali, laddove applicabili. Le regole di memorizzazione nella cache personalizzate vengono elaborate dall'alto verso il basso. Ovvero, se una richiesta corrisponde a entrambe le condizioni, le regole nella parte inferiore dell'elenco hanno precedenza rispetto alle regole nella parte superiore dell'elenco. È pertanto necessario inserire più regole specifiche nella parte inferiore dell'elenco.

**Esempio**:
- Regola di memorizzazione nella cache globale: 
   - Comportamento di memorizzazione nella cache: **Override**
   - Durata scadenza cache: 1 giorno

- Regola di memorizzazione nella cache personalizzata n. 1:
   - Condizione di corrispondenza: **Percorso**
   - Valore di corrispondenza: _/home/*_
   - Comportamento di memorizzazione nella cache: **Override**
   - Durata scadenza cache: 2 giorni

- Regola di memorizzazione nella cache n. 2:
   - Condizione di corrispondenza: **Estensione**
   - Valore di corrispondenza: _.html_
   - Comportamento di memorizzazione nella cache: **Imposta se mancante**
   - Durata scadenza cache: 3 giorni

Quando queste regole sono impostate, una richiesta per  _&lt;nome host dell'endpoint&gt;_ trigger.azureedge.net/home/index.html memorizzazione nella cache personalizzate regola #2, che è impostata su: **Imposta se mancante** e 3 giorni. Di conseguenza, se il file *index.html* include intestazioni HTTP `Cache-Control` o `Expires`, queste vengono rispettate. In caso contrario, se le intestazioni non sono impostate, il file viene memorizzato nella cache per 3 giorni.

> [!NOTE] 
> I file memorizzati nella cache prima della modifica di una regola mantengono l'impostazione di durata della cache di origine. Per reimpostare le durate della cache, è necessario [ripulire il file](cdn-purge-endpoint.md). 
>
> La propagazione delle modifiche alla configurazione della rete CDN di Azure può richiedere tempo: 
> - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
> - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Verizon** viene in genere completata entro 10 minuti.  
>

## <a name="see-also"></a>Vedere anche 

- [Funzionamento della memorizzazione nella cache](cdn-how-caching-works.md)
- [Esercitazione: Impostare le regole di memorizzazione nella cache della rete CDN di Azure](cdn-caching-rules-tutorial.md)
