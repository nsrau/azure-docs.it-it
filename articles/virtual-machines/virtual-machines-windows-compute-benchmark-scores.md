<properties
 pageTitle="Calcolare i punteggi di benchmark per le VM Windows | Microsoft Azure"
 description="Confrontare i punteggi di benchmark di SPECint calcolo per le VM di Azure che eseguono Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Calcolare i punteggi di benchmark per le VM Windows

I seguenti punteggi del benchmark SPECInt mostrano le prestazioni di calcolo per la linea di VM ad alte prestazioni di Azure con Windows Server. I punteggi di benchmark sul calcolo sono disponibili anche per le [VM Linux](virtual-machines-linux-compute-benchmark-scores.md).



## Serie A - Elevato utilizzo di calcolo


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | CPU Intel Xeon E5-2670 0 a 2,6 GHz | 10 | 236,1 | 1\.1
Standard\_A9 | 16 | 2 | CPU Intel Xeon E5-2670 0 a 2,6 GHz | 10 | 450,3 | 7\.0
Standard\_A10 | 8 | 1 | CPU Intel Xeon E5-2670 0 a 2,6 GHz | 5 | 235,6 | 0\.9
Standard\_A11 | 16 | 2 | CPU Intel Xeon E5-2670 0 a 2,6 GHz |7 | 454,7 | 4\.8

## Serie Dv2


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 83 | 36,6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 27 | 70 | 3,7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 130,5 | 4\.4
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 238,1 | 5,2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 14 | 460,9 | 15,4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 70,1 | 3,7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 2 | 132 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 17 | 235,8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 15 | 460,8 | 6,5


## Serie G, GS serie


Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1, Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 31 | 71,8 | 6,5
Standard\_G2, Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 5 | 133,4 | 13
Standard\_G3, Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 6 | 242,3 | 6\.0
Standard\_G4, Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 15 | 398,9 | 6\.0
Standard\_G5, Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 22 | 762,8 | 3,7

## Informazioni su SPECint

I valori di Windows sono stati calcolati eseguendo [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) in Windows Server. SPECint è stato eseguito utilizzando la velocità di base (SPECint\_rate2006), con una copia per core. SPECint consiste in 12 test distinti, ognuno eseguito tre volte prendendo i valori mediani di tutti i test e ponderandoli in modo da ottenere un punteggio composito. Dopodiché, i valori sono stati eseguiti tra più VM per arrivare alla media dei punteggi illustrata.


## Passaggi successivi

* Per conoscere le capacità di archiviazione, i dettagli sul disco e per considerazioni aggiuntive sulla scelta delle dimensioni delle VM, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-windows-sizes.md).

<!---HONumber=AcomDC_0727_2016-->