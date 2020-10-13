---
title: Vantaggi dell'utilizzo di Azure NetApp Files con Oracle Database | Microsoft Docs
description: Viene descritta la tecnologia e viene fornito un confronto tra le prestazioni tra Oracle Direct NFS (dNFS) e il client NFS tradizionale. Mostra i vantaggi dell'uso di dNFS con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932499"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Vantaggi dell'uso di Azure NetApp Files con Oracle Database

Oracle Direct NFS (dNFS) consente di ottenere prestazioni più elevate rispetto al driver NFS del sistema operativo. Questo articolo illustra la tecnologia e fornisce un confronto delle prestazioni tra dNFS e il client NFS tradizionale (kernel NFS). Vengono inoltre illustrati i vantaggi e la facilità di utilizzo di dNFS con Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Funzionamento di Oracle Direct NFS

Nel riepilogo seguente viene illustrato il funzionamento di Oracle Direct NFS a un livello elevato:

* Oracle Direct NFS ignora la cache del buffer del sistema operativo. I dati vengono memorizzati nella cache solo una volta nello spazio utente, eliminando l'overhead delle copie di memoria.  

* Il client NFS tradizionale usa un singolo flusso di rete, come illustrato di seguito:    

    ![Client NFS tradizionale con un singolo flusso di rete](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS migliora ulteriormente le prestazioni tramite il bilanciamento del carico del traffico di rete tra più flussi di rete. Come testato e mostrato di seguito, 650 connessioni di rete distinte sono state stabilite dinamicamente dal Oracle Database:  

    ![Oracle Direct NFS miglioramento delle prestazioni](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

Le [domande frequenti su Oracle per NFS diretto](http://www.orafaq.com/wiki/Direct_NFS) mostrano che Oracle dNFS è un client NFS ottimizzato. Fornisce accesso rapido e scalabile all'archiviazione NFS disponibile nei dispositivi di archiviazione NAS (accessibile tramite TCP/IP). dNFS è incorporato nel kernel del database proprio come ASM, che viene usato principalmente con DAS o l'archiviazione SAN. Di conseguenza, *la linea guida prevede l'uso di dNFS quando si implementa l'archiviazione NAS e si usa asm quando si implementa l'archiviazione San.*

dNFS è l'opzione predefinita in Oracle 18C.

dNFS è disponibile a partire da Oracle Database 11g. Il diagramma seguente confronta dNFS con NFS nativo. Quando si usa dNFS, un database Oracle in esecuzione in una macchina virtuale di Azure può gestire un numero maggiore di operazioni di I/O rispetto al client NFS nativo.

![Confronto tra Oracle e Azure NetApp Files di dNFS con NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

È possibile abilitare o disabilitare dNFS eseguendo due comandi e riavviando il database.

Per abilitare:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Per disabilitare:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files combinati con Oracle Direct NFS

È possibile migliorare le prestazioni di Oracle dNFS con il servizio Azure NetApp Files. Il servizio offre il controllo totale sulle prestazioni dell'applicazione. Può soddisfare applicazioni estremamente complesse. La combinazione di Oracle dNFS con Azure NetApp Files offre un notevole vantaggio ai carichi di lavoro.

## <a name="next-steps"></a>Passaggi successivi

- [Architetture di soluzioni con Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Panoramica delle applicazioni e delle soluzioni Oracle in Azure](../virtual-machines/workloads/oracle/oracle-overview.md)