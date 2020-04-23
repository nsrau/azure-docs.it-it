---
title: Vantaggi dell'utilizzo dei file NetApp di Azure con Oracle Database . Documenti Microsoft
description: Viene descritta la tecnologia e viene fornito un confronto delle prestazioni tra Oracle Direct NFS (dNFS) e il client NFS tradizionale. Illustra i vantaggi dell'uso di dNFS con i file NetApp di Azure.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: a73da39dafcc8be78fbe1c023693ffa4a19aa1d3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085009"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Vantaggi dell'uso di Azure NetApp Files con Oracle Database

Oracle Direct NFS (dNFS) consente di ottenere prestazioni più elevate rispetto al driver NFS del sistema operativo. In questo articolo viene illustrata la tecnologia e viene fornito un confronto delle prestazioni tra dNFS e il client NFS tradizionale (Kernel NFS). Illustra inoltre i vantaggi e la facilità di utilizzo di dNFS con i file NetApp di Azure.It also shows the advantages and the ease of using dNFS with Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Come funziona Oracle Direct NFS

Oracle Direct NFS (dNFS) ignora la cache del buffer del sistema operativo. I dati vengono memorizzati nella cache una sola volta nello spazio utente, eliminando l'overhead delle copie della memoria.  

Il client NFS tradizionale utilizza un singolo flusso di rete, come illustrato nell'esempio seguente:The traditional NFS client uses a single network flow, as the following example shows: 

![Client NFS tradizionale che utilizza un singolo flusso di rete](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Al contrario, dNFS Oracle migliora le prestazioni bilanciando il traffico di rete tra più flussi di rete. Questa funzionalità consente al database Oracle di stabilire dinamicamente un numero significativo di 650 connessioni di rete distinte, come illustrato nell'esempio seguente:This capability enables the Oracle database to dynamically establish a significant number of 650 distinct network connections, as shown in the example below:  

![Oracle Direct NFS migliora le prestazioni](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

Le [domande frequenti su Oracle per NFS diretto](http://www.orafaq.com/wiki/Direct_NFS) mostrano che Oracle dNFS è un client NFS ottimizzato. Fornisce un accesso rapido e scalabile all'archiviazione NFS che si trova su dispositivi di archiviazione NAS (accessibili tramite TCP/IP). dNFS è integrato nel kernel del database proprio come ASM, che viene utilizzato principalmente con l'archiviazione DAS o SAN. Di conseguenza, *la linea guida consiste nell'utilizzare dNFS quando si implementa l'archiviazione NAS e utilizzare ASM durante l'implementazione dell'archiviazione SAN.*

dNFS è l'opzione predefinita in Oracle 18c.

dNFS è disponibile a partire da Oracle Database 11g. Il diagramma seguente confronta il file DNFS con NFS nativo. Quando si usa dNFS, un database Oracle in esecuzione in una macchina virtuale di Azure può guidare più I/O rispetto al client NFS nativo.

![Confronto tra i file NetApp Oracle e Azure di dNFS con NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

È possibile abilitare o disabilitare dNFS eseguendo due comandi e riavviando il database.

Per abilitare:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Per disattivare:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>File NetApp di Azure combinati con Oracle Direct NFS

È possibile migliorare le prestazioni di DNFS Oracle con il servizio File di Rete di Azure.You can enhance the performance of Oracle dNFS with the Azure NetApp Files service. Il servizio offre il controllo totale sulle prestazioni dell'applicazione. Può soddisfare applicazioni estremamente impegnative. La combinazione di Oracle dNFS con Azure NetApp Files offre un grande vantaggio ai carichi di lavoro.

## <a name="next-steps"></a>Passaggi successivi

- [Architetture di soluzioni con Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Panoramica delle applicazioni e delle soluzioni Oracle in AzureOverview of Oracle Applications and solutions on Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
