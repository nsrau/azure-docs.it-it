---
title: Rinnovare un certificato del gateway applicazione di Azure
description: Informazioni su come rinnovare un certificato associato a un listener del gateway dell'applicazione.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598267"
---
# <a name="renew-application-gateway-certificates"></a>Rinnovare i certificati del gateway applicazione

A un certo punto, è necessario rinnovare i certificati se è stato configurato il gateway dell'applicazione per la crittografia SSL.

È possibile rinnovare un certificato associato a un listener che usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure:

## <a name="azure-portal"></a>Portale di Azure

Per rinnovare un certificato di listener dal portale, passare ai listener del gateway dell'applicazione. Fare clic sul listener che dispone di un certificato che deve essere rinnovato e quindi fare clic su **Rinnova o modifica il certificato selezionato**.

![Certificato da rinnovare](media/renew-certificate/ssl-cert.png)

Caricare il nuovo certificato PFX, assegnargli un nome, digitare la password e quindi fare clic su **Salva**.

## <a name="azure-powershell"></a>Azure PowerShell

Per rinnovare il certificato tramite Azure PowerShell, usare il cmdlet seguente:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```
## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'offload SSL con un gateway applicazione di Azure, vedere [Configurare un gateway applicazione per l'offload SSL con il portale](application-gateway-ssl-portal.md)
