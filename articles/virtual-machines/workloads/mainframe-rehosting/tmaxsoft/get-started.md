---
title: Introduzione a TmaxSoft OpenFrame in macchine virtuali di Azure
description: Riospitare i carichi di lavoro del mainframe IBM z/OS usando l'ambiente OpenFrame di TmaxSoft in macchine virtuali di Azure.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: 215a17ced6be4cc8792ac1a06115450bfbccac99
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963267"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introduzione a TmaxSoft OpenFrame in Azure

Prendere le risorse mainframe esistenti e spostarle in Microsoft Azure usando TmaxSoft OpenFrame. Questa popolare soluzione di riallocazione crea un ambiente di emulazione in Azure, consentendo di eseguire rapidamente la migrazione delle applicazioni. Non è richiesta alcuna riformattazione.

## <a name="openframe-rehosting-environment"></a>Ambiente di riallocazione OpenFrame

Configura un ambiente OpenFrame in Azure per carichi di lavoro di sviluppo, demo, test o produzione. Come illustrato nella figura seguente, OpenFrame include più componenti che creano l'ambiente di emulazione mainframe in Azure. Ad esempio, OpenFrame Servizi online sostituire il middleware mainframe come IBM Customer Information Control System (CICS). OpenFrame batch, con il relativo componente TJES, sostituisce il sottosistema di immissione dei processi (JES) del mainframe IBM. 

![Processo di riallocazione OpenFrame](media/openframe-01.png)

> [!NOTE]
> Per eseguire l'ambiente OpenFrame in Azure, è necessario avere una licenza del prodotto valida o una licenza di valutazione di TmaxSoft.

## <a name="openframe-components"></a>Componenti di OpenFrame

I componenti seguenti fanno parte dell'ambiente OpenFrame in Azure:

- **Strumenti di migrazione** , tra cui OFMiner, una soluzione che analizza gli asset mainframe e li esegue quindi la migrazione in Azure.
- **Compilatori**, incluso OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe; OFPLI, che interpreta i programmi del mainframe PL/I; e OFASM, un compilatore che interpreta i programmi assembler del mainframe.
- Componenti **front-end** , tra cui Java Enterprise User Solution (JEUS), un server applicazioni web certificato per Java Enterprise Edition 6. OFGW e il componente gateway OpenFrame che fornisce un listener 3270.
- Ambiente **dell'applicazione** . OpenFrame base è il middleware che gestisce l'intero sistema. OpenFrame Server Type C (OSC) sostituisce il middleware del mainframe e IBM CICS.
- **Database relazionale**, ad esempio tibero (visualizzato), Oracle Database, Microsoft SQL Server, IBM DB2 o MySQL. Le applicazioni OpenFrame utilizzano il protocollo ODBC (Open Database Connectivity) per comunicare con il database.
- **Sicurezza** tramite TACF, un modulo del servizio che controlla l'accesso degli utenti a sistemi e risorse. 
- **OFManager** è una soluzione che fornisce funzioni operative e di gestione di OpenFrame nell'ambiente Web.

![Architettura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Installare TmaxSoft OpenFrame in Azure](./install-openframe-azure.md)
