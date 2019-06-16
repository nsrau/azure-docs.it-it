---
title: Scalabilità delle applicazioni HPC - macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come ridimensionare le applicazioni HPC nella VM di Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: e2e2476449f956361639e42e7c398e53e42d44ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810113"
---
# <a name="scaling-hpc-applications"></a>Scalabilità delle applicazioni HPC

Ottenere prestazioni ottimali scalabilità verticale e orizzontale di applicazioni HPC in Azure richiede l'ottimizzazione delle prestazioni e ottimizzazione esperimenti per il carico di lavoro specifico. In questa sezione e le pagine specifiche della macchina virtuale offrono indicazioni generali per la scalabilità delle applicazioni.

## <a name="compiling-applications"></a>La compilazione di applicazioni

Sebbene non necessario, la compilazione di applicazioni con flag di ottimizzazione appropriato offre le migliori prestazioni di scalabilità verticale su HB HC-VM serie a e.

### <a name="amd-optimizing-cc-compiler"></a>AMD ottimizzazione di C /C++ compilatore

L'ottimizzazione di AMD C /C++ del compilatore (AOCC) del compilatore system offre un elevato livello di ottimizzazione avanzate, il multithreading e supporto del processore che include l'ottimizzazione globale, la vettorizzazione, analisi tra routine, le trasformazioni di ciclo, e generazione di codice. I file binari del compilatore AOCC sono adatti per i sistemi Linux con libreria GNU C versione 2.17 (Glibc versioni da) e versioni successive. Il gruppo del compilatore è costituito da una C /C++ del compilatore (clang), un compilatore di Fortran (FLANG) e un front-end Fortran Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang è C, C++e la gestione di pre-elaborazione, l'analisi, ottimizzazione, la generazione di codice, assembly e il collegamento del compilatore di Objective-C. Clang supporta il `-march=znver1` flag per consentire una migliore generazione del codice e ottimizzazione per Zen AMD basati su x86 architettura.

### <a name="flang"></a>FLANG

Il compilatore FLANG è un'aggiunta recente a suite AOCC (aggiunta di aprile 2018) ed è attualmente in versione non definitiva per gli sviluppatori per scaricare e test. Base 2008 Fortran, AMD estende la versione di GitHub di FLANG (https://github.com/flangcompiler/flang). Il compilatore FLANG supporta tutte le opzioni del compilatore Clang e un numero aggiuntivo di opzioni del compilatore FLANG specifici.

### <a name="dragonegg"></a>DragonEgg

DragonEgg è un plug-in gcc che sostituisce del GCC utilità di ottimizzazione e generatori di codice con quelli dal progetto LLVM. DragonEgg con AOCC funziona con gcc 4.8.x, è stato testato per destinazioni x86 a 32/x86 a 64 e che è stato usato correttamente su varie piattaforme Linux.

GFortran è il front-end effettivo per i programmi di Fortran responsabili pre-elaborazione, l'analisi e l'analisi semantica generazione rappresentazione intermedia GIMPLE GCC (IR). DragonEgg è un plug-in GNU, inserire nel flusso di compilazione GFortran. Implementa il plug-in di GNU API. Con l'architettura di plug-in, DragonEgg diventa il driver a compilatore, guidare le diverse fasi della compilazione.  Dopo aver seguito le istruzioni di download e l'installazione, Egg Dragon può essere richiamata usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilatore IGP
Community di IGP ver Edition. 17 viene confermato per lavorare con EPYC AMD. Una versione compilata IGP del flusso di fornire la larghezza di banda di memoria completa della piattaforma. La più recente della Community Edition 18.10 (novembre 2018) allo stesso modo dovrebbe funzionare correttamente. Di seguito è riportato l'esempio della riga di comando al compilatore in modo ottimale con il compilatore Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilatore di Intel
Versione del compilatore Intel 18 viene confermato per lavorare con EPYC AMD. Seguito è riportato l'esempio della riga di comando al compilatore in modo ottimale con il compilatore di Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilatore GCC 
Per HPC, AMD consiglia del compilatore GCC 7.3 o versione successiva. Le versioni precedenti, ad esempio 4.8.5 incluso con RHEL/CentOS 7.4, non sono consigliate. GCC 7.3 e versioni più recenti, garantirà prestazioni significativamente superiori in HPL HPCG e DGEMM test.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Ridimensionamento delle applicazioni 

I suggerimenti seguenti si applicano per applicazioni scala l'efficienza, prestazioni e coerenza:

* PIN elabora a core 0-59 usando un'aggiunta sequenziale approccio (in contrapposizione a un approccio di bilanciare in automatico). 
* Associazione di base Numa/Core/HwThread è migliore di associazione predefinita.
* Ibrida delle applicazioni parallele (OpenMP + MPI), utilizzare 1 classificazione MPI per CCX e 4 thread.
* Per le applicazioni MPI pure, provare a usare 1-4 che MPI classifica al CCX per ottenere prestazioni ottimali.
* Alcune applicazioni con estrema sensibilità alla larghezza di banda di memoria possono trarre vantaggio dall'utilizzo di un numero ridotto di core per CCX. Per queste applicazioni, con 3 o 2 core per CCX può ridurre la contesa di larghezza di banda di memoria e producono più coerente scalabilità o prestazioni più elevate del mondo reale. In particolare, allreduce, Reduction MPI può trarre vantaggio da questo.
* Per le operazioni di scalabilità significativamente più grande, si consiglia di utilizzare trasporti RC + UD UD o ibrida. Molte librerie di runtime o librerie MPI farlo internamente (ad esempio UCX o MVAPICH2). Controllare le configurazioni di trasporto per le operazioni su larga scala.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
