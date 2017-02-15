---
title: Aggiungere ritardo nelle app per la logica | Microsoft Docs
description: Panoramica delle azioni di ritardo e ritardo fino a e di come usarle con un&quot;app per la logica di Azure.
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e4281c70dc0edd45ff485c64a499291b23ad51b7


---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Introduzione alle azioni si ritardo e ritardo fino a
Le azioni di ritardo e "ritardo fino a" consentono di completare scenari di flusso di lavoro.

Ad esempio, è possibile:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.
* Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati.

Per iniziare a usare un'azione di ritardo in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Usare le azioni di ritardo
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](connectors-overview.md).

Di seguito è riportata una sequenza di esempio di come usare un passaggio di ritardo in un'app per la logica:

1. Dopo aver aggiunto un trigger, fare clic su **Nuovo passaggio** per aggiungere un'azione.
2. Cercare **ritardo** per visualizzare le azioni di ritardo. In questo esempio si selezionerà **Ritarda**.
   
    ![Azioni di ritardo](./media/connectors-native-delay/using-action-1.png)
3. Completare tutte le proprietà dell'azione per configurare il ritardo.
   
    ![Configurazione del ritardo](./media/connectors-native-delay/using-action-2.png)
4. Fare clic su **Salva** per pubblicare e attivare l'app per la logica.

## <a name="action-details"></a>Informazioni dettagliate sulle azioni
Il trigger di ricorrenza ha le proprietà seguenti che possono essere configurate.

### <a name="delay-action"></a>Azione Ritarda
Questa azione ritarda l'esecuzione per un determinato intervallo di tempo.
Un asterisco (*) indica che è un campo obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Conteggio* |count |Il numero di unità di tempo di ritardo |
| Unità* |unit |Unità di tempo: `Second`, `Minute`, `Hour` o `Day` |

<br>

### <a name="delay-until-action"></a>Azione Ritarda fino a
Questa azione ritarda l'esecuzione fino a una data e ora specificate.
Un asterisco (*) indica che è un campo obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Anno* |timestamp |L'anno di termine del ritardo (GMT) |
| Mese* |timestamp |Il mese di termine del ritardo (GMT) |
| Giorno* |timestamp |Il giorno di termine del ritardo (GMT) |

<br>

## <a name="next-steps"></a>Passaggi successivi
Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l' [elenco di API](apis-list.md).




<!--HONumber=Nov16_HO3-->


