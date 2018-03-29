---
title: Creare un servizio di bilanciamento del carico interno - Portale di Azure | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico interno in Resource Manager con il portale di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4cb61b60f97238a3c34953c679f177ef16056092
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Creare un servizio di bilanciamento del carico interno nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Introduzione alla creazione di un servizio di bilanciamento del carico interno tramite il portale di Azure

Per creare un servizio di bilanciamento del carico interno dal portale di Azure, seguire questa procedura.

1. Aprire un browser, passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.
2. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Load Balancer**.
3. Nel pannello **Crea servizio di bilanciamento del carico** immettere un **nome** per il servizio di bilanciamento del carico.
4. In **Tipo** fare clic su **Interno**.
5. Fare clic su **Rete virtuale**, quindi selezionare la rete virtuale in cui si desidera creare il servizio di bilanciamento del carico.

   > [!NOTE]
   > Se la rete virtuale che si desidera usare non viene visualizzata, controllare il **Percorso** per il servizio di bilanciamento del carico e modificarlo di conseguenza.

6. Fare clic su **Subnet**e scegliere la subnet in cui creare il servizio di bilanciamento del carico.
7. In **Assegnazione indirizzo IP** fare clic su **Dinamico** o **Statico**, a seconda che si voglia un servizio di bilanciamento del carico con indirizzo IP fisso, cioè statico, o dinamico.

   > [!NOTE]
   > Se si sceglie un indirizzo IP statico, è necessario fornire un indirizzo per il servizio di bilanciamento del carico.

8. In **Gruppo di risorse** specificare il nome di un nuovo gruppo di risorse per il servizio di bilanciamento del carico oppure fare clic su **Seleziona esistente** e scegliere un gruppo di risorse esistente.
9. Fare clic su **Crea**.

## <a name="configure-load-balancing-rules"></a>Configurare le regole del servizio di bilanciamento del carico

Dopo la creazione del servizio di bilanciamento di carico, individuare la risorsa di bilanciamento del carico per configurarla.
Prima di configurare una regola di bilanciamento del carico, configurare un pool di indirizzi back-end e un probe.

### <a name="step-1-configure-a-backend-pool"></a>Passaggio 1: Configurare un pool back-end

1. Nel portale di Azure fare clic su **Sfoglia** > **Servizi di bilanciamento del carico** e fare clic sul servizio di bilanciamento del carico creato prima.
2. Nella pagina **Impostazioni** fare clic su **Pool back-end**.
3. Nella pagina **Pool di indirizzi back-end** fare clic su **Aggiungi**.
4. Nella pagina **Aggiungi pool back-end** immettere un **nome** per il pool back-end e fare clic su **OK**.

### <a name="step-2-configure-a-probe"></a>Passaggio 2: Configurare un probe

1. Nel portale di Azure fare clic su **Sfoglia** > **Servizi di bilanciamento del carico** e fare clic sul servizio di bilanciamento del carico creato prima.
2. Nella pagina **Impostazioni** fare clic su **Probe integrità**.
3. Nella pagina **Probe integrità** fare clic su **Aggiungi**.
4. Nella pagina **Aggiungi probe integrità** immettere un **nome** per il probe.
5. In **Protocollo** selezionare **HTTP** per i siti Web o **TCP** per le altre applicazioni basate su TCP.
6. In **Porta**specificare la porta da usare per accedere al probe.
7. Solo per i probe HTTP, in **Percorso** specificare il percorso da usare come probe.
8. In **Intervallo** specificare la frequenza di probe per l'applicazione.
9. In **Soglia di non integrità**specificare il numero di tentativi con esito negativo ammessi prima che la macchina virtuale back-end sia contrassegnata come danneggiata.
10. Fare clic su **OK** per creare il probe.

### <a name="step-3-configure-load-balancing-rules"></a>Passaggio 3: Configurare le regole del servizio di bilanciamento del carico

1. Nel portale di Azure fare clic su **Sfoglia** > **Servizi di bilanciamento del carico** e fare clic sul servizio di bilanciamento del carico creato prima.
2. Nella pagina **Impostazioni** fare clic su **Regole di bilanciamento del carico**.
3. Nella pagina **Regole di bilanciamento del carico** fare clic su **Aggiungi**.
4. Nella pagina **Aggiungi regola di bilanciamento del carico** immettere un **nome** per la regola.
5. In **Protocollo** selezionare **TCP** o **UDP**.
6. In **Porta** specificare la porta del servizio di bilanciamento del carico a cui i client devono connettersi.
7. In **Porta back-end** specificare la porta da usare nel pool di back-end. La porta del servizio di bilanciamento del carico e la porta di back-end sono solitamente la stessa.
8. In **Pool back-end**selezionare il pool back-end creato prima.
9. In **Salvataggio permanente sessione** selezionare la modalità di salvataggio delle sessioni.
10. In **Timeout di inattività (minuti)**specificare il timeout di inattività.
11. In **IP mobile (Direct Server Return)** fare clic su **Disabilitato** o **Abilitato**.
12. Fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

