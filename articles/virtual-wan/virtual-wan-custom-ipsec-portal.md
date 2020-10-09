---
title: 'Configurare i criteri IPsec personalizzati per la rete WAN virtuale di Azure: portale | Microsoft Docs'
description: Informazioni su come configurare i criteri IPsec personalizzati per la rete WAN virtuale di Azure usando il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851175"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configurare un criterio IPsec personalizzato per la rete WAN virtuale usando il portale

È possibile configurare un criterio IPsec personalizzato per una connessione VPN WAN virtuale nel portale di Azure. I criteri personalizzati sono utili quando si vuole che entrambi i lati (locale e il gateway VPN di Azure) usino le stesse impostazioni per IKE fase 1 e IKE fase 2.

## <a name="working-with-custom-policies"></a>Uso dei criteri personalizzati

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurare i criteri

1. **Individuare l'hub virtuale**. In un browser passare al [portale di Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e accedere con l'account Azure. Passare alla risorsa WAN virtuale e individuare l'hub virtuale a cui è connesso il sito VPN.
2. **Selezionare il sito VPN**. Dalla pagina panoramica Hub fare clic su **VPN (da sito a sito)** e selezionare il sito VPN per cui si desidera configurare un criterio IPsec personalizzato.

   ![Proprietà](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Modificare la connessione VPN**. Dal **menu di scelta rapida** **...** Selezionare **Modifica connessione VPN**.

   ![modifica](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configurare le impostazioni**. Nella pagina **Modifica connessione VPN** modificare l'impostazione IPSec da predefinita a personalizzata e personalizzare i criteri IPSec. Per salvare le impostazioni, fare clic su **Save** (Salva).

   ![configurare e salvare](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).