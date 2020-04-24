---
title: Sistemi operativi supportati, motori di contenitori-Azure IoT Edge
description: Informazioni sui sistemi operativi in grado di eseguire il daemon e il runtime di Azure IoT Edge, oltre che sui motori di contenitore supportati per i dispositivi di produzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536939"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemi supportati da Azure IoT Edge

Questo articolo fornisce informazioni dettagliate sui sistemi e sui componenti supportati da IoT Edge, sia ufficialmente che in anteprima.

Se si verificano problemi durante l'utilizzo del servizio Azure IoT Edge, è possibile cercare supporto in diversi modi. Per il supporto, provare uno dei canali seguenti:

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità** : il prodotto Azure IOT Edge tiene traccia delle richieste di funzionalità tramite la [pagina vocale dell'utente](https://feedback.azure.com/forums/907045-azure-iot-edge)del prodotto.

## <a name="container-engines"></a>Motori di contenitore

Azure IoT Edge moduli sono implementati come contenitori, quindi IoT Edge necessita di un motore di contenitori per avviarli. Per soddisfare questo requisito, Microsoft fornisce un motore di contenitore, moby-engine, Questo motore di contenitori è basato sul progetto open source di Moby. Docker CE e Docker EE sono altri motori di contenitore comuni, Sono basate anche sul progetto Moby open source e sono compatibili con Azure IoT Edge. Microsoft offre supporto ottimale per i sistemi che usano i motori di contenitori; Tuttavia, Microsoft non è in grado di fornire correzioni per i problemi. Per questo motivo, Microsoft consiglia di usare il motore moby nei sistemi di produzione.

<br>
<center>

![Motore Moby come runtime del contenitore](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemi operativi

Azure IoT Edge viene eseguito nella maggior parte dei sistemi operativi che possono eseguire contenitori; Tuttavia, non tutti questi sistemi sono ugualmente supportati. I sistemi operativi sono raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti.

* Sono supportati i sistemi di livello 1. Per i sistemi di livello 1, Microsoft:
  * Include questo sistema operativo in test automatizzati
  * Fornisce i pacchetti di installazione
* I sistemi di livello 2 sono compatibili con Azure IoT Edge e possono essere usati in modo relativamente semplice. Per i sistemi di livello 2:
  * Microsoft ha eseguito un test informale sulle piattaforme o è a conoscenza di un partner che esegue correttamente Azure IoT Edge sulla piattaforma
  * I pacchetti di installazione per le altre piattaforme possono funzionare in queste piattaforme

La famiglia del sistema operativo host deve sempre corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. In altre parole, è possibile utilizzare solo i contenitori Linux in Linux e i contenitori Windows in Windows. Quando si usa Windows, sono supportati solo i contenitori isolati di processo, non i contenitori isolati di Hyper-V.  

<br>
<center>

![Sistema operativo host corrisponde al sistema operativo guest](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Livello 1

I sistemi elencati nella tabella seguente sono supportati da Microsoft, disponibile a livello generale o in anteprima pubblica, e sono testati a ogni nuova versione. 

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Server Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |
| [Windows 10 Internet Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), Build 17763 | ![Windows Internet core e AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)Internet Internet (Enterprise), Build 17763 | ![Windows 10 Internet Internet e AMD64 Enterprise](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows/iot-core/windows-server), Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

I sistemi operativi Windows elencati in precedenza sono i requisiti per i dispositivi che eseguono i contenitori di Windows in Windows, che è l'unica configurazione supportata per la produzione. I pacchetti di installazione di Azure IoT Edge per Windows consentono l'uso di contenitori Linux in Windows; Questa configurazione è tuttavia solo per lo sviluppo e il test. Per altre informazioni, vedere [usare IOT Edge in Windows per eseguire i contenitori Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Livello 2

I sistemi elencati nella tabella seguente sono considerati compatibili con Azure IoT Edge, ma non sono attivamente testati o gestiti da Microsoft.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Sistema operativo Linux Flex per Mentor Embedded](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Sistema operativo Linux Flex per Mentor Embedded](./media/tutorial-c-module/green-check.png) | ![Sistema operativo Linux Flex per Mentor Embedded + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Sistema operativo Linux Flex per Mentor Embedded + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> i sistemi Debian 10, tra cui Raspian Buster, usano una versione di OpenSSL che IoT Edge non supporta. Usare il comando seguente per installare una versione precedente prima di installare IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Rilasci

Le risorse e le note sulla versione di IoT Edge sono disponibili nella pagina delle [versioni di Azure-iotedge](https://github.com/Azure/azure-iotedge/releases) . In questa sezione vengono illustrate le informazioni di queste note sulla versione che consentono di visualizzare più facilmente i componenti di ciascuna versione.

I componenti di IoT Edge possono essere installati o aggiornati singolarmente e sono compatibili con i componenti di versioni precedenti. La tabella seguente elenca i componenti inclusi in ogni versione:

| Versione   | Daemon di sicurezza  | Hub Edge<br>Agente Edge | Libiothsm | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge USA Microsoft. Azure. Device. Client SDK. Per altre informazioni, vedere il [repository GitHub di Azure per C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) o il [contenuto di riferimento di Azure SDK per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet). Nell'elenco seguente viene illustrata la versione dell'SDK client su cui viene eseguita la verifica di ogni versione:

* **IOT Edge 1.0.9**: SDK client 1.21.1
* **IOT Edge 1.0.8**: SDK client 1.20.3
* **IOT Edge 1.0.7**: SDK client 1.20.1
* **IOT Edge 1.0.6**: SDK client 1.17.1
* **IOT Edge 1.0.5**: SDK client 1.17.1

## <a name="virtual-machines"></a>Macchine virtuali

Azure IoT Edge può essere eseguito nelle macchine virtuali. L'uso di una macchina virtuale come dispositivo IoT Edge è comune quando i clienti vogliono aumentare l'infrastruttura esistente con l'intelligence perimetrale. La famiglia del sistema operativo host della VM deve corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. Questo requisito è identico a quando Azure IoT Edge viene eseguito direttamente in un dispositivo. Azure IoT Edge è indipendente dalla tecnologia di virtualizzazione sottostante e funziona in macchine virtuali basate su piattaforme, ad esempio Hyper-V e vSphere.

<br>
<center>

![Dispositivo Azure IoT Edge in una VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisiti minimi di sistema

Azure IoT Edge funziona correttamente nei dispositivi con le dimensioni di un dispositivo Raspberry Pi3 per hardware a livello server. La scelta dell'hardware appropriato per lo scenario dipende dai carichi di lavoro che si desidera eseguire. Prendere la decisione per il dispositivo finale può essere complicato; tuttavia, è possibile avviare facilmente una soluzione di creazione di prototipi su desktop o portatili tradizionali.

L'esperienza sarà utile per selezione il dispositivo finale durante la creazione di prototipi. Di seguito sono riportate alcune domande da considerare:

* Quanti moduli sono presenti nel carico di lavoro?
* Il numero di livelli che i contenitori dei moduli condividono?
* In quale lingua vengono scritti i moduli?
* Quanti dati verranno elaborati dai moduli?
* I moduli necessitano di hardware specializzato per accelerare i carichi di lavoro?
* Quali sono le caratteristiche di prestazioni desiderate della soluzione?
* Qual è il tuo budget hardware?
