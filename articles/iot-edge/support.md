---
title: Sistemi operativi supportati, motori contenitore - Azure IoT EdgeSupported operating systems, container engines - Azure IoT Edge
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

In questo articolo vengono fornite informazioni dettagliate sui sistemi e i componenti supportati da IoT Edge, sia ufficialmente che in anteprima.

Se si verificano problemi durante l'utilizzo del servizio Azure IoT Edge, esistono diversi modi per richiedere supporto. Prova uno dei seguenti canali per il supporto:

**Segnalazione di bug**: la maggior parte delle attività di sviluppo per il prodotto Azure IoT Edge viene eseguita nel progetto open source di IoT Edge. I bug possono essere segnalati nella [pagina dei problemi](https://github.com/azure/iotedge/issues) del progetto. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

**Team di supporto tecnico Microsoft**: gli utenti con un [piano di supporto](https://azure.microsoft.com/support/plans/) possono rivolgersi al team di supporto tecnico Microsoft creando un ticket di supporto dal [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Richieste di funzionalità:** il prodotto Azure IoT Edge tiene traccia delle richieste di funzionalità tramite la [pagina Voce utente](https://feedback.azure.com/forums/907045-azure-iot-edge)del prodotto.

## <a name="container-engines"></a>Motori di contenitore

I moduli di Azure IoT Edge vengono implementati come contenitori, pertanto IoT Edge necessita di un motore contenitore per avviarli. Per soddisfare questo requisito, Microsoft fornisce un motore di contenitore, moby-engine, Questo motore contenitore è basato sul progetto open source Moby.This container engine is based on the Moby open source project. Docker CE e Docker EE sono altri motori di contenitore comuni, Sono anche basati sul progetto open source Moby e sono compatibili con Azure IoT Edge. Microsoft fornisce il supporto per il massimo sforzo per i sistemi che utilizzano tali motori contenitore; tuttavia, Microsoft non può spedire correzioni per i problemi in essi. Per questo motivo, Microsoft consiglia di usare il motore moby nei sistemi di produzione.

<br>
<center>

![Il motore Moby come runtime del contenitore](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemi operativi

Azure IoT Edge viene eseguito nella maggior parte dei sistemi operativi che possono eseguire contenitori. tuttavia, non tutti questi sistemi sono ugualmente supportati. I sistemi operativi sono raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti.

* Sono supportati i sistemi di livello 1. Per i sistemi di livello 1, Microsoft:For tier 1 systems, Microsoft:
  * Include questo sistema operativo in test automatizzati
  * Fornisce i pacchetti di installazione
* I sistemi di livello 2 sono compatibili con Azure IoT Edge e possono essere usati relativamente facilmente. Per i sistemi di livello 2:
  * Microsoft ha eseguito test informali sulle piattaforme o conosce un partner che esegue correttamente Azure IoT Edge sulla piattaforma
  * I pacchetti di installazione per le altre piattaforme possono funzionare in queste piattaforme

La famiglia del sistema operativo host deve sempre corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. In altre parole, è possibile utilizzare solo i contenitori Linux in Linux e i contenitori Windows in Windows. Quando si usa Windows, sono supportati solo i contenitori isolati di processo, non i contenitori isolati Hyper-V.  

<br>
<center>

![Sistema operativo host corrisponde al sistema operativo guest](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Livello 1

I sistemi elencati nella tabella seguente sono supportati da Microsoft, generalmente disponibili o in anteprima pubblica, e vengono testati con ogni nuova versione. 

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Allungamento Raspbia - ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 - AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 - AMD64](./media/tutorial-c-module/green-check.png) |  | Anteprima pubblica |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Nucleo IoT di Windows - AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise - AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 - AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![IoT di Windows Server 2019 - AMD64](./media/tutorial-c-module/green-check.png) |  |  |

I sistemi operativi Windows elencati in precedenza sono i requisiti per i dispositivi che eseguono contenitori di Windows in Windows, che è l'unica configurazione supportata per l'ambiente di produzione. I pacchetti di installazione di Azure IoT Edge per Windows consentono l'uso di contenitori Linux in Windows; tuttavia, questa configurazione è solo per lo sviluppo e il test. Per ulteriori informazioni, vedere [Usare Edge IoT in Windows per eseguire contenitori Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Livello 2

I sistemi elencati nella tabella seguente sono considerati compatibili con Azure IoT Edge, ma non sono testati o gestiti attivamente da Microsoft.The systems listed in the following table are considered compatible with Azure IoT Edge, but are not activeteteteed or maintained by Microsoft.

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS - AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS - ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS - ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 - AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 - AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 - AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS - AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS - ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS - AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS - ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 - AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 : AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 : AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 - ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Fiume del Vento 8 - AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto - AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto - ARM64](./media/tutorial-c-module/green-check.png) |
| Buster Raspbian <sup>1</sup> |  | ![Buster Raspbian - ARM32v7](./media/tutorial-c-module/green-check.png) | ![Buster Raspbian - ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> I sistemi Debian 10, tra cui Raspian Buster, utilizzano una versione di OpenSSL non supportata da IoT Edge. Utilizzare il comando seguente per installare una versione precedente prima di installare IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Rilasci

Gli asset di rilascio di IoT Edge e le note sulla versione sono disponibili nella pagina delle versioni di [azure-iotedge.IoT](https://github.com/Azure/azure-iotedge/releases) Edge release assets and release notes are available on the azure-iotedge releases page. Questa sezione contiene informazioni contenute in tali note sulla versione che consentono di visualizzare più facilmente i componenti di ogni versione.

I componenti IoT Edge possono essere installati o aggiornati singolarmente e sono compatibili con le versioni precedenti con i componenti delle versioni precedenti. Nella tabella seguente sono elencati i componenti inclusi in ogni versione:

| Versione   | daemon di sicurezza  | Hub perimetrale<br>Agente perimetrale | Libiothismo | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge usa Microsoft.Azure.Device.Client SDK. Per altre informazioni, vedere il repository GitHub di [Azure IoT C'SDK](https://github.com/Azure/azure-iot-sdk-csharp) o il contenuto di riferimento di [Azure SDK per .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) L'elenco seguente mostra la versione dell'SDK client in base alla quale viene testata ogni versione:

* **IoT Edge 1.0.9**: SDK del client 1.21.1
* **IoT Edge 1.0.8**: SDK client 1.20.3
* **IoT Edge 1.0.7**: SDK client 1.20.1
* **IoT Edge 1.0.6**: SDK client 1.17.1
* **IoT Edge 1.0.5**: SDK client 1.17.1

## <a name="virtual-machines"></a>Macchine virtuali

Azure IoT Edge può essere eseguito nelle macchine virtuali. L'uso di una macchina virtuale come dispositivo IoT Edge è comune quando i clienti desiderano aumentare l'infrastruttura esistente con l'intelligenza perimetrale. La famiglia del sistema operativo host della VM deve corrispondere alla famiglia del sistema operativo guest usato all'interno del contenitore di un modulo. Questo requisito è lo stesso di quando Azure IoT Edge viene eseguito direttamente in un dispositivo. Azure IoT Edge è indipendente dalla tecnologia di virtualizzazione sottostante e funziona in macchine virtuali basate su piattaforme, ad esempio Hyper-V e vSphere.

<br>
<center>

![Dispositivo Azure IoT Edge in una VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisiti minimi di sistema

Azure IoT Edge funziona correttamente nei dispositivi con le dimensioni di un dispositivo Raspberry Pi3 per hardware a livello server. La scelta dell'hardware corretto per lo scenario dipende dai carichi di lavoro che si desidera eseguire. Prendere la decisione per il dispositivo finale può essere complicato; tuttavia, è possibile avviare facilmente una soluzione di creazione di prototipi su desktop o portatili tradizionali.

L'esperienza sarà utile per selezione il dispositivo finale durante la creazione di prototipi. Le domande da considerare includono:

* Quanti moduli sono presenti nel carico di lavoro?
* Quanti layer condividono i contenitori dei moduli?
* In quale linguaggio sono scritti i vostri moduli?
* Quanti dati saranno elaborazione i moduli?
* I moduli hanno bisogno di hardware specializzato per accelerare i loro carichi di lavoro?
* Quali sono le caratteristiche di prestazioni desiderate della soluzione?
* Qual è il tuo budget hardware?
