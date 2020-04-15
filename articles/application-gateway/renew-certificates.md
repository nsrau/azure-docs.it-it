---
title: Rinnovare un certificato del gateway applicazione di Azure
description: Informazioni su come rinnovare un certificato associato a un listener del gateway dell'applicazione.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311953"
---
# <a name="renew-application-gateway-certificates"></a>Rinnovare i certificati del gateway applicazione

A un certo punto, sarà necessario rinnovare i certificati se è stato configurato il gateway applicazione per la crittografia TLS/SSL.

È possibile rinnovare un certificato associato a un listener che usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure:

## <a name="azure-portal"></a>Portale di Azure

Per rinnovare un certificato di listener dal portale, passare ai listener del gateway dell'applicazione. Fare clic sul listener che dispone di un certificato che deve essere rinnovato e quindi fare clic su **Rinnova o modifica il certificato selezionato**.

![Certificato da rinnovare](media/renew-certificate/ssl-cert.png)

Caricare il nuovo certificato PFX, assegnargli un nome, digitare la password e quindi fare clic su **Salva**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per rinnovare il certificato tramite Azure PowerShell, usare lo script seguente:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
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

Per informazioni su come configurare la ripartizione TLS con il gateway applicazione di Azure, vedere [Configurare l'offload TLSTo](application-gateway-ssl-portal.md) learn how to configure TLS Offloading with Azure Application Gateway, see Configure TLS Offload
