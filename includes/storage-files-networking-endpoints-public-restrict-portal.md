---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465124"
---
Passare all'account di archiviazione per cui limitare tutto l'accesso all'endpoint pubblico consentendolo solo a specifiche reti virtuali. Nel sommario dell'account di archiviazione selezionare **Firewall e reti virtuali**. 

Nella parte superiore della pagina selezionare il pulsante di opzione **Reti selezionate**. Verranno rese visibili diverse impostazioni nascoste per il controllo della restrizione dell'endpoint pubblico. Fare clic su **+ Aggiungi rete virtuale esistente** per selezionare la rete virtuale specifica a cui deve essere consentito l'accesso all'account di archiviazione tramite l'endpoint pubblico. Sarà necessario selezionare una rete virtuale e una subnet associata. 

Selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** per consentire ai servizi Microsoft attendibili, ad esempio Sincronizzazione file di Azure, di accedere all'account di archiviazione.

[![Screenshot del pannello Firewall e reti virtuali con una specifica rete virtuale autorizzata ad accedere all'account di archiviazione tramite l'endpoint pubblico](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)