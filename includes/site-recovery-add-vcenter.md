---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908456"
---
In **Aggiungi vCenter** specificare un nome descrittivo per il server vCenter o l'host vSphere e quindi specificare l'indirizzo IP o il nome di dominio completo del server. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa. Selezionare l'account che si deve connettere al server VMware vCenter o vSphere ESXi. Fare clic su **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Se si aggiunge il server VMware vCenter o all'host VMware vSphere con un account che non hanno privilegi di amministratore nel server vCenter o host, assicurarsi che l'account dispone di questi privilegi siano abilitati: Datacenter, Datastore, cartella, Host, rete, Resource, Virtual machine e vSphere Distributed Switch. Per il server VMware vCenter deve essere abilitato anche il privilegio Storage views.
