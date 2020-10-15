---
title: Monitorare il dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto come usare il portale di Azure e l'interfaccia utente Web locale per monitorare il Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cd75eb0f7de602979f2233a873c01ef742471e37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904400"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Monitorare il Azure Stack Edge Pro

Questo articolo descrive come monitorare il Azure Stack Edge Pro. Per monitorare il dispositivo, è possibile usare il portale di Azure o l'interfaccia utente Web locale. Usare il portale di Azure per visualizzare gli eventi del dispositivo, configurare e gestire gli avvisi e visualizzare le metriche. Usare l'interfaccia utente Web locale sul dispositivo fisico per visualizzare lo stato dell'hardware dei vari componenti del dispositivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Visualizzare gli eventi del dispositivo e gli avvisi corrispondenti
> * Visualizzare lo stato dell'hardware dei componenti del dispositivo
> * Visualizzare le metriche di capacità e delle transazioni per il dispositivo
> * Configurare e gestire gli avvisi

## <a name="view-device-events"></a>Visualizzare gli eventi del dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visualizzare lo stato dell'hardware

Per visualizzare lo stato dell'hardware dei componenti del dispositivo, seguire questa procedura nell'interfaccia utente Web locale.

1. Connettersi all'interfaccia utente Web locale del dispositivo.
2. Selezionare **Manutenzione > Stato hardware**. È possibile visualizzare lo stato di integrità dei vari componenti del dispositivo.

    ![Visualizzare lo stato dell'hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visualizzare le metriche

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metriche del dispositivo

Questa sezione descrive le metriche di monitoraggio del dispositivo. Le metriche possono essere:

* Metriche di capacità. Le metriche della capacità sono relative alla capacità del dispositivo.

* Metriche di transazione. Le metriche di transazione sono relative alle operazioni di lettura e scrittura in Archiviazione di Azure.

* Metriche di calcolo Edge. Le metriche di calcolo Edge sono correlate all'uso del calcolo Edge sul dispositivo.

Nella tabella seguente è riportato un elenco completo delle metriche:

|Metriche della capacità                     |Descrizione  |
|-------------------------------------|-------------|
|**Capacità disponibile**               | Si riferisce alle dimensioni dei dati che possono essere scritti nel dispositivo. In altre parole, è la capacità che può essere resa disponibile nel dispositivo. <br></br>È possibile liberare la capacità del dispositivo eliminando la copia locale dei file che hanno una copia nel dispositivo e nel cloud.        |
|**Capacità totale**                   | Si riferisce ai byte totali nel dispositivo in cui scrivere i dati. È detta anche dimensioni totali della cache locale. <br></br> È possibile aumentare la capacità di un dispositivo virtuale esistente tramite l'aggiunta di un disco dati. Aggiungere un disco dati tramite la gestione dell'hypervisor per la macchina virtuale e quindi riavviare la macchina virtuale. Il pool di archiviazione locale del dispositivo gateway si espanderà per adattarsi al disco dati appena aggiunto. <br></br>Per altre informazioni, vedere [Add a hard drive for Hyper-V virtual machine](https://www.youtube.com/watch?v=EWdqUw9tTe4) (Aggiungere un disco rigido per la macchina virtuale Hyper-V). |

|Metriche di transazione              | Descrizione         |
|-------------------------------------|---------|
|**Byte cloud caricati (dispositivo)**    | Somma di tutti i byte caricati in tutte le condivisioni nel dispositivo.        |
|**Byte cloud caricati (condivisione)**     | Byte caricati per condivisione. Può trattarsi di: <br></br> Medio, ovvero la somma di tutti i byte caricati per condivisione/numero di condivisioni  <br></br>Massimo, ovvero il numero massimo di byte caricati da una condivisione <br></br>Minimo, ovvero il numero massimo di byte caricati da una condivisione      |
|**Velocità effettiva download cloud (condivisione)**| Byte scaricati per condivisione. Può trattarsi di: <br></br> Medio, ovvero la somma di tutti i byte letti o scaricati in una condivisione/numero di condivisioni <br></br> Massimo, ovvero il numero massimo di byte scaricati da una condivisione<br></br> Minimo, ovvero il numero minimo di byte scaricati da una condivisione  |
|**Velocità effettiva lettura cloud**            | Somma di tutti i byte letti dal cloud in tutte le condivisioni nel dispositivo.     |
|**Velocità effettiva caricamento cloud**          | Somma di tutti i byte scritti nel cloud in tutte le condivisioni nel dispositivo.     |
|**Velocità effettiva caricamento cloud (condivisione)**  | Somma di tutti i byte scritti nel cloud da una condivisione/numero di condivisioni medio, massimo e minimo per condivisione      |
|**Velocità effettiva lettura (rete)**           | Include la velocità effettiva della rete di sistema per tutti i byte letti dal cloud. Questa visualizzazione può includere dati che non sono limitati alle condivisioni. <br></br>La suddivisione mostrerà il traffico su tutte le schede di rete nel dispositivo, incluse quelle non connesse o abilitate.      |
|**Velocità effettiva scrittura (rete)**       | Include la velocità effettiva della rete di sistema per tutti i byte scritti nel cloud. Questa visualizzazione può includere dati che non sono limitati alle condivisioni. <br></br>La suddivisione mostrerà il traffico su tutte le schede di rete nel dispositivo, incluse quelle non connesse o abilitate.          |

| Metriche di calcolo Edge              | Descrizione         |
|-------------------------------------|---------|
|**Calcolo Edge - Utilizzo memoria**      |           |
|**Calcolo Edge - Percentuale CPU**    |         |

## <a name="manage-alerts"></a>Gestisci avvisi

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire la larghezza di banda](azure-stack-edge-manage-bandwidth-schedules.md).
