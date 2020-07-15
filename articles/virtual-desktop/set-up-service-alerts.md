---
title: Configurare avvisi del servizio per Desktop virtuale Windows - Azure
description: Come configurare l'integrità dei servizi di Azure per ricevere notifiche del servizio per Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 035fc4840b08a0a9807c228cb5f63c191efc524a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256884"
---
# <a name="tutorial-set-up-service-alerts"></a>Esercitazione: Configurare gli avvisi dei servizi

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile usare il servizio di integrità dei servizi di Azure per monitorare i problemi del servizio e gli avvisi sull'integrità per Desktop virtuale Windows. Il servizio di integrità dei servizi di Azure è in grado di inviare notifiche con tipi diversi di avvisi (ad esempio messaggi di posta elettronica o SMS), di rendere più semplice la comprensione degli effetti dei problemi e di offrire informazioni aggiornate durante la risoluzione dei problemi stessi. Questo servizio è anche in grado di favorire la riduzione del tempo di inattività e la preparazione alla manutenzione pianificata e alle modifiche che possono influire sulla disponibilità delle risorse.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e configurare avvisi del servizio.

Per altre informazioni sull'integrità dei servizi di Azure, vedere la [documentazione del servizio di integrità dei servizi di Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="create-service-alerts"></a>Creare avvisi del servizio

Questa sezione illustra come configurare il servizio di integrità dei servizi di Azure e come impostare le notifiche, accessibili dal portale di Azure. È possibile configurare avvisi di tipo diverso, programmandoli in modo da ricevere una notifica in modo tempestivo.

### <a name="recommended-service-alerts"></a>Avvisi del servizio consigliati

È consigliabile creare avvisi del servizio per i tipi di evento di integrità seguenti:

- **Problema del servizio:** ricevere notifiche in caso di problemi rilevanti che influiscono sulla connettività degli utenti al servizio o sulla possibilità di gestire il tenant di Desktop virtuale Windows.
- **Avviso di integrità:** ricevere notifiche degli aspetti che richiedono attenzione. Ecco alcuni esempi di notifiche di questo tipo:
    - Macchine virtuali (VM) configurate in modo non sicuro con la porta 3389 aperta
    - Funzionalità deprecata

### <a name="configure-service-alerts"></a>Configurare avvisi del servizio

Per configurare avvisi del servizio:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Integrità dei servizi**.
3. Seguire le istruzioni in [Creare gli avvisi del log attività per le notifiche del servizio](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) per configurare gli avvisi e le notifiche.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare e usare il servizio di integrità dei servizi di Azure per monitorare i problemi del servizio e gli avvisi sull'integrità per Desktop virtuale Windows. Per informazioni su come accedere a Desktop virtuale Windows, continuare con le procedure per connettersi a Desktop virtuale Windows.

> [!div class="nextstepaction"]
> [Connettersi al client di Desktop remoto in Windows 7 e Windows 10](./connect-windows-7-10.md)
