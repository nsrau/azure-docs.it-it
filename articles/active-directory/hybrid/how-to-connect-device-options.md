---
title: 'Azure AD Connect: opzioni del dispositivo | Microsoft Docs'
description: Questo documento descrive nel dettaglio le opzioni del dispositivo disponibili in Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: aac0de0e39245835122ede1d99a14ce523bdd8ca
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498285"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opzioni del dispositivo

La documentazione seguente offre informazioni sulle diverse opzioni del dispositivo disponibili in Azure AD Connect. È possibile usare Azure AD Connect per configurare le due operazioni seguenti: 
* **Aggiunta ad Azure AD ibrido**: se l'ambiente ha un footprint AD locale e si vogliono i vantaggi di Azure AD, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi vengono aggiunti sia ad Active Directory locale che ad Azure Active Directory.
* **Writeback dispositivi**: il writeback dispositivi viene usato per abilitare l'accesso condizionale basato su dispositivi ai dispositivi protetti da AD FS (2012 R2 o versioni successive)

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurare le opzioni del dispositivo in Azure AD Connect

1.  Eseguire Azure AD Connect. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo**.  Fare clic su **Avanti**.
    ![Configurare le opzioni del dispositivo](./media/how-to-connect-device-options/deviceoptions.png) 

    Nella pagina **Panoramica** vengono visualizzati i dettagli.
    ![Overview](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Il nuovo Configura le opzioni del dispositivo è disponibile solo nella versione 1.1.819.0 e successive.

2.  Dopo aver specifico le credenziali per Azure AD, è possibile scegliere l'operazione da eseguire nella pagina Opzioni del dispositivo.
    ![Operazioni del dispositivo](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i dispositivi aggiunti all'istanza ibrida di Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurare/Disabilitare il writeback dei dispositivi](how-to-connect-device-writeback.md)

