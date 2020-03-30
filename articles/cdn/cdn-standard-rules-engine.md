---
title: Usare un motore di regole per applicare HTTPS nella rete CDN standard di Azure. Documenti Microsoft
description: Usare il motore regole per la rete per la distribuzione di contenuti di Azure standard (CDN di Azure) di Microsoft Azure per personalizzare il modo in cui la rete CDN di Azure gestisce le richieste HTTP, incluso il blocco del recapito di determinati tipi di contenuto, la definizione di criteri di memorizzazione nella cache e la modifica delle intestazioni HTTP. In questo articolo viene illustrato come creare una regola per reindirizzare gli utenti a HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171581"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurare il motore regole Standard per la rete CDN di AzureSet up the Standard rules engine for Azure CDN

Questo articolo descrive come configurare e usare il motore regole Standard per la rete per la distribuzione di contenuti di Azure.This article describes how to set up and use the Standard rules engine for Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Motore regole standard

È possibile usare il motore regole Standard per la rete CDN di Azure per personalizzare la modalità di gestione delle richieste HTTP. Ad esempio, è possibile utilizzare il motore regole per applicare la distribuzione del contenuto a protocolli specifici, per definire criteri di memorizzazione nella cache o per modificare un'intestazione HTTP. In questo articolo viene illustrato come creare una regola che reindirizza automaticamente gli utenti a HTTPS. 

> [!NOTE]
> Il motore regole descritto in questo articolo è disponibile solo per la rete CDN standard di Azure di Microsoft.The rules engine that's described in this article is available only for Standard Azure CDN from Microsoft. 

## <a name="redirect-users-to-https"></a>Reindirizzare gli utenti a HTTPS

1. Nei profili Microsoft passare a Rete per la distribuzione di contenuti di Azure.In your Microsoft profiles, go to Azure Content Delivery Network.

1. Nella pagina **Profilo rete CDN** selezionare l'endpoint per cui si desidera creare le regole.
  
1. Selezionare la scheda **Motore regole di** business.
   
    Viene visualizzato il riquadro **Motore regole** di business e visualizza l'elenco delle regole globali disponibili. 
   
    [![Pagina delle nuove regole della rete CDN di AzureAzure CDN new rules page](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > L'ordine in cui sono elencate più regole influisce sulla modalità di gestione delle regole. Le azioni specificate in una regola potrebbero essere sovrascritte da una regola successiva.
   >

1. Selezionare **Aggiungi regola** e immettere un nome per la regola. I nomi delle regole devono iniziare con una lettera e possono contenere solo numeri e lettere.

1. Per identificare il tipo di richieste a cui si applica la regola, creare una condizione di corrispondenza:
    1. Selezionare **Aggiungi condizione**, quindi selezionare la condizione di richiesta di corrispondenza **protocollo** .
    1. In **Operatore** selezionare **è uguale a**.
    1. In **Valore**selezionare **HTTP**.
   
   [![Condizione di corrispondenza regola della rete CDN di AzureAzure CDN rule match condition condition](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > È possibile selezionare una delle condizioni di corrispondenza multiple nell'elenco a discesa **Aggiungi condizione.** Per un elenco dettagliato delle condizioni di corrispondenza, vedere [Condizioni di corrispondenza nel motore regole Standard](cdn-standard-rules-engine-match-conditions.md).
   
1. Selezionare l'azione da applicare alle richieste che soddisfano la condizione di corrispondenza:
   1. Selezionare **Aggiungi azione**, quindi selezionare **Reindirizzamento URL**.
   1. Per **Tipo**, selezionare **Trovato (302)**.
   1. Per **Protocollo** selezionare **HTTPS**.
   1. Lasciare vuoti tutti gli altri campi per utilizzare i valori in entrata.
   
   [![Azione regola rete CDN di AzureAzure CDN rule action](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > È possibile selezionare una delle due azioni nell'elenco a discesa **Aggiungi azione.** Per un elenco dettagliato delle azioni, vedere [Azioni nel motore regole Standard](cdn-standard-rules-engine-actions.md).

6. Selezionare **Salva** per salvare la nuova regola. La regola è ora disponibile per l'uso.
   
   > [!IMPORTANT]
   > La propagazione delle modifiche alle regole nella rete CDN di Azure potrebbe richiedere fino a 15 minuti.
   >
   

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza nel motore regole StandardMatch conditions in the Standard rules engine](cdn-standard-rules-engine-match-conditions.md)
- [Azioni nel motore regole StandardActions in the Standard rules engine](cdn-standard-rules-engine-actions.md)
