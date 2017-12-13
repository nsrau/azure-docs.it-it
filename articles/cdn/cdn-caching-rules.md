---
title: Controllare il comportamento di memorizzazione nella cache della rete per la distribuzione di contenuti (CDN) di Azure con regole | Microsoft Docs
description: "È possibile usare le regole di memorizzazione nella cache della rete CDN o modificare il comportamento predefinito di scadenza della cache a livello globale e con condizioni, ad esempio un percorso URL e le estensioni dei file."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 8f89ef5a1763d5fc4ad09a9aeae89ccf683138c7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole

> [!NOTE] 
> Le regole di memorizzazione nella cache sono disponibili solo per la **rete CDN di Azure di Verizon Standard** e la **rete CDN di Azure di Akamai Standard**. Per la **rete CDN di Azure di Verizon Premium** è possibile usare il [motore delle regole della rete CDN di Azure](cdn-rules-engine.md) nel portale a cui si accedere tramite il pulsante **Gestisci** per funzionalità analoghe.
 
La rete CDN di Azure offre due modi per controllare la modalità di memorizzazione nella cache dei file: 

- Regole di memorizzazione nella cache: questo articolo descrive come è possibile usare le regole di memorizzazione nella cache della rete CDN o modificare il comportamento predefinito di scadenza della cache a livello globale e con condizioni personalizzate, ad esempio un percorso URL e le estensioni dei file. La rete CDN di Azure offre due tipi di regole di memorizzazione nella cache:
   - Regole di memorizzazione nella cache globali: è possibile impostare una regola di memorizzazione nella cache globale per ogni endpoint nel profilo, che influisce su tutte le richieste all'endpoint. La regola di memorizzazione nella cache globale esegue l'override di eventuali intestazioni di direttive della cache HTTP, se impostate.
   - Regole di memorizzazione nella cache personalizzate: è possibile impostare una o più regole di memorizzazione nella cache personalizzate per ogni endpoint nel profilo. Le regole di memorizzazione nella cache personalizzate corrispondono a percorsi ed estensioni dei file specifici, vengono elaborate in ordine e sostituiscono la regola di memorizzazione nella cache globale, se impostata. 

- Memorizzazione nella cache di stringhe di query: è possibile modificare il modo in cui la rete CDN di Azure gestisce la memorizzazione nella cache per le richieste con stringhe di query. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string.md). Se il file non è memorizzabile nella cache, l'impostazione di memorizzazione nella cache delle stringhe di query non ha alcun effetto, in base alle regole di memorizzazione e ai comportamenti predefiniti della rete CDN.

Per informazioni sul comportamento predefinito e sulle intestazioni delle direttive di memorizzazione nella cache, vedere [Funzionamento della memorizzazione nella cache](cdn-how-caching-works.md).

## <a name="tutorial"></a>Esercitazione

Come impostare regole di memorizzazione nella cache della rete CDN:

1. Aprire il portale di Azure, selezionare un profilo della rete CDN e selezionare un endpoint.
2. Nel riquadro sinistro, in Impostazioni, fare clic su **Cache**.
3. Creare una regola di memorizzazione nella cache globale come segue:
   1. In **Regole di memorizzazione nella cache globali** impostare **Comportamento di memorizzazione nella cache della stringa di query** su **Ignora stringhe di query**.
   2. Impostare **Comportamento di memorizzazione nella cache** su **Imposta se mancante**.
   3. Per **Durata scadenza cache** immettere 10 nel campo **Giorni**.

       La regola di memorizzazione nella cache globale influisce su tutte le richieste all'endpoint. Questa regola rispetta le intestazioni delle direttive della cache di origine, se presenti (`Cache-Control` o `Expires`); in caso contrario, se non sono specificate, imposta la cache su 10 giorni. 

4. Creare una regola di memorizzazione nella cache personalizzata come segue:
    1. In **Regole di memorizzazione nella cache personalizzate** impostare **Condizione di corrispondenza** su **Percorso** e **Valori di corrispondenza** su `/images/*.jpg`.
    2. Impostare **Comportamento di memorizzazione nella cache** su **Override** e immettere 30 nel campo **Giorni**.
       
       Questa regola di memorizzazione nella cache personalizzata imposta una durata della cache di 30 giorni in qualsiasi file di immagine `.jpg` nella cartella `/images` dell'endpoint. Esegue l'override di qualsiasi intestazione HTTP `Cache-Control` o `Expires` inviata dal server di origine.

  ![Finestra di dialogo Regole di memorizzazione nella cache](./media/cdn-caching-rules/cdn-caching-rules-dialog.png)

> [!NOTE] 
> I file memorizzati nella cache prima della modifica di una regola mantengono l'impostazione di durata della cache di origine. Per reimpostare le durate della cache, è necessario [ripulire il file](cdn-purge-endpoint.md). Per gli endpoint della **rete CDN di Azure di Verizon** l'attuazione delle regole di memorizzazione nella cache può richiedere fino a 90 minuti.

## <a name="reference"></a>riferimento

### <a name="caching-behavior-settings"></a>Impostazioni del comportamento di memorizzazione nella cache
Per le regole di memorizzazione nella cache globali e personalizzate, è possibile specificare le impostazioni di **Comportamento di memorizzazione nella cache** seguenti:

- **Ignora cache**: non viene eseguita la memorizzazione nella cache e le intestazioni delle direttive della cache di origine vengono ignorate.
- **Override**: le intestazioni delle direttive della cache di origine vengono ignorate e viene usata la durata della cache fornita.
- **Imposta se mancante**: vengono rispettate le intestazioni delle direttive della cache di origine, se presenti; in caso contrario, viene usata la durata della cache fornita.

### <a name="cache-expiration-duration"></a>Durata di scadenza della cache
Per le regole di memorizzazione nella cache globali e personalizzate è possibile specificare la durata di scadenza della cache in giorni, ore, minuti e secondi:

- Per le impostazioni **Override** e **Imposta se mancante** di **Comportamento di memorizzazione nella cache**, le durate della cache valide sono comprese tra 0 secondi e 366 giorni. Per un valore di 0 secondi, la rete CDN memorizza nella cache il contenuto, ma deve ripetere la convalida di ogni richiesta con il server di origine.
- Per l'impostazione **Ignora cache** la durata della cache viene impostata automaticamente su 0 secondi e non può essere modificata.

### <a name="custom-caching-rules-match-conditions"></a>Condizioni di corrispondenza delle regole di memorizzazione nella cache personalizzate

Per le regole della cache personalizzate sono disponibili due condizioni di corrispondenza:
 
- **Percorso**: questa condizione esegue la corrispondenza del percorso dell'URL, escluso il nome di dominio, e supporta il carattere jolly (\*). Ad esempio, `/myfile.html`, `/my/folder/*`, e `/my/images/*.jpg`. La lunghezza massima consentita è di 260 caratteri.

- **Estensione**: questa condizione esegue la corrispondenza dell'estensione del file richiesto. È possibile specificare un elenco di estensioni di file delimitati da virgole per la corrispondenza. Ad esempio, `.jpg`, `.mp3` o `.png`. Il numero massimo di estensioni è 50 e il numero massimo di caratteri per estensione è 16. 

### <a name="global-and-custom-rule-processing-order"></a>Ordine di elaborazione delle regole globali e personalizzate
Le regole di memorizzazione nella cache globali e personalizzate vengono elaborate nell'ordine seguente:

- Le regole di memorizzazione nella cache globali hanno precedenza rispetto al comportamento di memorizzazione nella cache della rete CDN predefinito (impostazioni delle intestazioni delle direttive della cache HTTP). 

- Le regole di memorizzazione nella cache personalizzate hanno precedenza rispetto alle regole di memorizzazione nella cache globali, laddove applicabili. Le regole di memorizzazione nella cache personalizzate vengono elaborate dall'alto verso il basso. Ovvero, se una richiesta corrisponde a entrambe le condizioni, le regole nella parte inferiore dell'elenco hanno precedenza rispetto alle regole nella parte superiore dell'elenco. È pertanto necessario inserire più regole specifiche nella parte inferiore dell'elenco.

**Esempio**:
- Regola di memorizzazione nella cache globale: 
   - Comportamento di memorizzazione nella cache: **Override**
   - Durata scadenza cache: 1 giorno

- Regola di memorizzazione nella cache personalizzata n. 1:
   - Condizione di corrispondenza: **Percorso**
   - Valori di corrispondenza: `/home/*`
   - Comportamento di memorizzazione nella cache: **Override**
   - Durata scadenza cache: 2 giorni

- Regola di memorizzazione nella cache n. 2:
   - Condizione di corrispondenza: **Estensione**
   - Valori di corrispondenza: `.html`
   - Comportamento di memorizzazione nella cache: **Imposta se mancante**
   - Durata scadenza cache: 3 giorni

Se sono impostate queste regole, una richiesta per `<endpoint>.azureedge.net/home/index.html` attiva la regola di memorizzazione nella cache personalizzata n. 2, impostata su **Imposta se mancante** e 3 giorni. Pertanto, se il file `index.html` include le intestazioni HTTP `Cache-Control` o `Expires`, queste vengono rispettate; in caso contrario, se le intestazioni non sono impostate, il file viene memorizzato nella cache per 3 giorni.

