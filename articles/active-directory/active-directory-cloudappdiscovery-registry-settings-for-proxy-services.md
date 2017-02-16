---
title: Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy | Documentazione Microsoft
description: Questo argomento illustra tutti i passaggi da eseguire per impostare la porta necessaria sui computer che eseguono l&quot;agente Cloud App Discovery.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 7f2a3c4bf8fda9dd235986be8b0ceaa8eb8313e0


---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy
Per impostazione predefinita, l'agente Cloud App Discovery è configurato solo per l'uso delle porte 80 o 443. Se si prevede di installare Cloud App Discovery in un ambiente con un server proxy che usa una porta personalizzata (non la 80 né la 443), è necessario configurare gli agenti per l'uso di questa porta. La configurazione si basa su una chiave del Registro di sistema.

Questo argomento illustra tutti i passaggi da eseguire per impostare la porta necessaria sui computer che eseguono l'agente Cloud App Discovery.

**Per modificare la porta usata dal computer che esegue l'agente Cloud App Discovery, eseguire la procedura seguente:**

1. Avviare l'Editor del Registro di sistema. <br> ![Esegui](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Passare alla chiave seguente del Registro di sistema oppure crearla:  <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 
3. Creare un nuovo valore **multistringa** denominato **Ports**. ![Nuovo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Per aprire la finestra di dialogo **Modifica multistringhe** , fare doppio clic sul valore Ports.
5. Nella casella di testo Dati valore digitare i valori seguenti e aggiungere tutte le porte personalizzate usate dall'organizzazione:  <br><br>
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

**Risorse aggiuntive**

* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md) 




<!--HONumber=Dec16_HO4-->


