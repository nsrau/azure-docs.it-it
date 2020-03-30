---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593725"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Scaricare e installare il file binario del client linkerd Linkerd Linkerd

In una shell basata su `Invoke-WebRequest` PowerShell in Windows, utilizzare per scaricare la versione Linkerd come segue:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Il `linkerd` file binario client viene eseguito nel computer client e consente di interagire con la rete di servizi Linkerd. Utilizzare i comandi seguenti per `linkerd` installare il file binario del client Linkerd in una shell basata su PowerShell in Windows.Use the following commands to install the Linkerd client binary in a PowerShell-based shell on Windows. Questi comandi `linkerd` copiano il file binario del client in una cartella Linkerd e quindi lo rendono `PATH`disponibile sia immediatamente (nella shell corrente) che in modo permanente (tra i riavvii della shell) tramite l'oggetto . Non sono necessari privilegi elevati (amministratore) per eseguire questi comandi e non è necessario riavviare la shell.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```