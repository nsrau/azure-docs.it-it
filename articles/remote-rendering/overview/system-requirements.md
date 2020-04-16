---
title: Requisiti di sistema
description: Elenca i requisiti di sistema per il rendering remoto di AzureLists the system requirements for Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411138"
---
# <a name="system-requirements"></a>Requisiti di sistema

> [!IMPORTANT]
> **Il rendering remoto di Azure** è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo capitolo elenca i requisiti minimi di sistema per usare il rendering remoto di *Azure.*

## <a name="development-pc"></a>PC di sviluppo

* Windows 10 versione 1903 o successiva.
* Driver grafici aggiornati.
* Facoltativo: decodificatore video hardware H265, se si desidera utilizzare l'anteprima locale del contenuto sottoposto a rendering remoto (ad esempio in Unity).

> [!IMPORTANT]
> L'aggiornamento di Windows non sempre fornisce i driver GPU più recenti, controlla il sito Web del produttore della GPU per i driver più recenti:
>
> * [Driver AMD](https://www.amd.com/en/support)
> * [Driver Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Driver NVIDIA](https://www.nvidia.com/Download/index.aspx)

La tabella seguente elenca quali GPU supportano la decodifica video hardware H265.

| Produttore GPU | Modelli supportati |
|-----------|:-----------|
| NVIDIA | Controllare la matrice di **supporto NVDEC** [in fondo a questa pagina](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). La GPU ha bisogno di un SI nella colonna **H.265 4:2:0 a 8 bit.** |
| AMD | GPU con almeno la versione 6 di [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)di AMD . |
| Intel | Skylake e nuove CPU |

Anche se è possibile installare il codec H265 corretto, le proprietà di sicurezza nelle DLL dei codec possono causare errori di inizializzazione del codec. La guida alla [risoluzione dei problemi](../resources/troubleshoot.md#h265-codec-not-available) descrive i passaggi per risolvere questo problema. Il problema DLL può verificarsi solo quando si utilizza il servizio in un'applicazione desktop, ad esempio in Unity.

## <a name="devices"></a>Dispositivi

Il rendering remoto di Azure attualmente supporta solo **HoloLens 2** e il desktop di Windows come dispositivo di destinazione. Vedere la sezione [relativa alle limitazioni](../reference/limits.md#platform-limitations) della piattaforma.

È importante usare il codec HEVC più recente, poiché le versioni più recenti presentano miglioramenti significativi nella latenza. Per verificare quale versione è installata sul dispositivo:

1. Avviare **Microsoft Store**.
1. Fai clic sul pulsante **"..."** in alto a destra.
1. Selezionare **Download e aggiornamenti**.
1. Cerca nell'elenco le **estensioni video HEVC di Device Manufacturer**.
1. Assicurarsi che il codec elencato abbia almeno la versione **1.0.21821.0**.
1. Fare clic sul pulsante **Ottieni aggiornamenti** e attendere l'installazione.

## <a name="network"></a>Rete

Una connessione di rete stabile e a bassa latenza è fondamentale per una buona esperienza utente.

Vedere il capitolo dedicato per [i requisiti di rete](../reference/network-requirements.md).

Per la risoluzione dei problemi di rete, fare riferimento alla Guida alla [risoluzione dei problemi](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Software

È necessario installare il seguente software:

* La versione più recente di **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(download)](https://git-scm.com/downloads)
* Facoltativo: per visualizzare il flusso video dal server su un PC desktop, è necessario eseguire le **estensioni video HEVC** [(collegamento a Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

## <a name="unity"></a>Unity

Per lo sviluppo con Unity, installare

* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Installare questi moduli in Unity:
  * **UWP** - Supporto della build di piattaforma Windows universale
  * **IL2CPP** - Supporto di Windows Build (IL2CPP)

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Rendering di un modello con Unity](../quickstarts/render-model.md)
