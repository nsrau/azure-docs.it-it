---
title: Creare gruppi IP in Firewall di AzureCreate IP Groups in Azure Firewall
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole di Firewall di Azure.IP Groups allow you to group and manage IP addresses for Azure Firewall rules.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444537"
---
# <a name="create-ip-groups-preview"></a>Crea gruppi IP (anteprima)Create IP Groups (preview)

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole di Firewall di Azure.IP Groups allow you to group and manage IP addresses for Azure Firewall rules. Possono avere un singolo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

## <a name="create-an-ip-group"></a>Creazione di un gruppo IP

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Digitare **Gruppi IP** nella casella di testo di ricerca, quindi selezionare **Gruppi IP**.
3. Selezionare **Crea**.
4. Selezionare la propria sottoscrizione.
5. selezionare un gruppo di risorse o crearne uno nuovo.
6. Digitare un nome univoco per il gruppo IP e quindi selezionare un'area.

6. Selezionare **Successivo: Indirizzi IP**.
7. Digitare un indirizzo IP, più indirizzi IP o intervalli di indirizzi IP.

   Esistono due modi per immettere gli indirizzi IP:
   - È possibile immetterli manualmente
   - È possibile importarli da un file

   Per importare da un file, selezionare **Importa da un file**. È possibile trascinare il file nella casella o selezionare **Cerca file**. Se necessario, è possibile rivedere e modificare gli indirizzi IP caricati.

   Quando si digita un indirizzo IP, il portale lo convalida per verificare la presenza di problemi di sovrapposizione, duplicati e formattazione.

5. Al termine, selezionare **Revisione e Crea**.
6. Selezionare **Crea**.


## <a name="next-steps"></a>Passaggi successivi

- [Ulteriori informazioni sui gruppi IP](ip-groups.md)