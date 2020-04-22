---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736807"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```