---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593975"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Scaricare e installare il file binario del client Istio Istio Istioctl

In una shell basata su `Invoke-WebRequest` PowerShell in Windows, utilizzare per `Expand-Archive` scaricare la versione Istio e quindi estrarre con come segue:In a PowerShell-based shell on Windows, use to download the Istio release and then extract with as follows:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Il `istioctl` file binario client viene eseguito nel computer client e consente di interagire con la rete mesh del servizio Istio. Usare i comandi seguenti per `istioctl` installare il file binario del client Istio in una shell basata su PowerShell in Windows.Use the following commands to install the Istio client binary in a PowerShell-based shell on Windows. Questi comandi `istioctl` copiano il file binario del client in una cartella Istio e quindi lo rendono `PATH`disponibile sia immediatamente (nella shell corrente) che in modo permanente (tra i riavvii della shell) tramite il file . Non sono necessari privilegi elevati (amministratore) per eseguire questi comandi e non è necessario riavviare la shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```