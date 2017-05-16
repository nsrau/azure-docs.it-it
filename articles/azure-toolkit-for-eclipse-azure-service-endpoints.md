---
title: Endpoint del servizio di Azure
description: "Descrive le impostazioni dell’Endpoint del servizio di Azure nel Toolkit di Azure per Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9c6125ec-7278-461e-b69c-ed56e844f742
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9f664b07566fe78d93b1e35a2e0f7e5513960fad
ms.lasthandoff: 01/05/2017


---
# <a name="azure-service-endpoints"></a>Endpoint del servizio di Azure
Gli endpoint del servizio di Azure determinano se l'applicazione è distribuita e gestita dalla piattaforma Azure globale, da Azure gestito da 21Vianet in Cina o da una piattaforma Azure privata. La finestra di dialogo **Endpoint del servizio** consente di specificare quali endpoint del servizio si desidera usare. Per aprire la finestra di dialogo **Service Endpoints** (Endpoint del servizio), in Eclipse fare clic su **Window** (Finestra) e quindi su **Preferences** (Preferenze), espandere **Azure** e infine fare clic su **Service Endpoints** (Endpoint del servizio). L'impostazione del campo **Set attivo** determina quali endpoint del servizio di Azure verranno usati per i progetti di Azure nell'area di lavoro corrente.

Di seguito viene mostrata la finestra di dialogo **Endpoint del servizio** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Per impostare gli endpoint del servizio
Nella finestra di dialogo **Endpoint del servizio** , eseguire una delle seguenti operazioni:

* Se si vuole usare la piattaforma Azure globale, nell'elenco a discesa **Active Set** (Set attivo) selezionare **windowsazure.com** e fare clic su **OK**.

* Se si vuole usare Azure gestito da 21Vianet in Cina, nell'elenco a discesa **Active Set** (Set attivo) selezionare **windowsazure.cn (China)** (windowsazure.cn - Cina) e fare clic su **OK**.

* Se si desidera utilizzare una piattaforma Azure privata:

  1. Fare clic su **Modifica**.

  2. Verrà visualizzata una finestra di dialogo che informa che la finestra di dialogo **Endpoint del servizio** verrà chiusa e che verrà aperto il file dei set di preferenza. Fare clic su **OK**.

  3. Nel file preferencesets.xml, creare un nuovo elemento `preferenceset` . Per questo nuovo elemento, creare gli attributi `name`, `blob`, `management`, `portalURL` e `publishsettings` e aggiungere ad essi valori che corrispondano alla piattaforma Azure privata. È possibile utilizzare i valori forniti dagli elementi esistenti `preferenceset` come modelli. **Nota**: il valore usato per l'attributo `blob` deve contenere il testo "blob" nell'URL.

  4. Salvare e chiudere preferencesets.xml.

  5. Riaprire la finestra di dialogo **Endpoint del servizio** .

  6. Nell'elenco a discesa **Active Set** (Set attivo) selezionare il set attivo creato e fare clic su **OK**.

  7. Dopo aver creato l'elemento `preferenceset` della piattaforma Azure privata, è possibile modificare i valori assegnati facendo clic sul pulsante **Edit** (Modifica) nella finestra di dialogo **Service Endpoints** (Endpoint del servizio). È inoltre possibile creare più elementi `preferenceset` della piattaforma Azure privata, se lo si desidera.

## <a name="see-also"></a>Vedere anche
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse] 

[Creare un'applicazione Hello World per Azure in Eclipse][Creating a Hello World Application for Azure in Eclipse]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

