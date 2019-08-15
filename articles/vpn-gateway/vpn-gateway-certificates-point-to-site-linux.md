---
title: 'Generare ed esportare i certificati per le connessioni da punto a sito: Linux: Interfaccia della riga di comando: Azure | Microsoft Docs'
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client usando l'interfaccia della riga di comando Linux (strongSwan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990307"
---
# <a name="generate-and-export-certificates"></a>Generare ed esportare i certificati

Le connessioni da punto a sito usano certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati usando l'interfaccia della riga di comando Linux e strongSwan. Se si cercano diverse istruzioni di certificato, vedere gli articoli [Powershell](vpn-gateway-certificates-point-to-site.md) oppure [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Per informazioni su come installare strongSwan usando l'interfaccia utente grafica anziché l'interfaccia della riga di comando, vedere i passaggi dell'articolo [Configurazione del client](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-and-export"></a>Generare ed esportare
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare con la configurazione da punto a sito [Creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
