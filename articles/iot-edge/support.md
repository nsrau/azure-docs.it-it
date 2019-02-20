---
title: Sistemi operativi e motori di contenitore supportati - Azure IoT Edge | Microsoft Docs
description: Informazioni sui sistemi operativi in grado di eseguire il daemon e il runtime di Azure IoT Edge, oltre che sui motori di contenitore supportati per i dispositivi di produzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892797"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemi supportati da Azure IoT Edge

È possibile cercare il supporto per il prodotto Azure IoT Edge in svariati modi.

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità**: il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina UserVoice](https://feedback.azure.com/forums/907045-azure-iot-edge) del prodotto.

## <a name="container-engines"></a>Motori di contenitore
Azure IoT Edge necessita di un motore del contenitore per avviare i moduli, poiché vengono implementati come contenitori. Per soddisfare questo requisito, Microsoft fornisce un motore di contenitore, moby-engine, basato sul progetto open source Moby. Docker CE e Docker EE sono altri motori di contenitore comuni, Sono sempre basati sul progetto open source Moby e compatibili con Azure IoT Edge. Microsoft offre il miglior supporto possibile per i sistemi che usano tali motori di contenitore, ma non può inviare le correzioni per eventuali problemi. Per questo motivo, Microsoft consiglia di usare il motore moby nei sistemi di produzione.

<br>
<center>
![Moby come runtime dei contenitori](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemi operativi
Azure IoT Edge viene eseguito nella maggior parte dei sistemi operativi che possono eseguire i contenitori, ma non per tutti è previsto lo stesso supporto. I sistemi operativi sono raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti.
* I sistemi di livello 1 possono essere considerati come ufficialmente supportati. Ciò significa che Microsoft:
    * Include questo sistema operativo in test automatizzati
    * Fornisce i pacchetti di installazione
* I sistemi di livello 2 possono essere considerati compatibili con Azure IoT Edge ed essere usati in modo relativamente semplice. Ciò significa che:
    * Microsoft ha eseguito test ad hoc sulle piattaforme o è a conoscenza di un partner che esegue senza problemi Azure IoT Edge nella piattaforma
    * I pacchetti di installazione per le altre piattaforme possono funzionare in queste piattaforme
    
La famiglia del sistema operativo host deve sempre corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. In altre parole, è possibile utilizzare solo i contenitori Linux in Linux e i contenitori Windows in Windows. Quando si usa Windows, solo i contenitori di processo isolato sono supportati, non i contenitori isolati hyper-v.  

<br>
<center>
![Sistema operativo host corrisponde al sistema operativo guest](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Livello 1
Disponibile a livello generale
| Sistema operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | No  | Sì|
| Ubuntu Server 16.04 | Sì | No  |
| Ubuntu Server 18.04 | Sì | No  |

Anteprima pubblica
| Sistema operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core compilazione 17763 | Sì | No  |
| Windows 10 IoT Enterprise build 17763 | Sì | No  |
| Windows Server 2019 | Sì | No  |

I sistemi operativi Windows precedenti sono i requisiti per i dispositivi che eseguono i contenitori Windows in Windows. Si tratta dell'unica configurazione supportata per la produzione. I pacchetti di installazione Azure IoT Edge per Windows consentono l'uso di contenitori Linux in Windows; tuttavia, questo è solo per sviluppo e test. Uso di contenitori Linux in Windows non è una configurazione supportata per la produzione. Qualsiasi versione di Windows 10 build 14393 o versioni successive e Windows Server 2016 o versioni successive sono utilizzabili per questo scenario di sviluppo.

### <a name="tier-2"></a>Livello 2

| Sistema operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Sì | Sì |
| Debian 8 | Sì | Sì |
| Debian 9 | Sì | Sì |
| RHEL 7.5 | Sì | Sì |
| Ubuntu 18.04 | Sì | Sì |
| Ubuntu 16.04 | Sì | Sì |
| Wind River 8 | Sì | No  |
| Yocto | Sì | No  |


## <a name="virtual-machines"></a>Macchine virtuali
Azure IoT Edge può essere eseguito nelle macchine virtuali. Ciò è comune quando i clienti vogliono ampliare l'infrastruttura esistente con intelligence ad arco. La famiglia del sistema operativo host della VM deve corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. Questo è il medesimo requisito quando Azure IoT Edge viene eseguito direttamente in un dispositivo. Azure IoT Edge è indipendente dalla tecnologia di virtualizzazione sottostante e funziona in macchine virtuali basate su piattaforme, ad esempio Hyper-V e vSphere.

<br>
<center>
![Dispositivo Azure IoT Edge in una VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisiti minimi di sistema
Azure IoT Edge funziona correttamente nei dispositivi con le dimensioni di un dispositivo Raspberry Pi3 per hardware a livello server. Scegliere l'hardware appropriato per il proprio scenario dipende molto dai carichi di lavoro che si desidera eseguire. Prendere la decisione per il dispositivo finale può essere complicato; tuttavia, è possibile avviare facilmente una soluzione di creazione di prototipi su desktop o portatili tradizionali.

L'esperienza sarà utile per selezione il dispositivo finale durante la creazione di prototipi. È necessario considerare i seguenti fattori: quanti moduli costituiscono un carico di lavoro, quanti livelli condividono i contenitori dei moduli, in quale lingua sono scritti i moduli, quanti dati elaboreranno i moduli, se i moduli hanno bisogno di hardware specializzati per accelerare i carichi di lavoro, quali sono le caratteristiche di prestazioni desiderate della soluzione e qual è il budget per l'hardware?
