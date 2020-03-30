---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180607"
---
1. [Connettersi all'interfaccia](#connect-to-the-powershell-interface)di PowerShell .
2. Utilizzare `Get-HcsApplianceInfo` il per ottenere le informazioni per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Ecco una tabella che riassume alcune delle informazioni importanti sul dispositivo:
    
    | Parametro                             | Descrizione                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Nome descrittivo del dispositivo configurato tramite l'interfaccia utente Web locale durante la distribuzione del dispositivo. Il nome descrittivo predefinito è il numero di serie del dispositivo.  |   |
    | SerialNumber                   | Il numero di serie del dispositivo è un numero univoco assegnato in fabbrica.                                                                             |   |
    | Modello                          | Modello per il dispositivo Data Box Edge o Data Box Gateway. Il modello è virtuale per Data Box Gateway e fisico per Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Stringa descrittiva che corrisponde alla versione del software del dispositivo. Per un sistema che esegue l'anteprima, la versione software amichevole sarebbe Data Box Edge 1902. |   |
    | HcsVersion                     | Versione del software HCS in esecuzione nel dispositivo. Ad esempio, la versione del software HCS corrispondente a Data Box Edge 1902 è 1.4.771.324.            |   |
    | LocalCapacityInMb              | La capacità locale totale del dispositivo in Megabits.                                                                                                        |   |
    | IsRegistered (Registrato)                   | Questo valore indica se il dispositivo è attivato con il servizio.                                                                                         |   |


