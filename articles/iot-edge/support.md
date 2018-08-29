---
title: Supporto per la piattaforma di Azure IoT Edge | Microsoft Docs
description: Piattaforme supportate da Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 91821d66ac0be265e6b66fd9eb2378169e337430
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143367"
---
# <a name="azure-iot-edge-support"></a>Supporto di Azure IoT Edge
È possibile cercare il supporto per il prodotto Azure IoT Edge in svariati modi.

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità**: il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina UserVoice](https://feedback.azure.com/forums/907045-azure-iot-edge) del prodotto.

## <a name="operating-systems"></a>Sistemi operativi
Azure IoT Edge viene eseguito nella maggior parte dei sistemi operativi che possono eseguire i contenitori, ma non per tutti è previsto lo stesso supporto. I sistemi operativi sono raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti.

### <a name="tier-1"></a>Livello 1
I sistemi di livello 1 possono essere considerati come ufficialmente supportati. Ciò significa che Microsoft:
* Include questi sistemi operativi in test automatizzati
* Fornisce i pacchetti di installazione

Disponibile a livello generale
| Sistema operativo | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Ubuntu Server 18.04 | Yes | No  |
| Ubuntu Server 16.04 | Yes | No  |
| Raspbian-stretch | No  | Yes|

Anteprima pubblica
| Sistema operativo | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 Server 1803 | Yes | No  |
| Windows 10 IoT Enterprise (aggiornamento di aprile 2018) | Yes | No  |
| Windows 10 IoT Core (aggiornamento di aprile 2018) | Yes | No  |

### <a name="tier-2"></a>Livello 2
I sistemi di livello 2 possono essere considerati compatibili con Azure IoT Edge ed essere usati in modo relativamente semplice. Ciò significa che:
* Microsoft ha eseguito test ad hoc sulle piattaforme o è a conoscenza di un partner che esegue senza problemi Azure IoT Edge nella piattaforma
* I pacchetti di installazione per le altre piattaforme possono funzionare in queste piattaforme

| Sistema operativo | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Ubuntu 18.04 | Yes | No  |
| Ubuntu 16.04 | Yes | No  |
| Wind River 8 | Yes | No  |
| Yocto | Yes | No  |
| Debian | Yes | No  |
| Mac | Yes | No  |

## <a name="container-engines"></a>Motori di contenitore
Azure IoT Edge necessita di un motore di contenitore per avviare i moduli, indipendentemente dal sistema operativo in cui è in esecuzione. Per soddisfare questo requisito, Microsoft fornisce un motore di contenitore, moby-engine, basato sul progetto open source Moby. Docker CE e Docker EE sono altri motori di contenitore comuni, sempre basati sul progetto open source Moby e compatibili con Azure IoT Edge. Microsoft offre il miglior supporto possibile per i sistemi che usano tali motori di contenitore, ma non può inviare le correzioni per eventuali problemi. Per questo motivo, Microsoft consiglia di usare il motore moby nei sistemi di produzione.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
