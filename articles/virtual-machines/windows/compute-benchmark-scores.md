---
title: Calcolare i punteggi di benchmark per le VM Windows | Microsoft Docs
description: Confrontare i punteggi di benchmark di SPECint calcolo per le VM di Azure che eseguono Windows Server
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a3e4ce890d02290ba954119de8443d28378c0f98
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Calcolare i punteggi di benchmark per le VM Windows
I seguenti punteggi del benchmark SPECInt mostrano le prestazioni di calcolo per la linea di VM ad alte prestazioni di Azure con Windows Server. I punteggi di benchmark sul calcolo sono disponibili anche per le [VM Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="a-series---compute-intensive"></a>Serie A - Elevato utilizzo di calcolo
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |10 |236,1 |1.1 |
| Standard_A9 |16 |2 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |10 |450,3 |7.0 |
| Standard_A10 |8 |1 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |5 |235,6 |0.9 |
| Standard_A11 |16 |2 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |7 |454,7 |4.8 |

## <a name="dv2-series"></a>Serie Dv2
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |83 |36,6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |27 |70 |3,7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |130,5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |238,1 |5,2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |14 |460,9 |15,4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |70,1 |3,7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |2 |132 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |17 |235,8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |15 |460,8 |6,5 |

## <a name="g-series-gs-series"></a>Serie G, GS serie
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 a 2 GHz |31 |71,8 |6,5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 a 2 GHz |5 |133,4 |13 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 a 2 GHz |6 |242,3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 a 2 GHz |15 |398,9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 a 2 GHz |22 |762,8 |3,7 |

## <a name="h-series"></a>Serie H
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Velocità di base media  | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |573.2 |2,9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>Informazioni su SPECint
I valori di Windows sono stati calcolati eseguendo [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) in Windows Server. SPECint è stato eseguito utilizzando la velocità di base (SPECint_rate2006), con una copia per core. SPECint consiste in 12 test distinti, ognuno eseguito tre volte prendendo i valori mediani di tutti i test e ponderandoli in modo da ottenere un punteggio composito. Questi test sono stati quindi eseguiti su più VM per ottenere la media dei punteggi illustrata.

## <a name="next-steps"></a>Passaggi successivi
* Per conoscere le capacità di archiviazione, i dettagli sul disco e per considerazioni aggiuntive sulla scelta delle dimensioni delle macchine virtuali, vedere [Dimensioni delle macchine virtuali in Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


