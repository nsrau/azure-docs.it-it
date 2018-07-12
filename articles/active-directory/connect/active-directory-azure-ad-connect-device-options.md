---
title: 'Azure AD Connect: opzioni del dispositivo | Microsoft Docs'
description: Questo documento descrive nel dettaglio le opzioni del dispositivo disponibili in Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e52f691c75d491897b06a4ebb492d87fda682e38
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917847"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: opzioni del dispositivo

La documentazione seguente offre informazioni sulle diverse opzioni del dispositivo disponibili in Azure AD Connect. È possibile usare Azure AD Connect per configurare le due operazioni seguenti: 
* **Aggiunta ad Azure AD ibrido**: se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi vengono aggiunti sia ad Active Directory locale che ad Azure Active Directory.
* **Writeback dispositivi**: il writeback dispositivi viene usato per abilitare l'accesso condizionale basato su dispositivi ai dispositivi protetti AD FS (2012 R2 o versione successiva)

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurare le opzioni del dispositivo in Azure AD Connect

1.  Eseguire Azure AD Connect. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo**.
    ![Configurare le opzioni del dispositivo](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Quando si fa clic su Avanti, viene visualizzata una pagina **Panoramica** che visualizza i dettagli delle operazioni che è possibile eseguire.
    ![Overview](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Il nuovo Configura le opzioni del dispositivo è disponibile solo nella versione 1.1.819.0 e successive.

2.  Dopo aver specifico le credenziali per Azure AD, è possibile scegliere l'operazione da eseguire nella pagina Opzioni del dispositivo.
    ![Operazioni del dispositivo](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i dispositivi aggiunti all'istanza ibrida di Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurare/Disabilitare il writeback dei dispositivi](./active-directory-aadconnect-feature-device-writeback.md)

