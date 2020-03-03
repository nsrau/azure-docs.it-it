---
title: Specifiche hardware di Azure Kinect DK
description: Informazioni su componenti, specifiche e funzionalità di Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specifiche, hardware, DK, funzionalità, profondità, colore, RGB, IMU, microfono, array, profondità
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: e0d42a3ce1dd9deb5e73500371c367134ca852e1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619954"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Specifiche hardware di Azure Kinect DK

Questo articolo contiene informazioni sull'integrazione dell'hardware di Azure Kinect e la tecnologia dei sensori più recente di Microsoft in un singolo accessorio connesso tramite USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Termini

Nell'articolo vengono usate le abbreviazioni seguenti.

- NFOV (Narrow field-of-view depth mode, modalità di profondità campo visivo stretto)
- WFOV (Wide field-of-view depth mode, modalità di profondità campo visivo largo)
- FOV (Field-of-view, campo visivo)
- FPS (Frames-per-second, fotogrammi al secondo)
- IMU (Inertial Measurement Unit, unità di misura inerziale)
- FoI (Field of Interest, campo di interesse)

## <a name="product-dimensions-and-weight"></a>Dimensioni e peso del prodotto

Ecco le dimensioni e il peso del dispositivo Azure Kinect.

- **Dimensioni**: 103 x 39 x 126 mm
- **Peso**: 440 g

![Dimensioni di Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Ambiente operativo

Azure Kinect DK è destinato a sviluppatori e attività commerciali che operano nelle condizioni ambientali seguenti:

- **Temperatura**: 10-25⁰C
- **Umidità**: 8-90% di umidità relativa (senza condensa)

> [!NOTE]
> L'uso del dispositivo in condizioni ambientali diverse potrebbe causare malfunzionamenti e/o errori. Queste condizioni sono applicabili all'ambiente immediatamente circostante il dispositivo in tutte le condizioni operative. Per l'uso con enclosure esterne, è consigliabile adottare soluzioni per il controllo della temperatura attiva e/o altre soluzioni di raffreddamento, per assicurarsi che il dispositivo venga mantenuto all'interno di questi intervalli. L'architettura del dispositivo prevede un canale di raffreddamento tra la sezione anteriore e il manicotto posteriore. Quando si implementa il dispositivo, assicurarsi che tale canale di raffreddamento non sia ostruito.

Vedere altre [informazioni sulla sicurezza](https://support.microsoft.com/help/4023454/safety-information) del prodotto.

## <a name="depth-camera-supported-operating-modes"></a>Modalità operative supportate dalla fotocamera di profondità

Azure Kinect DK integra una fotocamera di profondità ToF (Time-of-Flight) da 1 Megapixel progettata da Microsoft con il [sensore di immagini presentato alI'SSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). La fotocamera di profondità supporta le modalità indicate di seguito:

 | Mode            | Risoluzione | FoI       | FPS                | Intervallo operativo* | Tempo di esposizione |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV senza binning   | 640x576    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 3,86 m       | 12,8 ms        |
| NFOV 2x2 con binning (SW) | 320x288    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 5,46 m       | 12,8 ms        |
| WFOV 2x2 con binning | 512x512    | 120°x120° | 0, 5, 15, 30       | 0,25 - 2,88 m      | 12,8 ms        |
| WFOV senza binning   | 1024x1024  | 120°x120° | 0, 5, 15           | 0,25 - 2,21 m      | 20,3 ms        |
| IR passivo      | 1024x1024  | N/D       | 0, 5, 15, 30       | N/D              | 1,6 ms         |

\*Riflettanza dal 15% al 95% a 850 nm, 2,2 μW/cm<sup>2</sup>/nm, deviazione standard errori casuali ≤ 17 mm, errore sistematico tipico < 11 mm + 0,1% della distanza senza interferenza multi-percorso. La profondità può essere specificata al di fuori dell'intervallo operativo indicato in precedenza. Dipende dalla riflettività di un oggetto.

## <a name="color-camera-supported-operating-modes"></a>Modalità operative supportate dalla fotocamera a colori

Azure Kinect DK include un sensore CMOS di 12 MP OV12A10 rolling shutter. Le modalità operative native sono indicate di seguito:

|             Risoluzione fotocamera RGB (OxV)  |          Proporzioni  |          Opzioni formato   |          Frequenza dei fotogrammi (FPS)  |          FOV nominale (OxV)(post-elaborazione)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90°x74.3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90°x74.3°                            |

La fotocamera RGB è compatibile a livello di classe con video USB e può essere usata senza Sensor SDK. Sazio colore della fotocamera RGB: gamma completa BT.601 [0..255]. 

> [!NOTE]
> Sensor SDK può fornire immagini a colori in formato pixel BGRA. Non è una modalità nativa supportata dal dispositivo e se usata genera un carico aggiuntivo sulla CPU. La CPU host viene usata per la conversione dalle immagini MJPEG ricevute dal dispositivo.

## <a name="rgb-camera-exposure-time-values"></a>Valori del tempo di esposizione della fotocamera RGB

Di seguito è riportato il mapping dei valori di esposizione manuale accettabili per la fotocamera RGB:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Intervalli di base del sensore di profondità

Modalità di profondità | IR <br>IR | Durata <br>Larghezza  | Idle <br>inattività| Tempo inattività | Tempo <br> Tempo
-|-|-|-|-|-
NFOV senza binning <br>  NFOV 2xx con binning <br> WFOV 2x2 con binning | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV senza binning                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Campo visivo della fotocamera

L'immagine seguente mostra la profondità del campo visivo della fotocamera RGB, ovvero le angolazioni che i sensori "vedono". Questo diagramma mostra la fotocamera RGB in modalità 4:3.

![FOV della fotocamera](./media/resources/hardware-specs-media/camera-fov.png)

Questa immagine mostra il campo visivo della fotocamera visto di fronte a una distanza di 2000 mm.

![FOV anteriore della fotocamera](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Quando la profondità è in modalità NFOV, la fotocamera RGP presenta una sovrapposizione dei pixel migliore nelle risoluzioni a 4:3 rispetto a 16:9 .

## <a name="motion-sensor-imu"></a>Sensore di movimento (IMU)

L'unità di misurazione inerziale (IMU) incorporata è LSM6DSMUS e include sia un accelerometro che un giroscopio. L'accelerometro e il giroscopio vengono campionati simultaneamente a 1,6 kHz. I campioni vengono riportati all'host a 208 Hz.

## <a name="microphone-array"></a>Array di microfoni

Azure Kinect DK incorpora un array circolare di sette microfoni di alta qualità che viene identificato come dispositivo audio USB di classe 2.0. Tutti e 7 i canali sono accessibili. Le specifiche di prestazioni sono:

- Sensibilità: -22 dBFS (94 dB SPL, 1 kHz)
- Rapporto segnale/rumore > 65 dB
- Punto di sovraccarico acustico: 116 dB

![Bolla microfono](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK è un dispositivo composito USB3 che espone gli endpoint hardware seguenti al sistema operativo:

L'ID fornitore è 0x045E (Microsoft), tabella di ID prodotto di seguito:

|    Interfaccia USB        |    PNP IP    |     Note            |
|-------------------------|--------------|----------------------|
|    Hub USB3.1 Gen1    |    0x097A    |    Hub principale    |
|    Hub USB2.0         |    0x097B    |    USB HS          |
|    Fotocamera di profondità       |    0x097C    |    USB 3.0            |
|    Fotocamera a colori       |    0x097D    |    USB 3.0            |
|    Microfoni        |    0x097E    |    USB HS          |

## <a name="indicators"></a>Indicatori

Il dispositivo include un indicatore di streaming della fotocamera nella parte anteriore che è possibile disabilitare a livello di codice tramite Sensor SDK.

Il LED di stato dietro il dispositivo indica lo stato:

| Se la luce è     | Significa                                                   |
|-----------------------|------------------------------------------------------------|
| Bianca continua           | Il dispositivo è acceso e funziona correttamente.                         |
| Bianca lampeggiante        | Il dispositivo è acceso ma non ha una connessione dati USB 3.0.   |
| Gialla lampeggiante        | L'alimentazione del dispositivo non è sufficiente per il funzionamento.               |
| Gialla, quindi bianca lampeggiante  | Aggiornamento o ripristino del firmware in corso                    |

## <a name="power-device"></a>Alimentazione del dispositivo

Il dispositivo può essere alimentato in due modi:

1. Tramite l'alimentatore incluso. I dati vengono connessi tramite un cavo USB da tipo C a tipo A separato.
2. Tramite un cavo da tipo C a tipo C per alimentazione e dati.

Il cavo da tipo C a tipo C non è incluso nel dispositivo Azure Kinect DK.

> [!NOTE]
> - Il cavo dell'alimentatore incluso è USB da tipo A a un singolo connettore a cilindro posteriore. Usare l'alimentatore a rete fornito con questo cavo. Il dispositivo è in grado di assorbire una potenza maggiore di quella che possono fornire due porte USB standard di tipo A.
> - I cavi USB sono importante, quindi è consigliabile usare cavi di alta qualità e di verificarne in funzionamento prima di distribuire l'unità in remoto.

> [!TIP]
> Per selezionare un cavo da tipo C a tipo C valido:
> - Il [cavo certificato USB](https://www.usb.org/products) deve supportare sia l'alimentazione che i dati.
> - Un cavo passivo deve essere di lunghezza inferiore a 1,5 m. Per lunghezze maggiori, usare un cavo attivo. 
> - Il cavo deve supportare almeno >1,5 A. In caso contrario, è necessario connettere un alimentatore esterno.

Verificare il cavo:

- Connettere il dispositivo al PC host tramite il cavo.
- Verificare che tutti i dispositivi vengano enumerati correttamente in Gestione dispositivi di Windows. Le fotocamere di profondità e RGB dovrebbero comparire come illustrato nell'esempio seguente.

  ![Azure Kinect DK in Gestione dispositivi](./media/resources/hardware-specs-media/device-manager.png)

- Verificare che il cavo sia in grado di trasmettere in modo affidabile in tutti i sensori nel visualizzatore di Azure Kinect, con le impostazioni seguenti:

  - Fotocamera di profondità: NFOV senza binning
  - Fotocamera RBG: 2160p
  - Microfoni e dispositivi abilitati IMU

## <a name="what-does-the-light-mean"></a>Cosa significa la luce?

L'indicatore di alimentazione è un LED posto sul retro del dispositivo Azure Kinect DK. Il colore del LED cambia in base allo stato del dispositivo.

![L'immagine mostra il retro di Azure Kinect DK. Sono presenti tre callout numerati, uno per l'indicatore LED e, al di sotto, due per i cavi.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Le etichette della figura fanno riferimento ai componenti seguenti:

1. Indicatore di alimentazione
1. Cavo di alimentazione, connesso alla fonte di energia
1. Cavo dati USB-C, connesso al PC

Assicurarsi che i cavi siano connessi come illustrato. Quindi consultare la tabella seguente per informazioni sui vari stati indicati dal LED di alimentazione.

|Se la luce è: |Significa che: |Ed è necessario |
| ---| --- | --- |
|Bianca continua |Il dispositivo è alimentato e funziona correttamente. |Usare il dispositivo. |
|Spenta |Il dispositivo non è connesso al PC. |Assicurarsi che il cavo di alimentazione con il connettore cilindrico sia connesso al dispositivo e all'alimentatore USB.<br /><br />Assicurarsi che il cavo USB-C sia connesso al dispositivo e al PC. |
|Bianca lampeggiante |Il dispositivo è alimentato ma non ha una connessione dati USB 3.0. |Assicurarsi che il cavo di alimentazione con il connettore cilindrico sia connesso al dispositivo e all'alimentatore USB.<br /><br />Assicurarsi che il cavo USB-C sia connesso al dispositivo e a una porta USB 3.0 del PC.<br /><br />Connettere il dispositivo a una porta USB 3.0 diversa del PC.<br /><br />Nel PC aprire Gestione dispositivi (**Start** > **Pannello di controllo** > **Gestione dispositivi**) e verificare che il PC sia dotato di un controller host USB 3.0 supportato. |
|Gialla lampeggiante |L'alimentazione del dispositivo non è sufficiente per il funzionamento. |Assicurarsi che il cavo di alimentazione con il connettore cilindrico sia connesso al dispositivo e all'alimentatore USB.<br /><br />Assicurarsi che il cavo USB-C sia connesso al dispositivo e al PC. |
|Gialla, quindi bianca lampeggiante |Il dispositivo è alimentato ed è in corso la ricezione di un aggiornamento firmware oppure un ripristino delle impostazioni di fabbrica. |Attendere che la luce dell'indicatore di alimentazione diventi bianca continua. Per altre informazioni, vedere [Reimpostare Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Consumo di energia

Azure Kinect DK consuma fino a 5,9 W; il consumo di energia specifico varia a seconda del caso d'uso.

## <a name="calibration"></a>Calibrazione

Il dispositivo Azure Kinect DK viene calibrato in fabbrica. I parametri di calibrazione per i sensori visivo e inerziale possono essere sottoposti a query a livello di codice tramite Sensor SDK.

## <a name="device-recovery"></a>Ripristino del dispositivo

Il firmware del dispositivo può essere ripristinato sui valori originali usando il pulsante al di sotto del pin di blocco.

![Pulsante per il ripristino di Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Per ripristinare il dispositivo, vedere [queste istruzioni](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Passaggi successivi

- [Usare Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Configurare l'hardware](set-up-azure-kinect-dk.md)
