---
title: Connettere ExpressRoute al gateway di rete virtuale
description: Passaggi per connettere ExpressRoute al gateway di rete virtuale.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598158"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Passare al cloud privato creato nell'esercitazione precedente e selezionare **Connettività** in **Gestisci**, quindi selezionare la scheda **ExpressRoute**.

1. Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. Selezionare **+ Richiedi una chiave di autorizzazione**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. Selezionare + Richiedi una chiave di autorizzazione." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Passare al gateway di rete virtuale creato nel passaggio precedente e quindi, in **Impostazioni**, selezionare **Connessioni**. Nella pagina **Connessioni** selezionare **+ Aggiungi**.

1. Nella pagina **Aggiungi connessione** specificare i valori per i campi e selezionare **OK**. 

   | Campo | Valore |
   | --- | --- |
   | **Nome**  | Immettere un nome per la connessione.  |
   | **Tipo di connessione**  | selezionare **ExpressRoute**.  |
   | **Riscatta autorizzazione**  | Assicurarsi che questa casella sia selezionata.  |
   | **Gateway di rete virtuale** | Gateway di rete virtuale creato in precedenza.  |
   | **Chiave di autorizzazione**  | Copiare a incollare la chiave di autorizzazione dalla scheda ExpressRoute per il gruppo di risorse. |
   | **URI del circuito peer**  | Copiare e incollare l'ID ExpressRoute dalla scheda ExpressRoute per il gruppo di risorse.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. Selezionare + Richiedi una chiave di autorizzazione." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Viene creata la connessione tra il circuito ExpressRoute e la rete virtuale.