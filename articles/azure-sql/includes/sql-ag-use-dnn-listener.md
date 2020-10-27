---
title: Usare un listener del nome di rete distribuita (DNN, Distributed Network Name) invece di un listener del nome di rete virtuale (VNN, Virtual Network Name) per i gruppi di disponibilità nelle VM di SQL Server.
description: Messaggio per reindirizzare i clienti all'uso del listener di DNN invece del listener di VNN.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167994"
---
> [!NOTE]
> I clienti di SQL Server 2019 CU8 e versioni successive in Windows 2016 e versioni successive possono sostituire il tradizionale listener di VNN e Azure Load Balancer con un [listener del nome di rete distribuito (DNN)](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md). Ignorare i passaggi rimanenti di questo articolo per la creazione del listener e del servizio di bilanciamento del carico.
