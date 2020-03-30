---
title: Eliminare una soluzione Azure VMware dal cloudSimple Private CloudDelete an Azure VMware Solution by CloudSimple Private Cloud
description: Viene descritto come eliminare un cloud privato CloudSimple.Describes how to delete a CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024756"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Eliminare un cloud privato sempliceDelete a CloudSimple Private Cloud

CloudSimple offre la flessibilità necessaria per eliminare un cloud privato.  Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si elimina un cloud privato, tutti i cluster verranno eliminati.

## <a name="before-you-begin"></a>Prima di iniziare

L'eliminazione di un cloud privato elimina l'intero cloud privato.  Tutti i componenti del cloud privato verranno eliminati.  Se si vuole mantenere i dati, assicurarsi di aver eseguito il backup dei dati nell'archiviazione locale o nell'archiviazione di Azure.If you want to keep any of the data, ensure you're backed the data to on-premises storage or Azure storage.

I componenti di un cloud privato includono:

* Nodi CloudSimple
* Macchine virtuali
* VLAN/subnet
* Tutti i dati utente memorizzati sul cloud privato
* Tutti gli allegati delle regole del firewall a una VLAN/Subnet

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="delete-a-private-cloud"></a>Eliminare un cloud privato

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **Risorse.**

3. Fare clic sul cloud privato che si desidera eliminare

4. Nella pagina di riepilogo fare clic su **Elimina**.

    ![Eliminare il cloud privatoDelete private cloud](media/delete-private-cloud.png)

5. Nella pagina di conferma, immettere il nome del cloud privato e fare clic su **Elimina**. 

    ![Elimina cloud privato - conferma](media/delete-private-cloud-confirm.png)

Il cloud privato è contrassegnato per l'eliminazione.  Il processo di eliminazione inizia dopo tre ore ed elimina il cloud privato.

> [!CAUTION]
> I nodi devono essere eliminati dopo l'eliminazione del cloud privato.  La misurazione dei nodi continuerà fino a quando i nodi non verranno eliminati dalla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* [Eliminare nodi](delete-nodes.md)
