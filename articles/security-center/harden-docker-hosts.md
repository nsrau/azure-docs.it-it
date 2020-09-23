---
title: Usare il Centro sicurezza di Azure per rafforzare gli host Docker e proteggere i contenitori
description: Come proteggere gli host Docker e verificare che siano conformi al benchmark Docker di CIS
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 25d8d3dee8810311ae25f54c0e51e34bdf918c09
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937207"
---
# <a name="harden-your-docker-hosts"></a>Proteggere in modo avanzato gli host Docker

Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in macchine virtuali IaaS Linux o altri computer Linux che eseguono contenitori docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Viene quindi confrontato con il [benchmark Docker di Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole del benchmark Docker di CIS e avvisa l'utente se i contenitori non soddisfano i controlli. Quando rileva configurazioni non configurate, il Centro sicurezza genera raccomandazioni sulla sicurezza. Utilizzare la **pagina raccomandazioni** per visualizzare le raccomandazioni e correggere i problemi. Verranno inoltre visualizzati i consigli nella scheda **contenitori** che Visualizza tutte le macchine virtuali distribuite con Docker. 

Quando vengono rilevate vulnerabilità, vengono raggruppate all'interno di un'unica raccomandazione.

>[!NOTE]
> Questi controlli di benchmark di CIS non vengono eseguiti nelle macchine virtuali gestite da AKS o dalle macchine virtuali gestite da databricks.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|Richiede [Azure Defender per i server](defender-for-servers-introduction.md)|
|Ruoli e autorizzazioni necessari:|**Reader** nell'area di lavoro a cui si connette l'host|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identificare e correggere le vulnerabilità di sicurezza nella configurazione di Docker

1. Dal menu del Centro sicurezza aprire la pagina **raccomandazioni** .

1. Filtrare per le vulnerabilità delle raccomandazioni **nelle configurazioni di sicurezza del contenitore da correggere** e selezionare la raccomandazione.

    La pagina di raccomandazione Mostra le risorse interessate (host Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Raccomandazione per correggere le vulnerabilità nelle configurazioni di sicurezza del contenitore ":::

1. Per visualizzare e correggere i controlli CIS non riusciti da un host specifico, selezionare l'host che si desidera analizzare. 

    > [!TIP]
    > Se è stata avviata la pagina inventario asset ed è stata raggiunta questa raccomandazione da qui, seleziona il pulsante di **azione** nella pagina di raccomandazione.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Pulsante azione per avviare Log Analytics":::

    Log Analytics si apre con un'operazione personalizzata pronta per l'esecuzione. La query personalizzata predefinita include un elenco di tutte le regole non riuscite valutate, insieme alle linee guida che consentono di risolvere i problemi.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics pagina con la query che Mostra tutti i controlli CIS non riusciti":::

1. Se necessario, modificare i parametri della query.

1. Quando si è certi che il comando sia appropriato e pronto per l'host, selezionare **Esegui**.


## <a name="next-steps"></a>Passaggi successivi

Docker hardening è solo un aspetto delle funzionalità di sicurezza del contenitore del Centro sicurezza. 

Ulteriori informazioni [sulla sicurezza del contenitore sono disponibili nel centro sicurezza](container-security.md).