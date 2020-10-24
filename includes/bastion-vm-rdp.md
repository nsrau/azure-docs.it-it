---
title: includere file
description: includere file
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521535"
---
1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui connettersi e selezionare **Connetti**. Selezionare **Bastion** nell'elenco a discesa.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Selezionare Bastion":::

1. Dopo aver selezionato Bastion nell'elenco a discesa, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Poiché è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Selezionare **Usa Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Selezionare Bastion":::

1. Nella pagina **Connetti tramite Azure Bastion** immettere il nome utente e la password per la macchina virtuale, quindi selezionare **Connetti**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Selezionare Bastion":::

1. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Selezionare Bastion":::
