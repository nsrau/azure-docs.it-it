---
title: Calcolare i punteggi di benchmark per le VM Linux | Microsoft Docs
description: Confrontare i punteggi di benchmark di CoreMark calcolo per le VM di Azure che eseguono Linux
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 3c7b34652ea4c9340b9fe7f6ada3f9992642aeac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Calcolare i punteggi di benchmark per le VM Linux
I punteggi del benchmark CoreMark riportati di seguito mostrano le prestazioni di calcolo per la linea di VM ad alte prestazioni di Azure con Ubuntu. I punteggi di benchmark sul calcolo sono disponibili anche per le [VM Windows](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="a-series---compute-intensive"></a>Serie A - Elevato utilizzo di calcolo
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |179 |110.294 |554 |
| Standard_A9 |16 |2 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |189 |210.816 |2.126 |
| Standard_A10 |8 |1 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |188 |110.025 |1.045 |
| Standard_A11 |16 |2 |CPU Intel Xeon E5-2670 0 a 2,6 GHz |188 |210.727 |2.073 |

## <a name="dv2-series"></a>Serie Dv2
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |140 |14.852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |133 |29.467 |1.863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |139 |56.205 |1.167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |126 |108.543 |3.446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |126 |205.332 |9.998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |125 |28.598 |1.510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |131 |55.673 |1.418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |140 |107.986 |3.089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |140 |208.186 |8.839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |28 |268.560 |4.667 |

## <a name="f-series"></a>Serie F
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |154 |15.602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |126 |29.519 |1.233 |
| Standard_F4 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |147 |58.709 |1.227 |
| Standard_F8 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |224 |112.772 |3.006 |
| Standard_F16 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |42 |218.571 |5.113 |

## <a name="g-series"></a>Serie G
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 a 2 GHz |83 |31.310 |2.891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 a 2 GHz |84 |60.112 |3.537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 a 2 GHz |84 |107.522 |4.537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 a 2 GHz |83 |195.116 |5.024 |
| Standard_G5 |32 |2 |Intel Xeon E5-2698B v3 a 2 GHz |84 |360.329 |14.212 |

## <a name="gs-series"></a>Serie GS
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 a 2 GHz |84 |28.613 |1.884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 a 2 GHz |83 |54.348 |3.474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 a 2 GHz |83 |104.564 |1.834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 a 2 GHz |84 |194.111 |4.735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 a 2 GHz |84 |357.396 |16.228 |

## <a name="h-series"></a>Serie H
| Dimensione | vCPU | Nodi NUMA | CPU | Esecuzioni | Iterazioni/sec | Deviazione standard |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |28 |140.782 |2512 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |35 |275.289 |7110 |
| Standard_H18m |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |28 |139.071 |3988 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |28 |275.988 |6963 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |28 |273.982 |6069 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |28 |274.523 |5698 |

## <a name="about-coremark"></a>Informazioni su CoreMark
I numeri di Linux sono stati calcolati eseguendo [CoreMark](http://www.eembc.org/coremark/faq.php) su Ubuntu. CoreMark è stato configurato con il numero di thread impostato sul numero di CPU virtuali e la concorrenza impostata su PThreads. Il numero di iterazioni è stato rettificato in base alle prestazioni previste per fornire un runtime di almeno 20 secondi (ma in genere molto di più). Il punteggio finale rappresentava il numero di iterazioni completate, diviso per la durata del test espressa in secondi. Ogni test è stato eseguito almeno sette volte per ogni VM. I test (ad eccezione di quelli della serie H) sono stati eseguiti nell'ottobre 2015 su più VM in ogni area pubblica di Azure in cui erano supportate le macchine virtuali alla data di esecuzione.

## <a name="next-steps"></a>Passaggi successivi
* Per conoscere le capacità di archiviazione, i dettagli sul disco e per considerazioni aggiuntive sulla scelta delle dimensioni delle macchine virtuali, vedere [Dimensioni delle macchine virtuali in Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per eseguire gli script CoreMark nelle VM Linux, scaricare il [pacchetto di script CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

