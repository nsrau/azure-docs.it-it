---
title: Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy | Documentazione Microsoft
description: Questo argomento illustra tutti i passaggi da eseguire per impostare la porta necessaria sui computer che eseguono l'agente Cloud App Discovery.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f633e76ea7c0df456bff41c450eb136809de12a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy
Questo argomento illustra la procedura da seguire per impostare la porta necessaria sui computer che eseguono l'agente Cloud App Discovery. Per impostazione predefinita, l'agente Cloud App Discovery è configurato solo per l'uso delle porte 80 o 443. Se si prevede di installare Cloud App Discovery in un ambiente con un server proxy che usa una porta personalizzata (non la 80 né la 443), è necessario configurare gli agenti per l'uso di questa porta. La configurazione si basa su una chiave del Registro di sistema.

> [!TIP] 
> Osservare i miglioramenti del nuovo Cloud App Discovery senza agenti di Azure Active Directory (Azure AD), ottimizzati dall'[integrazione con Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Modificare la porta usata dal computer che esegue l'agente Cloud App Discovery

1. Avviare l'Editor del Registro di sistema.
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Passare alla chiave del Registro di sistema seguente o crearla: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. Creare un nuovo valore **multistringa** denominato **Ports**. 
  ![Nuovo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Per aprire la finestra di dialogo **Modifica multistringhe**, fare doppio clic sul valore **Porte**.
5. In **Dati valore** digitare i valori seguenti e aggiungere tutte le porte personalizzate usate dall'organizzazione: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Modifica multistringhe](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Fare clic su **OK** per chiudere la finestra di dialogo **Modifica multistringhe**.

## <a name="next-steps"></a>Passaggi successivi

* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md) 

