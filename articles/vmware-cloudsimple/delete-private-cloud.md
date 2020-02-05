---
title: Eliminare un cloud privato di Azure VMware Solutions (AVS)
description: Viene descritto come eliminare un cloud privato AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024756"
---
# <a name="delete-an-avs-private-cloud"></a>Eliminare un cloud privato AVS

AVS offre la flessibilità necessaria per eliminare un cloud privato AVS. Un cloud privato AVS è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si elimina un cloud privato AVS, verranno eliminati tutti i cluster.

## <a name="before-you-begin"></a>Prima di iniziare

L'eliminazione di un cloud privato AVS Elimina l'intero cloud privato AVS. Tutti i componenti del cloud privato AVS verranno eliminati. Se si vuole mantenere i dati, assicurarsi di aver eseguito il backup dei dati nell'archiviazione locale o in archiviazione di Azure.

I componenti di un cloud privato AVS includono:

* Nodi AVS
* Macchine virtuali
* VLAN/subnet
* Tutti i dati utente archiviati nel cloud privato AVS
* Tutti gli allegati delle regole del firewall in una VLAN/subnet

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Eliminare un cloud privato AVS

1. [Accedere al portale AVS](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** .

3. Fare clic sul cloud privato AVS che si vuole eliminare

4. Nella pagina Riepilogo fare clic su **Elimina**.

    ![Elimina il cloud privato AVS](media/delete-private-cloud.png)

5. Nella pagina Conferma immettere il nome del cloud privato AVS e fare clic su **Elimina**. 

    ![Eliminare AVS private cloud-conferma](media/delete-private-cloud-confirm.png)

Il cloud privato AVS è contrassegnato per l'eliminazione. Il processo di eliminazione viene avviato dopo tre ore ed Elimina il cloud privato AVS.

> [!CAUTION]
> I nodi devono essere eliminati dopo l'eliminazione del cloud privato AVS. La misurazione dei nodi continuerà fino a quando i nodi non verranno eliminati dalla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* [Elimina nodi](delete-nodes.md)
