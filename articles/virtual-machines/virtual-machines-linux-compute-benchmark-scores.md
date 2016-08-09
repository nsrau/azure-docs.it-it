<properties
 pageTitle="Calcolare i punteggi di benchmark per le VM Linux | Microsoft Azure"
 description="Confrontare i punteggi di benchmark di CoreMark calcolo per le VM di Azure che eseguono Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Calcolare i punteggi di benchmark per le VM Linux

I seguenti punteggi del benchmark CoreMark mostrano le prestazioni di calcolo per la linea di VM ad alte prestazioni di Azure con Ubuntu. I punteggi di benchmark sul calcolo sono disponibili anche per le [VM Windows](virtual-machines-windows-compute-benchmark-scores.md).




## Serie A - Elevato utilizzo di calcolo


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | CPU Intel Xeon E5-2670 0 a 2,6 GHz| 179 | 110\.294 | 554
Standard\_A9 | 16 | 2 | CPU Intel Xeon E5-2670 0 a 2,6 GHz| 189 | 210\.816| 2\.126
Standard\_A10 | 8 | 1 | CPU Intel Xeon E5-2670 0 a 2,6 GHz| 188 | 110\.025 | 1\.045
Standard\_A11 | 16 | 2 | CPU Intel Xeon E5-2670 0 a 2,6 GHz| 188 | 210\.727| 2\.073

## Serie Dv2


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 14\.852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 133 | 29\.467 | 1\.863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 139 | 56\.205 | 1\.167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 108\.543 | 3\.446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 205\.332 | 9\.998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 125 | 28\.598 | 1\.510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 131 | 55\.673 | 1\.418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 107\.986 | 3\.089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 208\.186 | 8\.839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz |28 | 268\.560 | 4\.667

## Serie F

Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 154 | 15\.602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 29\.519 | 1\.233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 147 | 58\.709 | 1\.227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 224 | 112\.772 | 3\.006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 42 | 218\.571 | 5\.113



## Serie G


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 31\.310 | 2\.891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 60\.112 | 3\.537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 107\.522 | 4\.537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 195\.116 | 5\.024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 360\.329 | 14\.212

## Serie GS


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 28\.613 | 1\.884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 54\.348 | 3\.474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 104\.564 | 1\.834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 194\.111 | 4\.735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 357\.396 | 16\.228


## Informazioni su CoreMark

I numeri di Linux sono stati calcolati eseguendo [CoreMark](http://www.eembc.org/coremark/faq.php) su Ubuntu. CoreMark è stato configurato con il numero di thread impostato sul numero di CPU virtuali e la concorrenza impostata su PThreads. Il numero di iterazioni è stato rettificato in base alle prestazioni previste per fornire un runtime di almeno 20 secondi (ma in genere molto di più); il punteggio finale rappresentava il numero di iterazioni completate, diviso per la durata del test espressa in secondi. Ogni test è stato eseguito almeno sette volte per ogni VM. I test sono stati eseguiti nell'ottobre 2015 su più VM in ogni area pubblica di Azure in cui erano supportate le macchine virtuali alla data di esecuzione.
## Passaggi successivi



* Per conoscere le capacità di archiviazione, i dettagli sul disco e per considerazioni aggiuntive sulla scelta delle dimensioni delle VM, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

* Per eseguire gli script CoreMark nelle VM Linux, scaricare il [pacchetto di script CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

<!---HONumber=AcomDC_0727_2016-->