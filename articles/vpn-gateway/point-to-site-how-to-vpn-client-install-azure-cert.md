---
title: 'Gateway VPN di Azure: installare un certificato client da punto a sito'
description: Installare il certificato client per l'autenticazione del certificato di connessione da punto a sito - Windows, Mac e Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: a0cfbc39bbbd54e225879bd479bf0ba47186aee8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987127"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installare i certificati client per connessioni con autenticazione del certificato di connessione da punto a sito

Per tutti i client che si connettono a una rete virtuale usando l'autenticazione del certificato di Azure da punto a sito è necessario un certificato client. Questo articolo illustra come installare un certificato client che verrà usato per l'autenticazione durante la connessione a una rete virtuale con una configurazione da punto a sito.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Acquisire un certificato client

Indipendentemente da quale sistema operativo client ci si vuole connettere, è necessario disporre sempre di un certificato client. È possibile generare un certificato client da un certificato radice generato con una soluzione CA globale (enterprise) oppure da un certificato radice autofirmato. Consultare le istruzioni [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) o [Linux](vpn-gateway-certificates-point-to-site-linux.md) per consultare i passaggi per la generazione di un certificato client. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>I client VPN Mac sono supportati solo per il modello di distribuzione Resource Manager. Non sono supportati per il modello di distribuzione classica.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Il certificato client Linux viene installato nel client come parte della configurazione del client. Consultare la [Configurazione del Client - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) per le istruzioni.

## <a name="next-steps"></a>Passaggi successivi

Continuare con i passaggi di configurazione da punto a sito per [Creare e installare file di configurazione di client VPN](point-to-site-vpn-client-configuration-azure-cert.md).
