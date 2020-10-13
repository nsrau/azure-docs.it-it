---
title: Ridimensionamento di applicazioni HPC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come ridimensionare le applicazioni HPC nelle VM di Azure.
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2c20fcf2624c1f620b68b46e56e0602f15041348
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993832"
---
# <a name="scaling-hpc-applications"></a>Ridimensionamento di applicazioni HPC

Prestazioni ottimali di scalabilità verticale e orizzontale delle applicazioni HPC in Azure sono necessari esperimenti di ottimizzazione e ottimizzazione delle prestazioni per il carico di lavoro specifico. Questa sezione e le pagine specifiche della serie VM offrono indicazioni generali per la scalabilità delle applicazioni.

## <a name="compiling-applications"></a>Compilazione di applicazioni

Sebbene non sia necessario, la compilazione di applicazioni con flag di ottimizzazione appropriati offre le migliori prestazioni di scalabilità verticale sulle VM serie HB e HC.

### <a name="amd-optimizing-cc-compiler"></a>AMD ottimizzazione del compilatore C/C++

Il sistema di compilazione AMD Optimizing C/C++ Compiler (AOCC) offre un elevato livello di ottimizzazioni, multithreading e supporto del processore avanzati che includono l'ottimizzazione globale, la vettorizzazione, le analisi tra procedure, le trasformazioni di cicli e la generazione di codice. I file binari del compilatore AOCC sono adatti per i sistemi Linux con la libreria GNU C (glibc) versione 2,17 e successive. Il gruppo di compilatori è costituito da un compilatore C/C++ (Clang), un compilatore FORTRAN (flangia) e un front-end FORTRAN per Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang è un compilatore C, C++ e Objective-C che gestisce la pre-elaborazione, l'analisi, l'ottimizzazione, la generazione di codice, l'assembly e il collegamento. Clang supporta il  `-march=znver1` flag per abilitare la generazione di codice migliore e l'ottimizzazione per l'architettura x86 basata su Zen di AMD.

### <a name="flang"></a>FLANG

Il compilatore della flangia è una recente aggiunta a AOCC Suite (aggiunta il 2018 aprile) ed è attualmente in versione non definitiva per gli sviluppatori da scaricare e testare. In base a Fortran 2008, AMD estende la versione GitHub della flangia ( https://github.com/flang-compiler/flang) . Il compilatore della flangia supporta tutte le opzioni del compilatore Clang e un numero aggiuntivo di opzioni del compilatore specifiche della flangia.

### <a name="dragonegg"></a>DragonEgg

DragonEgg è un plug-in GCC che sostituisce gli ottimizzatori e i generatori di codice di GCC con quelli del progetto LLVM. La DragonEgg fornita con AOCC funziona con GCC-4.8. x, è stata testata per le destinazioni x86-32/x86-64 ed è stata usata correttamente su diverse piattaforme Linux.

GFortran è il front-end effettivo per i programmi FORTRAN responsabili della pre-elaborazione, dell'analisi e della semantica di generazione della rappresentazione intermedia GIMPLE (IR) GCC. DragonEgg è un plug-in GNU, che si connette al flusso di compilazione GFortran. Implementa l'API del plug-in GNU. Con l'architettura del plug-in, DragonEgg diventa il driver del compilatore, guidando le diverse fasi di compilazione.  Dopo aver seguito le istruzioni per il download e l'installazione, Dragon Egg può essere richiamato usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilatore IGP
IGP Community Edition ver. 17 è confermato per funzionare con AMD EPYC. Una versione del flusso compilata da IGP fornisce una larghezza di banda di memoria completa della piattaforma. La versione più recente della community 18,10 (nov 2018) dovrebbe funzionare in modo analogo. Di seguito è riportato un esempio di interfaccia della riga di comando per il compilatore ottimale con il compilatore Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilatore Intel
Compilatore Intel ver. 18 è confermato per funzionare con AMD EPYC. Di seguito è riportato un esempio di interfaccia della riga di comando per il compilatore ottimale con il compilatore Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilatore GCC 
Per HPC, AMD consiglia il compilatore GCC 7,3 o versione successiva. Non sono consigliate le versioni precedenti, ad esempio 4.8.5 incluse con RHEL/CentOS 7,4. GCC 7,3 e versioni successive offrono prestazioni significativamente superiori nei test HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Ridimensionamento delle applicazioni 

I suggerimenti seguenti si applicano per ottimizzare l'efficienza, le prestazioni e la coerenza dell'applicazione:

* Aggiungere processi a Core 0-59 usando un approccio di blocco sequenziale (in contrapposizione a un approccio di bilanciamento automatico). 
* Il binding per NUMA/Core/HwThread è migliore dell'associazione predefinita.
* Per le applicazioni ibride parallele (OpenMP + MPI), usare 4 thread e 1 MPI rango per ogni CCX.
* Per le applicazioni MPI pure, provare a usare i livelli MPI 1-4 per CCX per ottenere prestazioni ottimali.
* Alcune applicazioni con una notevole sensibilità alla larghezza di banda della memoria possono trarre vantaggio dall'uso di un numero ridotto di core per CCX. Per queste applicazioni, l'uso di 3 o 2 core per CCX può ridurre la contesa della larghezza di banda della memoria e ottenere prestazioni più elevate del mondo reale o scalabilità più uniforme. In particolare, MPI Allreduce può trarre vantaggio da questo problema.
* Per le esecuzioni di scala notevolmente più grandi, è consigliabile usare i trasporti UD o ibridi RC + UD. Molte librerie MPI/librerie di runtime eseguono questa operazione internamente, ad esempio UCX o MVAPICH2. Controllare le configurazioni del trasporto per le esecuzioni su larga scala.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.
