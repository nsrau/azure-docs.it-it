---
title: 'Configurare i criteri IPsec personalizzati per la rete WAN virtuale di Azure: portale | Microsoft Docs'
description: Informazioni su come configurare i criteri IPsec personalizzati per la rete WAN virtuale di Azure usando il portale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515746"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configurare un criterio IPsec personalizzato per la rete WAN virtuale usando il portale

È possibile configurare i criteri IPsec personalizzati per la rete WAN virtuale nel portale di Azure. I criteri personalizzati sono utili quando si vuole che entrambi i lati (locale e il gateway VPN di Azure) usino le stesse impostazioni per IKE fase 1 e IKE fase 2.

## <a name="working-with-custom-policies"></a>Uso dei criteri personalizzati

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurare i criteri

1. **Individuare l'hub virtuale**. In un browser passare al [portale di Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e accedere con l'account Azure. Individuare l'hub virtuale per il sito.
2. **Selezionare il sito VPN**. Dalla pagina Hub selezionare il sito VPN per cui si desidera configurare un criterio personalizzato.

   ![seleziona](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Modificare la connessione VPN**. Dal **menu di scelta rapida**Selezionare **Modifica connessione VPN**.

   ![Modifica](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configurare le impostazioni**. Nella pagina **Modifica connessione VPN** configurare le impostazioni. Per salvare le impostazioni, fare clic su **Save** (Salva).

   ![configurare e salvare](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).