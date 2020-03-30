---
title: 'Generare ed esportare certificati per Point-to-Site: Linux: CLI'
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client usando l'interfaccia della riga di comando Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779757"
---
# <a name="generate-and-export-certificates"></a>Generare ed esportare i certificati

Le connessioni da punto a sito usano certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati usando l'interfaccia della riga di comando Linux e strongSwan. Se si cercano diverse istruzioni di certificato, vedere gli articoli [Powershell](vpn-gateway-certificates-point-to-site.md) oppure [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Per informazioni su come installare strongSwan usando l'interfaccia utente grafica anziché l'interfaccia della riga di comando, vedere i passaggi dell'articolo [Configurazione del client](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="install-strongswan"></a>Installare strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generare ed esportare i certificati

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare con la configurazione da punto a sito [Creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
