---
title: Introduzione a TmaxSoft OpenFrame macchine virtuali di Azure
description: Rehosting IBM z/OS mainframe carichi di lavoro con ambiente TmaxSoft OpenFrame in macchine virtuali (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485437"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introduzione a TmaxSoft OpenFrame in Azure

Eseguire i tuoi asset esistenti mainframe e spostarli in Microsoft Azure usando TmaxSoft OpenFrame. Questa soluzione di riallocazione comune consente di creare un ambiente di emulazione in Azure, consentendo di eseguire rapidamente la migrazione di applicazioni. Nessun riformattazione è obbligatorio.

## <a name="openframe-rehosting-environment"></a>Ambiente di riallocazione OpenFrame

Configurare un ambiente OpenFrame in Azure per sviluppo e le demo o per i carichi di lavoro di produzione. Come illustrato nella figura seguente, OpenFrame include più componenti che crea l'ambiente di emulazione di mainframe in Azure. Ad esempio, servizi online OpenFrame sostituiscono il middleware di mainframe, ad esempio IBM Customer informazioni Control System (CICS). OpenFrame Batch, con il componente relativo TJES, sostituisce processo voce di sottosistema (JES del mainframe IBM). 

![Processo di riallocazione OpenFrame](media/openframe-01.png)

> [!NOTE]
> Per eseguire l'ambiente OpenFrame in Azure, è necessario disporre di una licenza valida o valutazione da TmaxSoft.

## <a name="openframe-components"></a>Componenti OpenFrame

I componenti seguenti fanno parte dell'ambiente OpenFrame in Azure:

- **Strumenti di migrazione** inclusi OFMiner, una soluzione che consente di analizzare gli asset di mainframe e quindi ne esegue la migrazione ad Azure.
- **I compilatori**, tra cui OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe. OFPLI, che interpreta PL del mainframe / programmi; e OFASM, un compilatore che interpreta i programmi di assembler del mainframe.
- **Front-end** componenti, tra cui il componente gateway OpenFrame che fornisce un listener 3270 Java Enterprise utente soluzione (JEUS) e un server applicazioni web che è certificato per 6.OFGW Java Enterprise Edition.
- **Applicazione** ambiente. OpenFrame Base è il middleware che gestisce l'intero sistema. OpenFrame Server tipo C (Oschooser) sostituisce middleware al mainframe e IBM CICS.
- **Database relazionale**, ad esempio Tibero (mostrato), Oracle Database, Microsoft SQL Server, IBM Db2 o MySQL. Le applicazioni OpenFrame usano il protocollo Open Database Connectivity (ODBC) per comunicare con il database.
- **Sicurezza** tramite TACF, un modulo del servizio che controlla l'accesso utente ai sistemi e alle risorse. 
- **OFManager** è una soluzione che offre funzioni di funzionamento e alla gestione dell'OpenFrame nell'ambiente di web.

![Architettura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Installare TmaxSoft OpenFrame in Azure](./install-openframe-azure.md)
