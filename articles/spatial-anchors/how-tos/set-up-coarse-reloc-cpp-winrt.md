---
title: Come creare e individuare ancoraggi usando sensori su dispositivo in C++/WinRT | Microsoft Docs
description: Descrizione approfondita di come creare e individuare ancoraggi usando sensori su dispositivo in C++/WinRT.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ea4abe8dee22a50fc50e8d58f99f382a135b70b
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093087"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cwinrt"></a>Come creare e individuare ancoraggi usando sensori su dispositivo in C++/WinRT

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Gli ancoraggi nello spazio di Azure possono associare i dati dei sensori di posizionamento su dispositivo agli ancoraggi creati. Questi dati possono anche essere usati per determinare rapidamente se sono presenti ancoraggi vicini al dispositivo. Per altre informazioni, vedere [Rilocalizzazione grossolana](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, accertarsi di avere:

- Conoscenze di base di C# e le <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">API di Windows Runtime</a>.
- Letto la [Panoramica di ancoraggi nello spazio di Azure](../overview.md).
- Completato uno degli argomenti di [avvio rapido di 5 minuti](../index.yml).
- Lette le [procedure per creare e individuare ancoraggi](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the ASA factory
SpatialAnchorsFactory m_asaFactory { nullptr };
// . . .

// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider;
sensorProvider = m_asaFactory.CreatePlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = m_asaFactory.CreateNearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = m_asaFactory.CreateAnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]