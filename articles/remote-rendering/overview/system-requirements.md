---
title: Requisiti di sistema
description: Elenca i requisiti di sistema per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 81480bea735017d3fc59e9c6cf126c2146a0c968
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798466"
---
# <a name="system-requirements"></a>Requisiti di sistema

> [!IMPORTANT]
> **Rendering remoto di Azure** è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo capitolo elenca i requisiti di sistema minimi per l'uso di *Azure Remote rendering* (arr).

## <a name="development-pc"></a>COMPUTER di sviluppo

* Windows 10 versione 1903 o successiva.
* Driver grafici aggiornati.
* Facoltativo: [decodificatore video hardware H265](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)se si vuole usare l'anteprima locale del contenuto sottoposto a rendering remoto, ad esempio in Unity.

> [!IMPORTANT]
> Windows Update non fornisce sempre i driver GPU più recenti, controllare il sito Web del produttore della GPU per i driver più recenti:
>
> * [Driver AMD](https://www.amd.com/en/support)
> * [Driver Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Driver NVIDIA](https://www.nvidia.com/Download/index.aspx)

La tabella seguente elenca le GPU che supportano la decodifica video hardware H265.

| Produttore GPU | Modelli supportati |
|-----------|:-----------|
| NVIDIA | Controllare la **matrice di supporto NVDEC** [nella parte inferiore della pagina](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Per la GPU è necessario un Sì nella colonna **H. 265 a 4:2:0 8 bit** . |
| AMD | GPU con almeno la versione 6 del [decodificatore video unificato](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)di AMD. |
| Intel | Skylake e CPU più recenti |

Anche se è possibile che venga installato il codec H265 corretto, le proprietà di sicurezza nelle DLL dei codec possono causare errori di inizializzazione del codec. Nella [Guida alla risoluzione dei problemi](../resources/troubleshoot.md#h265-codec-not-available) vengono descritti i passaggi per risolvere il problema. Il problema della DLL può verificarsi solo quando si usa il servizio in un'applicazione desktop, ad esempio in Unity.

## <a name="devices"></a>Dispositivi

Il rendering remoto di Azure attualmente supporta solo **HoloLens 2** e Windows desktop come dispositivo di destinazione. Vedere la sezione [limitazioni della piattaforma](../reference/limits.md#platform-limitations) .

È importante usare il codec HEVC più recente, in quanto le versioni più recenti presentano miglioramenti significativi della latenza. Per verificare quale versione è installata nel dispositivo:

1. Avviare il **Microsoft Store**.
1. Fare clic sul pulsante **"..."** in alto a destra.
1. Selezionare **download e aggiornamenti**.
1. Cercare nell'elenco le **estensioni video di HEVC dal produttore del dispositivo**. Se questo elemento non è elencato in aggiornamenti, la versione più recente è già installata.
1. Verificare che il codec elencato includa almeno la versione **1.0.21821.0**.
1. Fai clic sul pulsante **Ottieni aggiornamenti** e attendi l'installazione.

## <a name="network"></a>Rete

Una connessione di rete stabile e a bassa latenza è essenziale per un'esperienza utente ottimale.

Vedere il capitolo dedicato per i [requisiti di rete](../reference/network-requirements.md).

Per la risoluzione dei problemi di rete, vedere la [Guida alla risoluzione dei](../resources/troubleshoot.md#unstable-holograms)problemi.

## <a name="software"></a>Software

È necessario installare il software seguente:

* La versione più recente di **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools per Realtà mista](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). In particolare, le installazioni del *carico di lavoro* seguente sono obbligatorie:
  * **Sviluppo per desktop con C++**
  * **Sviluppo di app UWP (Universal Windows Platform)**
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(download)](https://git-scm.com/downloads)
* Facoltativo: per visualizzare il flusso video dal server su un PC desktop, è necessario **HEVC video Extensions** [(collegamento Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Verificare che la versione più recente sia installata controllando la disponibilità di aggiornamenti nell'archivio.

## <a name="unity"></a>Unity

Per lo sviluppo con Unity, installare

* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Installare questi moduli in Unity:
  * **UWP** - Supporto per la compilazione della piattaforma UWP (Universal Windows Platform)
  * **IL2CPP** - Supporto per la compilazione di Windows (IL2CPP)

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
