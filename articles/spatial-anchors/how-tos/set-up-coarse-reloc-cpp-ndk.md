---
title: Relocalização grosseira em C++/NDK
description: Explicação detalhada de como criar e localizar âncoras usando sensores no dispositivo em C++/NDK.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e505e6a8a3c9f28905c1b1fc3d90dbeee959a793
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277257"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cndk"></a>Como criar e localizar âncoras usando sensores no dispositivo em C++/NDK

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

As Âncoras Espaciais do Azure podem associar dados de sensor de posicionamento no dispositivo com as âncoras que você criar. Esses dados também podem ser usados para determinar rapidamente se há âncoras próximas ao seu dispositivo. Para obter mais informações, confira [Relocalização grosseira](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, verifique se você tem:

- Conhecimento básico sobre o C++ e o <a href="https://developer.android.com/ndk/" target="_blank">Kit de Desenvolvimento Nativo do Android</a>.
- Leia a [Visão geral de Âncoras Espaciais do Azure](../overview.md).
- Concluído um dos [Inícios Rápidos de 5 minutos](../index.yml).
- Leia as [Instruções de criação e localização de âncoras](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Allow GPS
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);

// Allow WiFi scanning
sensors->WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors->BluetoothEnabled(true);
sensors->KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);

// Configure the near-device criteria
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();
nearDeviceCriteria->DistanceInMeters(5.0f);
nearDeviceCriteria->MaxResultCount(25);

// Set the session's locate criteria
auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]