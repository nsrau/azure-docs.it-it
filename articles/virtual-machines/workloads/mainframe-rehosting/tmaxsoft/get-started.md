---
title: Introduzione a TmaxSoft OpenFrame in Macchine virtuali di AzureGet started with TmaxSoft OpenFrame on Azure Virtual Machines
description: Riospita i carichi di lavoro mainframe IBM z/OS usando l'ambiente OpenFrame TmaxSoft nelle macchine virtuali di Azure.Rehost your IBM z/OS mainframe workloads using TmaxSoft OpenFrame environment on Azure Virtual Machines (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485437"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introduzione a TmaxSoft OpenFrame in AzureGet started with TmaxSoft OpenFrame on Azure

Prendi le tue risorse mainframe esistenti e spostale in Microsoft Azure usando TmaxSoft OpenFrame. Questa soluzione di rehosting comune crea un ambiente di emulazione in Azure, consentendo di eseguire rapidamente la migrazione delle applicazioni. Non è richiesta alcuna riformattazione.

## <a name="openframe-rehosting-environment"></a>Ambiente di rehosting OpenFrame

Configurare un ambiente OpenFrame in Azure per carichi di lavoro di sviluppo, demo, test o produzione. Come illustrato nella figura seguente, OpenFrame include più componenti che creano l'ambiente di emulazione mainframe in Azure.As the following figure shows, OpenFrame includes multiple components that create the mainframe emulation environment on Azure. Ad esempio, i servizi online OpenFrame sostituiscono il middleware mainframe mainframe come IBM Customer Information Control System (CICS). OpenFrame Batch, con il suo componente TJES, sostituisce il Job Entry Subsystem (JES) del mainframe IBM. 

![Processo di rehosting OpenFrame](media/openframe-01.png)

> [!NOTE]
> Per eseguire l'ambiente OpenFrame in Azure, è necessario disporre di una licenza di prodotto valida o di una licenza di valutazione da TmaxSoft.To run the OpenFrame environment on Azure, you must have a valid product license or trial license from TmaxSoft.

## <a name="openframe-components"></a>Componenti OpenFrame

I componenti seguenti fanno parte dell'ambiente OpenFrame in Azure:

- Strumenti di **migrazione,** tra cui OFMiner, una soluzione che analizza gli asset mainframes e quindi ne esegue la migrazione in Azure.Migration tools including OFMiner, a solution that analyzes the mainframes assets and then migrates them to Azure.
- **Compilatori**, incluso OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe; OFPLI, che interpreta i programmi PL/I del mainframe; e OFASM, un compilatore che interpreta i programmi assembler del mainframe.
- **Componenti front-end,** tra cui Java Enterprise User Solution (JEUS), un server applicazioni Web certificato per Java Enterprise Edition 6.OFGW e il componente gateway OpenFrame che fornisce un listener 3270.
- **Ambiente applicativo.** OpenFrame Base è il middleware che gestisce l'intero sistema. OpenFrame Server Type C (OSC) sostituisce il middleware mainframe e IBM CICS.
- **Database relazionale**, ad esempio Tibero (illustrato), Oracle Database, Microsoft SQL Server, IBM Db2 o MySQL. Le applicazioni OpenFrame utilizzano il protocollo ODBC (Open Database Connectivity) per comunicare con il database.
- **Sicurezza** tramite TACF, un modulo di servizio che controlla l'accesso degli utenti a sistemi e risorse. 
- **OFManager** è una soluzione che fornisce le funzioni operative e di gestione di OpenFrame nell'ambiente Web.

![Architettura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Installare TmaxSoft OpenFrame in AzureInstall TmaxSoft OpenFrame on Azure](./install-openframe-azure.md)
