---
title: Script per abilitare kdump in SAP HANA (istanze large) | Microsoft Docs
description: Script per abilitare kdump in SAP HANA (istanze large) HLI tipo I, HLI tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d723e95212e457a81eedf7726bf3c5bd2499643
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84488886"
---
# <a name="enable-kdump-service"></a>Abilita servizio kdump

Questo documento descrive i dettagli su come abilitare il servizio kdump in istanze large di Azure HANA (**tipo I e tipo II**)

## <a name="supported-skus"></a>SKU supportati

|  Tipo di istanza large di Hana   |  Fornitore del sistema operativo   |  Versione del pacchetto del sistema operativo   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Prerequisiti

- Il servizio kdump USA `/var/crash` la directory per scrivere i dump, assicurarsi che la partizione corrisponda a questa directory con spazio sufficiente per contenere i dump.

## <a name="setup-details"></a>Dettagli di configurazione

- Lo script per abilitare kdump è disponibile [qui](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Eseguire questo script in un'istanza large di HANA usando il comando seguente

    > [!NOTE]
    > privilegio sudo necessario per eseguire questo comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se il comando restituisce kdump è stato abilitato correttamente, riavviare il sistema per applicare la modifica, quindi il kdump viene abilitato correttamente. Riavviare il sistema per applicare le modifiche.

- Se l'output del comando non è riuscito a eseguire determinate operazioni, l'uscita!!!!, il servizio kdump non è abilitato. Vedere la sezione [problema di supporto](#support-issue).

## <a name="test-kdump"></a>Test kdump

> [!NOTE]
>  L'operazione seguente attiverà un arresto anomalo del kernel e il riavvio del sistema.

- Attivare un arresto anomalo del kernel

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Dopo il riavvio del sistema, controllare la `/var/crash` Directory per i log di arresto anomalo del kernel.

- Se `/var/crash` dispone di una directory con la data corrente, il kdump viene abilitato correttamente.

## <a name="support-issue"></a>Problemi di supporto

Se lo script ha esito negativo con un errore o kdump non è abilitato, generare la richiesta di servizio con il team di supporto Microsoft con i dettagli seguenti.

* ID sottoscrizione HLI

* Nome server

* Fornitore del sistema operativo

* Versione sistema operativo

* Versione del kernel
