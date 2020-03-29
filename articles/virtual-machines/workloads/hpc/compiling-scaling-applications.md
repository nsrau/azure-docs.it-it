---
title: Scalabilità delle applicazioni HPC - Macchine virtuali di Azure Documenti Microsoft
description: Informazioni su come ridimensionare le applicazioni HPC nelle macchine virtuali di Azure.Learn how to scale HPC applications on Azure VMs.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707831"
---
# <a name="scaling-hpc-applications"></a>Scalabilità delle applicazioni HPC

Le prestazioni ottimali di scalabilità verticale e orizzontale delle applicazioni HPC in Azure richiedono esperimenti di ottimizzazione e ottimizzazione delle prestazioni per il carico di lavoro specifico. Questa sezione e le pagine specifiche della serie di macchine virtuali offrono indicazioni generali per la scalabilità delle applicazioni.

## <a name="compiling-applications"></a>Compilazione di applicazioni

Sebbene non sia necessario, la compilazione di applicazioni con flag di ottimizzazione appropriati offre le migliori prestazioni di scalabilità verticale nelle macchine virtuali HB e serie HC.

### <a name="amd-optimizing-cc-compiler"></a>Compilatore AMD Ottimizzazione C/C

Il sistema del compilatore AMD Optimizing C/Cè Compiler (AOCC) offre un elevato livello di ottimizzazioni avanzate, multithreading e supporto del processore che include ottimizzazione globale, vettorizzazione, analisi interprocedurali, trasformazioni di loop e generazione di codice. I file binari del compilatore AOCC sono adatti per i sistemi Linux con GNU C Library (glibc) versione 2.17 e successive. La suite di compilatori è costituita da un compilatore C/C, un compilatore Fortran (FLANG) e un front-end Fortran a Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang è un compilatore C, C , e Objective-C che gestisce la pre-elaborazione, l'analisi, l'ottimizzazione, la generazione di codice, l'assembly e il collegamento. Clang supporta `-march=znver1` il flag per consentire la migliore generazione di codice e l'ottimizzazione per l'architettura x86 basata su zen di AMD.

### <a name="flang"></a>FLANG

Il compilatore FLANG è una recente aggiunta alla suite AOCC (aggiunto aprile 2018) ed è attualmente in pre-release per gli sviluppatori da scaricare e testare. Basato su Fortran 2008, AMD estende lahttps://github.com/flangcompiler/flang)versione GitHub di FLANG ( . Il compilatore FLANG supporta tutte le opzioni del compilatore Clang e un numero aggiuntivo di opzioni del compilatore specifiche di FLANG.

### <a name="dragonegg"></a>DragoUovo

DragonEgg è un plugin gcc che sostituisce gli ottimizzatori e i generatori di codice di GCC con quelli del progetto LLVM. DragonEgg che viene fornito con AOCC funziona con gcc-4.8.x, è stato testato per x86-32/x86-64 obiettivi ed è stato utilizzato con successo su varie piattaforme Linux.

GFortran è il frontend effettivo per i programmi Fortran responsabili della pre-elaborazione, dell'analisi e dell'analisi semantica che genera la rappresentazione intermedia GCC GIMPLE (IR). DragonEgg è un plugin GNU, che si collega al flusso di compilazione GFortran. Implementa l'API del plugin GNU. Con l'architettura plugin, DragonEgg diventa il driver del compilatore, guidando le diverse fasi della compilazione.  Dopo aver seguito le istruzioni di download e installazione, Dragon Egg può essere richiamato utilizzando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilatore IGP
PGI Community Edition ver. 17 è confermato per lavorare con AMD EPYC. Una versione compilata da IGP di STREAM fornisce la larghezza di banda della memoria completa della piattaforma. Anche la nuova Community Edition 18.10 (novembre 2018) dovrebbe funzionare bene. Di seguito è riportato l'interfaccia della riga di comando di esempio per il compilatore in modo ottimale con il compilatore Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilatore Intel
Compilatore Intel ver. 18 è confermato per lavorare con AMD EPYC. Di seguito è riportato l'interfaccia della riga di comando di esempio per il compilatore in modo ottimale con il compilatore Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilatore GCC 
Per HPC, AMD consiglia il compilatore GCC 7.3 o più recente. Le versioni precedenti, come 4.8.5 incluse in RHEL/CentOS 7.4, non sono consigliate. GCC 7.3 e versioni successive, fornirà prestazioni significativamente più elevate nei test HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Ridimensionamento delle applicazioni 

I suggerimenti seguenti si applicano per migliorare l'efficienza, le prestazioni e la coerenza delle applicazioni ottimali:The following suggestions apply for optimal application scaling efficiency, performance, and consistency:

* Processi di pin ai core 0-59 utilizzando un approccio di blocco sequenziale (anziché un approccio di bilanciamento automatico). 
* L'associazione per Numa/Core/HwThread è migliore dell'associazione predefinita.
* Per le applicazioni parallele ibride (OpenMP-MPI), utilizzare 4 thread e 1 rango MPI per CCX.
* Per le applicazioni MPI pure, sperimentare con 1-4 gradi MPI per CCX per prestazioni ottimali.
* Alcune applicazioni con estrema sensibilità alla larghezza di banda della memoria possono trarre vantaggio dall'utilizzo di un numero ridotto di core per CCX. Per queste applicazioni, l'utilizzo di 3 o 2 core per CCX può ridurre i conflitti di larghezza di banda della memoria e ottenere prestazioni reali più elevate o una scalabilità più coerente. In particolare, MPI Allreduce può trarne vantaggio.
* Per le esecuzioni su scala significativamente più grande, si consiglia di utilizzare i trasporti UD o ibrido RC-UD. Molte librerie MPI/librerie di runtime eseguire questa operazione internamente (ad esempio UCX o MVAPICH2). Controllare le configurazioni di trasporto per le condutture su larga scala.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su HPC in Azure.Learn more about [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) on Azure.
