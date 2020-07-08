---
title: Usare un motore regole per applicare HTTPS nella rete CDN di Azure standard | Microsoft Docs
description: Usare il motore regole per la rete di distribuzione dei contenuti di Azure standard di Microsoft (rete CDN di Azure) per personalizzare il modo in cui la rete CDN di Azure gestisce le richieste HTTP, tra cui il blocco della distribuzione di determinati tipi di contenuto, la definizione di criteri di memorizzazione nella cache e la modifica delle intestazioni HTTP. Questo articolo illustra come creare una regola per reindirizzare gli utenti a HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 1c596bf10d8afbce504b5abf04eacb877989521a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887541"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurare il motore regole standard per la rete CDN di Azure

Questo articolo descrive come configurare e usare il motore regole standard per la rete di distribuzione dei contenuti di Azure (rete CDN di Azure).

## <a name="standard-rules-engine"></a>Motore regole standard

Il motore regole standard per la rete CDN di Azure consente di personalizzare la modalità di gestione delle richieste HTTP. È ad esempio possibile usare il motore regole per fare in modo che per la distribuzione di contenuto vengano usati protocolli specifici, per definire un criterio di memorizzazione nella cache o per modificare un'intestazione HTTP. Questo articolo illustra come creare una regola per il reindirizzamento automatico degli utenti a HTTPS. 

> [!NOTE]
> Il motore regole descritto in questo articolo è disponibile solo per la rete CDN di Azure standard di Microsoft. 

## <a name="redirect-users-to-https"></a>Reindirizzare gli utenti a HTTPS

1. Nei profili Microsoft passare alla rete di distribuzione dei contenuti di Azure.

1. Nella pagina **Profilo rete CDN** selezionare l'endpoint per cui si desidera creare le regole.
  
1. Fare clic sulla scheda **Motore regole**.
   
    Verrà visualizzato il riquadro **Motore regole** con l'elenco delle regole globali disponibili. 
   
    [![Pagina delle nuove regole della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Le azioni specificate in una regola potrebbero essere sovrascritte da una regola successiva.
   >

1. Selezionare **Aggiungi regola** e immettere un nome per la regola. I nomi delle regole devono iniziare con una lettera e possono contenere solo numeri e lettere.

1. Per identificare il tipo di richieste a cui verrà applicata la regola, creare una condizione di corrispondenza:
    1. Selezionare **Aggiungi condizione**, quindi selezionare la condizione di corrispondenza **Protocollo richiesta**.
    1. Per **Operatore** selezionare **Uguale**.
    1. Per **Valore** selezionare **HTTP**.
   
   [![Condizione di corrispondenza per la regola della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > È possibile scegliere tra più condizioni di corrispondenza dall'elenco a discesa **Aggiungi condizione**. Per un elenco dettagliato delle condizioni di corrispondenza, vedere [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md).
   
1. Selezionare l'azione da applicare alle richieste che soddisfano la condizione di corrispondenza:
   1. Selezionare **Aggiungi azione**, quindi **Reindirizzamento URL**.
   1. Per **Tipo** selezionare **302 - Trovato**.
   1. Per **Protocollo** selezionare **HTTPS**.
   1. Lasciare vuoti tutti gli altri campi per usare i valori in ingresso.
   
   [![Azione della regola della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > È possibile scegliere tra più azioni dall'elenco a discesa **Aggiungi azione**. Per un elenco dettagliato delle azioni, vedere [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md).

6. Scegliere **Salva** per salvare la nuova regola. La regola è ora disponibile per l'uso.
   
   > [!IMPORTANT]
   > La propagazione delle modifiche delle regole in tutta la rete CDN di Azure può richiedere fino a 15 minuti.
   >
   

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md)
