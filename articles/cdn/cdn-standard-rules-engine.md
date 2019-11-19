---
title: Usare un motore regole per applicare HTTPS nella rete CDN standard di Azure | Microsoft Docs
description: Usare il motore regole per la rete per la distribuzione di contenuti di Azure standard Microsoft (rete CDN di Azure) per personalizzare il modo in cui la rete CDN di Azure gestisce le richieste HTTP, incluso il blocco del recapito di determinati tipi di contenuto, la definizione di criteri di memorizzazione nella cache e la modifica In questo articolo viene illustrato come creare una regola per reindirizzare gli utenti a HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171581"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurare il motore regole standard per la rete CDN di Azure

Questo articolo descrive come configurare e usare il motore regole standard per la rete per la distribuzione di contenuti di Azure (rete CDN di Azure).

## <a name="standard-rules-engine"></a>Motore regole standard

Per personalizzare il modo in cui vengono gestite le richieste HTTP, è possibile usare il motore regole standard per la rete CDN di Azure. Ad esempio, è possibile usare il motore regole di per applicare la distribuzione del contenuto per usare protocolli specifici, per definire un criterio di memorizzazione nella cache o per modificare un'intestazione HTTP. Questo articolo illustra come creare una regola che reindirizza automaticamente gli utenti a HTTPS. 

> [!NOTE]
> Il motore regole descritto in questo articolo è disponibile solo per la rete CDN standard di Azure di Microsoft. 

## <a name="redirect-users-to-https"></a>Reindirizza gli utenti a HTTPS

1. Nel profilo Microsoft passare alla rete per la distribuzione di contenuti di Azure.

1. Nella pagina **profilo rete CDN** selezionare l'endpoint per il quale si vogliono creare le regole.
  
1. Selezionare la scheda **motore regole** .
   
    Si apre il riquadro **motore regole** e viene visualizzato l'elenco delle regole globali disponibili. 
   
    [![pagina nuove regole della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > L'ordine in cui vengono elencate più regole influiscono sul modo in cui vengono gestite le regole. Le azioni specificate in una regola potrebbero essere sovrascritte da una regola successiva.
   >

1. Selezionare **Aggiungi regola** e immettere un nome per la regola. I nomi delle regole devono iniziare con una lettera e possono contenere solo numeri e lettere.

1. Per identificare il tipo di richieste a cui viene applicata la regola, creare una condizione di corrispondenza:
    1. Selezionare **Aggiungi condizione**, quindi selezionare la condizione di corrispondenza del **protocollo di richiesta** .
    1. Per **operator**selezionare **Equals**.
    1. Per **valore**selezionare **http**.
   
   [![condizione di corrispondenza della regola della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > È possibile scegliere tra più condizioni di corrispondenza nell'elenco a discesa **Aggiungi condizione** . Per un elenco dettagliato delle condizioni di corrispondenza, vedere [condizioni di corrispondenza nel motore regole standard](cdn-standard-rules-engine-match-conditions.md).
   
1. Selezionare l'azione da applicare alle richieste che soddisfano la condizione di corrispondenza:
   1. Selezionare **Aggiungi azione**, quindi selezionare **Reindirizzamento URL**.
   1. Per **tipo**selezionare **trovato (302)** .
   1. Per **Protocollo** selezionare **HTTPS**.
   1. Lasciare vuoti tutti gli altri campi per usare i valori in ingresso.
   
   [![azione regola della rete CDN di Azure](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > È possibile scegliere tra più azioni nell'elenco a discesa **Aggiungi azione** . Per un elenco dettagliato delle azioni, vedere [azioni nel motore delle regole standard](cdn-standard-rules-engine-actions.md).

6. Selezionare **Save (Salva** ) per salvare la nuova regola. La regola è ora disponibile per l'uso.
   
   > [!IMPORTANT]
   > La propagazione delle modifiche alle regole potrebbe richiedere fino a 15 minuti attraverso la rete CDN di Azure.
   >
   

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Riferimento al motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza nel motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Azioni nel motore regole standard](cdn-standard-rules-engine-actions.md)
