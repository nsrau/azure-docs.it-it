---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161139"
---
1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Get-HcsApplianceInfo` per ottenere le informazioni per il dispositivo.

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

    Ecco una tabella che riepiloga alcune delle informazioni sul dispositivo importanti:
    
    | Parametro                             | Descrizione                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Il nome descrittivo del dispositivo configurato tramite l'interfaccia utente web locale durante la distribuzione del dispositivo. Il nome descrittivo predefinito è il numero di serie del dispositivo.  |   |
    | SerialNumber                   | Il numero di serie è un numero univoco assegnato in fase di produzione.                                                                             |   |
    | Modello                          | Il modello per dispositivo Edge di finestra di dati o Gateway di finestra di dati. Il modello è virtuale per il Gateway di finestra di dati e fisiche per Edge di finestra di dati.                   |   |
    | FriendlySoftwareVersion        | Stringa descrittiva che corrisponde alla versione del software del dispositivo. Per un sistema che esegue preview, la versione del software descrittivo sarebbe 1902 Edge finestra di dati. |   |
    | HcsVersion                     | Versione del software HCS in esecuzione nel dispositivo. Ad esempio, la versione del software HCS corrispondente a una Data finestra Edge 1902 è 1.4.771.324.            |   |
    | LocalCapacityInMb              | La capacità totale locale del dispositivo in megabit.                                                                                                        |   |
    | IsRegistered                   | Questo valore indica se il dispositivo viene attivato con il servizio.                                                                                         |   |


