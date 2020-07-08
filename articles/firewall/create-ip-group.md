---
title: Creare gruppi IP nel firewall di Azure
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602534"
---
# <a name="create-ip-groups"></a>Creare gruppi IP

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure. Possono avere un solo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

## <a name="create-an-ip-group"></a>Creare un gruppo IP

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Digitare **gruppi IP** nella casella di testo Cerca, quindi selezionare **gruppi di indirizzi IP**.
3. Selezionare **Crea**.
4. Selezionare la propria sottoscrizione.
5. selezionare un gruppo di risorse o crearne uno nuovo.
6. Digitare un nome univoco per il gruppo IP, quindi selezionare un'area.

6. Selezionare **Avanti: indirizzi IP**.
7. Digitare un indirizzo IP, più indirizzi IP o intervalli di indirizzi IP.

   Esistono due modi per immettere gli indirizzi IP:
   - È possibile immetterli manualmente
   - È possibile importarli da un file

   Per importare da un file, selezionare **Importa da un file**. È possibile trascinare il file nella casella oppure selezionare **Cerca file**. Se necessario, è possibile esaminare e modificare gli indirizzi IP caricati.

   Quando si digita un indirizzo IP, il portale lo convalida per verificare la sovrapposizione, i duplicati e i problemi di formattazione.

5. Al termine, selezionare **Verifica + crea**.
6. Selezionare **Crea**.


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui gruppi di indirizzi IP](ip-groups.md)